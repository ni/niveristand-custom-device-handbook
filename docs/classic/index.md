# Custom Device Classic

The **Classic** framework is the original VeriStand custom device development framework. The developer authors the configuration UI, scripting API, and orchestration code manually in LabVIEW, giving maximum flexibility over the device architecture.

If you are new to custom devices, first review [Choosing Between Classic and Express](../Choosing_A_Framework.md) to confirm Classic is the right framework for your project.

Classic framework provides the LabVIEW source project that contains the following.
* An NI VeriStand APIs virtual folder containing two VI libraries called *Custom Device API.lvlib* and *Custom Device Utility Library.lvlib*.
* A *&lt;Custom Device Name&gt;* VI library.
* A *&lt;Custom Device Name&gt; Shared.lvlib* VI library.
* A *&lt;Custom Device Name&gt; System Explorer.lvlib* VI library.

The following image displays a new custom device template project.

![](images/Picture5.jpg)

The Custom Device API library and Custom Device Utility Library contain most of the type definitions, template VIs and LabVIEW API needed to interact with VeriStand data and timing resources. They allow the VI to behave as a native task in the VeriStand Engine.

**Note:** Some of these VIs also appear on the LabVIEW palette in **NI VeriStand** » **[Custom Device API](https://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/vs_custom_device_api_vis_pal/)**.

The API library contains the custom device’s configuration and real-time engine VIs. These correspond to the configuration and engine VI libraries. The front panel and block diagram of these VIs are populated with objects from the Custom Device API libraries.


#### Configuration

The custom device configuration defines how the operator adds and configures the custom device through a user interface (UI). The Custom Device Template Tool provides the Initialization VIs for configuration purposes. You can add more VIs during development.

When a custom device VI’s front panel is presented to the operator in System Explorer, that VI is called a page. Pages are a subset of the VIs that make up a custom device.


#### Initialization VI

The niveristand-custom-device-wizard adds the *Initialization VI.vi.* inside the Dynamically Called virtual folder of the &lt;Custom Device Name&gt; System Explorer library. This VI runs in the background when the custom device is first added to the system definition. The Initialization VI does not run again unless the operator removes and re-adds the custom device.

While you may rename certain objects in the custom device’s LabVIEW Project, it’s important to understand the ramifications of doing so. For example, the Initialization VI is referenced by name in the custom device XML file.

This file is generated when you first run the niveristand-custom-device-wizard. If you rename the Initialization VI after running the wizard, you’ll need to manually change the path to the Initialization VI in the custom device XML file.

The Initialization VI runs each time a new instance of the same custom device is added to the system definition. VeriStand retains state information for each instance of a custom device in the system definition (.[nivssdf](https://www.ni.com/docs/en-US/bundle/veristand/page/configure-system-definition-file.html)) file.

State is defined by the value of each control, indicator, and property of the page. The system definition is human-readable XML, so you can open the file with a text editor.

**Note:** You can use the [.NET API](https://www.ni.com/docs/en-US/bundle/veristand/page/veristand-net-reference.html) to programmatically modify the system definition.

#### Main Page

The niveristand-custom-device-wizard creates Main Page.vi inside Dynamically Called virtual folder of the *&lt;Custom Device Name&gt;* System Explorer library. After the custom device has been added to the system definition, the Main Page runs whenever the operator clicks on the custom device’s top-level item in the System Explorer configuration tree. The following image displays the top-level item.

![](images/Picture6.png)
<br />

#### Engine

The niveristand-custom-device-wizard creates the *RT Driver.vi.* inside the *&lt;Custom Device Name&gt;* Engine library. This VI defines the behavior of the custom device on the [Target](https://www.ni.com/docs/en-US/bundle/veristand/page/veristand-glossary.html).

The RT Driver VI runs on the Target regardless of the operating system. VeriStand deploys the engine when the operator runs the project from VeriStand or when the system definition is deployed using the VeriStand Execution API.

The engine runs after the custom device deployed to the execution host. You can usually add initialization, steady-state, and shutdown code to the engine template. There aren't any hard boundaries on what code you can put into the engine, but each additional code that is added can increase the size of the engine, and the time required to deploy your system.

Each of the five prebuilt custom devices has a different engine VI. Each engine VI executes at a different time with respect to other VeriStand components. The timing requirements of a custom device, and thus the type of device selected, are functions of when the device needs to execute with respect to other VeriStand Engine components.

Not all requirements can be satisfied by one of the five types of prebuilt custom devices. Some custom devices will require multiple engine libraries. For example, a device may need to support different real-time operating systems. The [NI VeriStand – Set Custom Device Driver VI](https://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/vs_set_custom_device_drivers/) allows you to programmatically change the driver library for a custom device.

Some custom devices use the prebuilt template as a launching pad for multiple parallel processes or complex frameworks. For more information, refer to **Beyond the Template Frameworks**.

For more details on the Classic framework, refer to the following sections.

+ [Planning the Custom Device](Planning_The_Custom_Device.md)

+ [Implementing the Custom Device](Implement_the_Custom_Device.md)

+ [Distributing the Custom Device](Distributing_the_Custom_Device.md)

+ [Scripting APIs for Custom Devices](Scripting_API.md)

+ [Migrating a Custom Device from LLB to PPL](Migrating_LLB_to_a_PPL_based_Custom_Device.md)

+ [LabVIEW Debugging Techniques](LabVIEW_Debugging_Techniques.md)

+ [Custom Device Tips & Tricks](Custom_Device_Tips_and_Tricks.md)
