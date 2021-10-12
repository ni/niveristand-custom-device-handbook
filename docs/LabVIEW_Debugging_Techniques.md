### Debugging and Benchmarking

Use LabVIEW and VeriStand to **[debug and benchmark custom devices](https://www.ni.com/documentation/en/veristand/latest/manual/custom-device-benchmark-debug/)** as you would any other code during development.

#### LabVIEW Debugging Techniques

Custom devices are written in LabVIEW code. You can develop, test, and debug this code in LabVIEW before running the niveristand-custom-device-wizard. Use built-in [LabVIEW debugging techniques](https://www.ni.com/en-us/support/documentation/supplemental/12/debugging-techniques-in-labview.html) during development and merge your resulting LabVIEW code into the custom device framework.

A custom device is one of many parts of the system definition. Behavior of LabVIEW code within the custom device framework, such as timing, will likely differ from a stand-alone LabVIEW application. You should benchmark the custom device inside of the VeriStand Engine.

Once custom devices are added to the system definition, they are fully integrated into the VeriStand context. Built-in LabVIEW debugging techniques will no longer be available.

#### Console Viewer

The [Console Viewer](https://www.ni.com/documentation/en/veristand/latest/manual/viewing-console-output/) is a subcomponent of the VeriStand Real-Time (RT) Engine.

You can install the Console Viewer to the target with NI Measurement and Automation Explorer (MAX). Once installed, the component runs a small UDP daemon that allows the operator to view the console. You can access the Console Viewer from the **VeriStand Editor** » **Tool Launcher** » **View Console**.

**Note:** You cannot use the Console Viewer on NI Linux Real-Time targets. Instead, connect your NI Linux Real-Time targets to a computer using a serial port to view the output.

The Console Viewer provides a periodic snapshot of the system definition and resulting CPU usage. The viewer can also display debugging messages.

![](images/ConsoleViewer1.jpg)

CPU spikes and transients may not be observable. If the system is busy, the Console Viewer may not update. You can use other debugging methods for a more accurate indication of resource utilization.

The Console Viewer is also available as a stand-alone add-on to LabVIEW Real-Time. For more information, refer to [Remotely View Console Output of Real-Time Targets](https://knowledge.ni.com/KnowledgeArticleDetails?id=kA03q000000x4TjCAI&l=en-US).

#### Custom Error Codes

You can define custom error codes in LabVIEW and distribute them to VeriStand with a custom device.
1. Copy a custom *errors.txt* file to VeriStand in the `<Base&gt>\National Instruments\Shared\Errors\English` directory.
1. Add the file as a dependency in the custom device.
1. Add the file as a dependency in the [custom device XML file](https://www.ni.com/documentation/en/veristand/latest/manual/custom-device-xml/).
1. **(Optional)** For RT targets, deploy the *errors.txt* file to the error directory on target. Error messages will display in Console Viewer.

For more information, refer to [Defining Custom Error Codes to Distribute throughout Your Application](https://zone.ni.com/reference/en-XX/help/371361R-01/lvhowto/def_custom_error_text/).

#### Printing With the Print Debug Line VI
The recommended method of printing to the console is to use the NI VeriStand - Print Debug Line VI. To locate this VI in LabVIEW, navigate to **Custom Device API** » **Utilities**.

| VI     | Description |
| ----------- | ----------- |
| ![](images/NIVSdebugString.jpg) | Works on both Windows and RT execution hosts. <br><br> It has an optional **Attribute** input to change the color of the text. It also has an optional **Add to log file? (F)** input to append the string to the VeriStand log file. |

#### Printing with RT Debug String VI

The RT Debug String VI sends a string to the standard output device. To locate this VI in LabVIEW, navigate to **Real-Time** » **RT Utilities**.

| VI     | Description |
| ----------- | ----------- |
| ![](images/RT_Debug_String_VI.jpg) | By default, sends the debug string to the video port. If you have a device capable of serial redirection, this VI sends the debug string to the serial port.|

#### Telemetry Custom Device

The [Telemetry Custom Device](https://github.com/ni/niveristand-telemetry-custom-device/releases) supports VeriStand benchmarking by logging system channels and monitoring target resources. Usage data is logged to a TDMS file on the target that is running the VeriStand Engine.

#### System Channels

VeriStand includes [system channels](https://www.ni.com/documentation/en/veristand/latest/manual/system-channels/) that provide information on internal processes. Several of these system channels are useful for benchmarking and debugging.

The following table contains these debugging and benchmarking system channels.

| System Channel | Description |
| ----------- | ----------- |
| HP Count  | The number of times the Primary Control Loop reported being late. |
| HP Loop Duration | The duration of the Primary Control Loop in nanoseconds. |
| LP Count | The number of times the Data Processing Loop reported being late. |
| Model Count | The number of times the models have not completed their execution in time. |

If the value of the count channels increase over time, the execution host is not achieving the desired loop rates. You can use the system channels in conjunction with an **[alarm](https://www.ni.com/documentation/en/veristand/latest/manual/add-configure-alarm/)** or **[procedure](https://www.ni.com/documentation/en/veristand/latest/manual/add-configure-procedure/)** to handle events.

#### System Monitor Custom Device

The **[System Monitor Custom Device](https://github.com/ni/niveristand-system-monitor-custom-device/releases)** tracks memory resources and CPU usage on an RT target running the VeriStand Engine. Set the update rate (Hz) in System Explorer to determine how often the custom device checks CPU and memory usage and sends them to the corresponding channel.

**Note:** The VeriStand System Monitor can only be used on an RT target.

#### Distributed System Manager

You can use the NI Distributed System Manager (DSM) to monitor the CPU and memory resources of an RT target. You must install System State Publisher on the RT target.

This component runs a small daemon that publishes the system state to DSM. For more information, refer to [Monitor RT target resources](https://zone.ni.com/reference/en-XX/help/372572E-01/sysman/monitoring_resources/).

System State Publisher provides a periodic snapshot of utilization. CPU spikes and transients may not be observable. If the system is busy, DSM may not update. You can use other debugging methods for a more accurate indication of resource utilization.

#### Real-Time Trace Viewer

VeriStand provides built-in support for the [Real-Time Trace Viewer](https://zone.ni.com/reference/en-XX/help/370715P-01/lvtracehelp/lv_tracetoolkit_help/) and [Real-Time Trace Viewer VIs](https://zone.ni.com/reference/en-XX/help/370715P-01/lvtrace/tracetoolkitvis_pal/). Use the trace viewer to capture the timing and execution data of a VI and thread events for applications running on an RT target.

to display the Real-Time Trace Viewer In a LabVIEW VI, select **Tools** » **Real-Time Module** » **Trace Viewer**. For more info on how to use Real-Time Trace Viewer in LabVIEW, navigate to **Help** » **LabVIEW Help** » **Real-Time Module** » **Real-Time Trace Viewer**.

#### Additional Debugging Options for VeriStand
Upon request, NI can provide advanced debugging tools to help you resolve certain custom device issues. These tools are a last resort when all other debugging options have been exhausted. For more information, contact NI.
