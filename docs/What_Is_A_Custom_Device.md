### Introduction to Custom Devices


VeriStand is an open software environment you can use to configure real-time testing applications, including hardware-in-the-loop (HIL) systems. With VeriStand, you can complete the following objectives:

* Configure real-time input/output (I/O), stimulus profiles, data logging, alarming, and other tasks; 
* Implement control algorithms or system simulations by importing models from a variety of software environments; 
* Build test system interfaces quickly with a run-time editable user interface complete with ready-to-use tools. 

For more information refer to **[NI Developer Zone Tutorial: What is NI VeriStand](https://www.ni.com/en-us/shop/data-acquisition-and-control/application-software-for-data-acquisition-and-control-category/what-is-veristand.html)**.

You can customize and extend the VeriStand environment with LabVIEW to meet application requirements. This document provides the background, design decisions, and technical information required to understand and develop custom devices in VeriStand. 

Before you begin creating a custom device, you must understand the VeriStand Engine. For more information on the VeriStand Engine refer to VeriStand Help or **[VeriStand Manual](https://www.ni.com/documentation/en/veristand/latest/manual/vs-engine/)**.

#### What is a Custom Device?

While VeriStand provides most of the functionality required by a real-time testing application, the environment can be customized to meet application requirements.  Custom devices are one way to extend VeriStand. For more ways to customize NI VeriStand, refer to **[NI Developer Zone Tutorial: Using LabVIEW and Other Software Environments with NI VeriStand](https://www.ni.com/ro-ro/innovations/white-papers/09/using-ni-veristand-with-other-software-environments-to-create-re.html)**.

Developers can use custom devices to dictate how VeriStand executes. Any LabVIEW callable code can be made into a custom device. Custom devices allow customization to the operator interface within System Explorer. 

Custom devices can display many different configuration experiences. This include simple controls on a VI front panel, pop-up windows and silent routines to scrape the configuration from a database.

A custom device typically consists of two **[VI libraries](https://zone.ni.com/reference/en-XX/help/371361R-01/lvhowto/lv_file_extensions/)** (configuration and engine) and an XML file. The VI Libraries define the behavior of the device. The XML file tells VeriStand how to load, display, use and deploy the device. Custom devices can be created by NI, 3rd parties, and in-house developers. The developer builds the configuration and engine library, and the XML file from **[Source Distributions](https://zone.ni.com/reference/en-XX/help/371361R-01/lvdialog/source_distrib_db/)** in LabVIEW. 

Most custom devices begin as a LabVIEW template project. The latest [niveristand-custom-device-wizard](https://github.com/ni/niveristand-custom-device-wizard) release scripts the template project based on user inputs. You can then modify the template project to fulfill the requirements of the custom device.

A LabVIEW project is needed to build a custom device, but only the configuration library, engine library and XML file are required to use the custom device in VeriStand.

After obtaining (or building himself) the custom device’s libraries, the operator places them in the VeriStand <span style="color:green">*&lt;CommonData&gt;\Custom Devices*</span> directory. This directory location varies with the host operating system.

#### Table of Directories and Aliases:

The following tables list paths to common VeriStand directories by operating system. The heading before each table indicates how NI documentation refers to the directory. For directories with aliases listed, the alias is the text that appears with a relative path in an API or XML file. This text defines the directory that the path is relative to.

```eval_rst
+-------------------------+-------------------------------------------------------------------------+
|<Common Data>            |Alias: To Common Doc Dir                                                 |
+=========================+=========================================================================+
|Windows                  |<Public Documents>\\National Instruments\\NI VeriStand <xxxx>            |
+-------------------------+-------------------------------------------------------------------------+
```

```eval_rst
+-------------------------+-----------------------------------------------------------------------+
|<Application Data>       | Alias: To Application Data Dir                                        |
+=========================+=======================================================================+
|Windows                  |<Application Data>\\National Instruments\\VeriStand                    |
+-------------------------+-----------------------------------------------------------------------+
```
 
```eval_rst
+----------------------------+-------------------------------------------------------+
|<Base>                      | Alias: To Base                                        |
+============================+=======================================================+
|Windows                     |<Program Files>\\National Instruments\\VeriStand <xxxx>|
+----------------------------+-------------------------------------------------------+
```

```eval_rst
+----------------------------------+--------------------------------------------------------------+
|<Custom Device Engine Destination>| Alias: To Base                                               |
+==================================+==============================================================+
|Linux                             |c:\\ni-rt\\NIVeriStand\\Custom Devices\\<custom device name>\\|
+----------------------------------+--------------------------------------------------------------+
```

&nbsp;&nbsp;&nbsp; **Note:** &lt;xxxx&gt; is the  VeriStand version number.

VeriStand parses <span style="color:green">*Common Data\Custom Devices*</span> for custom device XML files when it first launches. You must restart VeriStand to recognize newly added or modified custom device XML files. 

Add the custom device to the system definition in the configuration tree by navigating to **[System Definition](https://zone.ni.com/reference/en-XX/help/372846M-01/veristand/system_explorer/)** » **[Targets](http://zone.ni.com/reference/en-XX/help/372846B-01/veristand/targets_se/)** » **[Controller](https://zone.ni.com/reference/en-XX/help/372846M-01/veristand/cp_controller/)** and right-clicking **[Custom Devices](https://www.ni.com/documentation/en/veristand/latest/manual/add-configure-custom-device/)**. The following image displays how-to add a custom device.

![](images/Picture4.jpg)

Logically, custom devices consist of three parts.

&nbsp;&nbsp;&nbsp; Custom Device Framework<br />
&nbsp;&nbsp;&nbsp; Custom Code<br />
&nbsp;&nbsp;&nbsp; Custom Device XML File<br />

#### Custom Device Framework

The custom device framework consists of type definitions, specifically named controls and indicators, template VIs and a LabVIEW API. Together these items form the rules, or framework, that allows any conforming VI to interact with VeriStand. There are several prebuilt types of custom devices. Almost any requirement can be accomplished by adding or modifying code in one of the prebuilt devices.

The prebuilt devices start with the [niveristand-custom-device-wizard](https://github.com/ni/niveristand-custom-device-wizard).
The developer specifies the type of custom device before running the niveristand-custom-device-wizard. The wizard generates the LabVIEW Project for the new custom device. The exact resources in the project depend on the type of custom device selected.

The project is pre-populated with VIs, LabVIEW Libraries, an XML File, and two build specifications. These resources provide the framework upon which almost all custom devices are built.

VeriStand evolved from NI Dynamic Test Software (NI-DTS). NI-DTS evolved from 3rd party intellectual property (IP) called EASE. The IP made basic provisions for add-on LabVIEW code. This provisions could be considered the first custom device framework on which several “custom devices” were built. If you find a custom device that does not fit the niveristand-custom-device-wizard framework, you may be operating an EASE based custom devices.

For each of the five types of custom devices, in the LabVIEW source project, you’ll see:
* an _NI VeriStand APIs_ virtual folder containing two VI libraries: <span style="color:green">*Custom Device API.lvlib*</span> and <span style="color:green">*Custom Device Utility Library.lvlib*</span>;
* a <span style="color:green">*&lt;Custom Device Name&gt; Engine.lvlib*</span> VI library;
* a <span style="color:green">*&lt;Custom Device Name&gt; Shared.lvlib*</span> VI library;
* a <span style="color:green">*&lt;Custom Device Name&gt; System Explorer.lvlib*</span> VI library.

The following image displays a new custom device template project. <br />

![](images/Picture5.jpg) <br />

The Custom Device API library and Custom Device Utility Library contain most of the type definitions, template VIs and LabVIEW API needed to interact with VeriStand data and timing resources. They allow the VI to behave as a native task in the VeriStand Engine.

&nbsp;&nbsp;&nbsp; **Note:** Some of these VIs also appear on the LabVIEW palette in **[NI VeriStand](https://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/vs_ni_veristand_vis_pal/)** » **[Custom Device API](https://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/vs_custom_device_api_vis_pal/)**.

The API library contains the custom device’s configuration and real-time engine VIs. These correspond to the configuration and engine VI libraries (or LLBs). The front panel and block diagram of these VIs are populated with objects from the Custom Device API libraries.


#### Configuration

The custom device’s configuration defines how the operator adds and configures the custom device through a user interface (UI). The Custom Device Template Tool provides the Initialization VIs for configuration purposes. You can add more VIs during development. 

When a custom device VI’s front panel is presented to the operator in System Explorer, that VI is called a page. Pages are a subset of the VIs that make up a custom device.


#### Initialization VI

The niveristand-custom-device-wizard adds the <span style="color:green">*Initialization VI.vi.*</span> inside the Dinamically Called virtual folder of the &lt;Custom Device Name&gt; System Explorer library. This VI runs in the background when the custom device is first added to the system definition. The initialization page does not run again unless the operator removes and re-adds the custom device.

While you may rename certain objects in the custom device’s LabVIEW Project, it’s important to understand the ramifications of doing so. For example, the Initialization VI is referenced by name in the custom device XML file. This file is generated when you first run the niveristand-custom-device-wizard. If you rename the Initialization VI after running the wizard, you’ll need to manually change the path to the Initialization VI in the custom device XML file.

The Initialization Page runs each time a new instance of the same custom device is added to the system definition. VeriStand retains state information for each instance of a custom device in the **[System Definition](https://zone.ni.com/reference/en-XX/help/372846M-01/veristand/comp_of_project/)** (.**[nivssdf](https://www.ni.com/documentation/en/veristand/latest/manual/configure-system-definition-file/)**) file. State is defined by the value of each control, indicator, and property of the page. The system definition is human-readable XML, so you can open the file with a text editor. 

&nbsp;&nbsp;&nbsp; **Note:** You can use the **[.NET API](https://www.ni.com/documentation/en/veristand/latest/manual/veristand-net-reference/)** to programmatically modify the system definition.

#### Main Page

The niveristand-custom-device-wizard creates Main Page.vi inside Dynamically Called virtual folder of the &lt;Custom Device Name&gt; System Explorer library. After the custom device has been added to the system definition, the Main Page runs whenever the operator clicks on the custom device’s top-level item in the System Explorer configuration tree. The following image displays the top-level item.

![](images/Picture6.png)
<br />

#### Engine

The niveristand-custom-device-wizard creates the <span style="color:green">*RT Driver.vi.*</span> inside the &lt;Custom Device Name&gt; Engine library. This VI defines the behavior of the custom device on the **[Target](https://www.ni.com/documentation/en/veristand/latest/manual/veristand-glossary/)**. The RT Driver VI runs on the Target regardless of the operating system. VeriStand deploys the engine when the operator runs the project from VeriStand or when the system definition is deployed using the VeriStand Execution API.


The engine runs after the custom device deployed to the execution host. You can usually add initialization, steady-state, and shutdown code to the engine template. There aren't any hard boundaries on what code you can put into the engine, but each additional code that is added can increase the size of the engine, and the time required to deploy your system.

Each of the five prebuilt custom devices has a different engine VI. Each engine VI executes at a different time with respect to other VeriStand components. The timing requirements of a custom device, and thus the type of device selected, are functions of when the device needs to execute with respect to other VeriStand Engine components.

Not all requirements can be satisfied by one of the five types of prebuilt custom devices. Some custom devices will require multiple engine libraries, such as (to support different real-time operating systems for example). **[NI VeriStand – Set Custom Device Driver VI](https://zone.ni.com/reference/en-XX/help/372846M-01/veristandmerge/vs_set_custom_device_drivers/)** allows you to programmatically change the driver library for a custom device. 
Some custom devices use the prebuilt template as a launching pad for multiple parallel processes or complex frameworks.
For more information, refer to **Beyond the Template Frameworks**.

#### Custom Code

Custom code performs any functionality desired by the custom device developer. While the initialization and engine frameworks provide access to VeriStand data and timing resources, you must implement the code to meet specification.

For example, custom code can perform a single A/D conversion on a 3rd party digitizer. The framework provides the means for sending the digitized value to the rest of the VeriStand system so that it can be mapped to a channel or used in a stimulus profile.

#### Custom Device XML

Each custom device has an XML file that contains information used by VeriStand to load, configure, display, deploy and run the device. The basic information includes VI and dependency paths, page names, action items, menu items, and meta data for the various pages that make up the custom device.

The niveristand-custom-device-wizard generates an XML file in the template LabVIEW Project. Any properly formatted XML file will be parsed by VeriStand. After the XML file is created by the Custom Device Template Tool, all updates have to be manual.

The custom device XML file does not automatically synchronize with changes to the LabVIEW project. Also, the file does not automatically deploy. You must modify the XML file in the LabVIEW Project directory when making changes. Building the initialization specification overwrites the XML in the <span style="color:green">*&lt;Common Data&gt;\Custom Devices*</span> folder.

The XML file alters the appearance and behavior of the custom device in System Explorer. For example, you can add a right-click menu to a custom device by adding tags to the custom device XML file.

VeriStand parses <span style="color:green">*&lt;Common Data&gt;*</span> for custom devices when it launches. A corrupt custom device XML file can affect the overall VeriStand system. You should exercise care and make a backup of the custom device XML before modifying it.

![](images/Picture7.png)