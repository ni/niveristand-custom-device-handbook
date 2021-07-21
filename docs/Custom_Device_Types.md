# Custom Device Types

The type of a custom device refers to its execution mode, which defines how the device interacts with the VeriStand Engine.

Depending on your requirements, you can create custom devices that run inline or in parallel with the Primary Control Loop (PCL). You also can create timing and sync devices.
spd-note-note
Note  

Timing and sync devices are the same as regular custom devices, but you can configure them as the hardware synchronization master to drive RTSI0. They appear in System Explorer under Hardware»Chassis»Timing and Sync.

The following table displays the pre-defined custom device types that are included in VeriStand.
Device type 	Basic architecture 	Data interface 	Timing 	Benefits 	Caveats 	Use cases
Asynchronous 	Two loops, one for receiving commands and one for data transfer 	FIFOs 	Variable 	Unlikely to affect timing of the rest of the VeriStand system, can run faster or slower than the PCL 	1-cycle latency due to FIFOs 	Shared resources, background processes, non-deterministic hardware/protocols, system health monitoring, logging, offline analysis
Inline Hardware Interface 	State machine with two-phase execution 	Channel references 	Inline 	Sends data to engine before other components execute, receives data from engine after other components execute 	Can adversely affect PCL timing 	Most hardware, deterministic operation, two-phase operations (for example, stimulus-response)
Inline Model Interface 	State machine with one-phase execution 	Channel references 	Inline 	Sends data to engine with low latency 	Can adversely affect PCL timing 	Low latency calculations, such as PID, interpolation, and so on
Inline Timing and Sync 	State machine with two-phase execution 	Channel references 	Inline 	Same as Inline Hardware Interface, can function as hardware synchronization master device to drive RTSI 0 line 	Can adversely affect PCL timing 	Inline hardware synchronization master device
Asynchronous Timing and Sync 	Single loop 	FIFOs 	Variable 	Same as Asynchronous, but can function as hardware synchronization master device to drive RTSI 0 line 	1-cycle latency due to FIFOs 	Asynchronous hardware synchronization master device
spd-note-note
Note  

Both inline and asynchronous custom devices have advantages and limitations. Consider launching asynchronous loop(s) within an inline custom device to take advantage of the best features of both while overcoming many of their limitations.

## Custom Device Type Selection

Choose the type of custom device that most closely resembles the functionality you want to configure. For example, if you want to read and write data to and from a hardware device at the same rate as the PCL, select an inline hardware interface custom device type. However, if you have a serial hardware device, you should select an asynchronous custom device, as serial devices are slow and could affect the PCL timing. Understanding the execution steps of the PCL also can help you select the appropriate custom device type.
spd-note-note
Note  

Consider using an inline mode or hardware interface custom device and, if an asynchronous loop is required, launch an asynchronous loop within that device. Inline model and hardware interface custom devices give you more control and allow you to access channels outside of the custom device.

## Custom Device Type Configuration

You configure the custom device type by selecting the appropriate template VI from the Custom Device API library. The type of custom device you select will determine the architecture of the RT Driver VI, but the VIs for configuring the custom device remain the same across all types.

A custom device is not limited to a single device type, and you can alter the code in the template VIs to fit your needs. For example, you can create both inline and asynchronous engines for the same custom device, and use the Set Custom Device Drivers VI to switch between them. However, you must maintain the connector pane, controls, and indicators that the tool and templates include to ensure correct interaction with VeriStand.
