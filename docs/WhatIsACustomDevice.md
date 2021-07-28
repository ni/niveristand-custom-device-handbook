### Introduction to Custom Devices


NI VeriStand is a ready-to-use, open software environment for configuring real-time testing applications, including hardware-in-the-loop (HIL) test systems. With NI VeriStand, you can configure real-time input/output (IO), stimulus profiles, data logging, alarming, and other tasks; implement control algorithms or system simulations by importing models from a variety of software environments; and build test system interfaces quickly with a run-time editable user interface complete with ready-to-use tools. See **[NI Developer Zone Tutorial: What is NI VeriStand](https://www.ni.com/en-us/shop/data-acquisition-and-control/application-software-for-data-acquisition-and-control-category/what-is-veristand.html)** for more information.

When necessary, you can customize and extend NI VeriStand’s open environment with LabVIEW, ensuring it always meets application requirements. The purpose of this document is to provide the background, design decisions,and technical information required to understand and develop custom devices in NI VeriStand 2020. 

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td>
 Understanding the NI VeriStand Engine is prerequisite to this document. <a href="https://zone.ni.com/reference/en-XX/help/372846B-01/">See NI VeriStand Help</a> » <a href="https://zone.ni.com/reference/en-XX/help/372846B-01/TOC5.htm">Components of a Project</a> » <a href="https://zone.ni.com/reference/en-XX/help/372846B-01/veristand/understanding_vs_engine/">Understanding the VeriStand Engine</a> for more information.
      </td>
 	</tr>
</table>

</body> 
<br /> 

#### What is a Custom Device?

While NI VeriStand provides most of the functionality required by a real-time testing application, NI has designed the environment to be customized and extended when necessary to ensure it always meets application requirements.  Custom devices are one of several ways to customize and extend NI VeriStand.To learn about other ways you can customize NI VeriStand, see **[NI Developer Zone Tutorial: Using LabVIEW and Other Software Environments with NI VeriStand](http://zone.ni.com/devzone/cda/tut/p/id/9366)**.

Custom devices give the developer complete freedom in regards to execution.  Any LabVIEW code, or any code you can call from LabVIEW,can bemade into a custom device.Custom devices give the developer complete freedom to customize the operator interface to within System Explorer.Custom devices may present whatever configuration experience desired by the developers.  From simple controls on a VI front panel, to a company branded pop-up window, to a silent routine that scrapes the configuration from an ActiveX database–the developer defines the configuration experience.

Custom devices typically consist of two **[VI libraries](http://zone.ni.com/reference/en-XX/help/371361G-01/lvhowto/lv_file_extensions/)** (configuration and engine) that define the behavior of the device, and an XML file that tells NI VeriStand how to load, display, use and deploy the device.  Custom devices come from developers including National Instruments, 3rd parties, and in-house developers.  The developer builds the configuration and engine libraries and the XML file from **[Source Distributions](http://zone.ni.com/reference/en-XX/help/371361G-01/lvhowto/build_source_distrib/)** in LabVIEW.The LabVIEW Project for most custom devices starts with a templateproject. A VI called the **[Custom Device Template Tool](http://zone.ni.com/reference/en-XX/help/372846B-01/veristand/creating_custom_dev/)** scripts the template project based on a few inputs from the developer. The developer then adds-to and changes the template project to fulfill the requirements of the custom device.  The Custom Device Template Tool installs on top of NI LabVIEW with the Full and PC versions of NI VeriStand.

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td> The LabVIEW Project is needed to build the custom device, but only the configuration
and engine libraries and the XML file are required to use the custom device in NI VeriStand.      </td>
 	</tr>
</table>

</body> 
<br /> 

After obtaining (or building himself)the custom device’s libraries, the operator places them in the NI VeriStand <CommonData>\Custom Devices directory.  This directory varies with the host operating system.

#### Table of Directories and Aliases:



<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
	<tr>
		<th>&lt;Common Data&gt;</th>
		 <th>Alias: To Common Doc Dir</th>
 	</tr>
 	<tr>
  		 <td>Generic Windows OS</td>
       <td>Public Documents\National Instruments\NI VeriStand 2010</td>
 	</tr>
	<tr>
  		<td>Default Windows XP</td>
   	<td>C:\Documents and Settings\All Users\Shared Documents\National Instruments\NI VeriStand 2010</td>
 	</tr>
	<tr>
  		<td>Default Windows Vista & 7</td>
   	<td>C:\Users\Public\Documents\National Instruments\NI VeriStand 2010</td>
 	</tr>
</table>

</body>
<br /> 

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
	<tr>
		<th>&lt;Application Data&gt;</th>
		 <th>Alias: To Application Data Dir</th>
 	</tr>
 	<tr>
  		 <td><b>Generic Windows OS</b></td>
       <td>Application Data\National Instruments\NI VeriStand 2010</td>
 	</tr>
	<tr>
  		<td>Default Windows XP</td>
   	<td>C:\Documents and Settings\All Users\Shared Documents\National Instruments\NI VeriStand 2010</td>
 	</tr>
	<tr>
  		<td>Default Windows Vista & 7</td>
   	<td>C:\Users\Public\Documents\National Instruments\NI VeriStand 2010</td>
 	</tr>
</table>

</body>
<br /> 


<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
	<tr>
		<th>&lt;Base&gt;</th>
		 <th>Alias: To Base</th>
 	</tr>
 	<tr>
  		 <td>Generic Windows OS</td>
       <td>Program Files\National Instruments\NI VeriStand 2010</td>
 	</tr>
	<tr>
  		<td>Default Windows XP, Vista & 7</td>
   	<td>C:\Program Files\National Instruments\VeriStand 2010</td>
 	</tr>
</table>

</body>
<br />

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
	<tr>
		<th>&lt;Custom Device Engine Destination&gt;</th>
		 <th></th>
 	</tr>
 	<tr>
  		 <td>PharLap / ETX</td>
       <td>C:\ni-rt\veristand\custom devices\&lt;custom device
name&gt;\</td>
 	</tr>
</table>

</body>
<br />



NI VeriStand parses "<Common Data>\Custom Devices" for custom device XML files when it first launches. You must restart NI VeriStand to recognize newly added or modified custom device XML files. The custom device may then be added to the system definition by right- clicking Custom Devices from System Definition » Targets » Controller in the configuration tree.
It’s not necessary for the operator to have any knowledge of LabVIEW or custom device development to use the custom device. It’s not necessary to have the LabVIEW Project to use a custom device. It’s courteous common practice to provide the LabVIEW Project along with the custom device. Providing the project allows operators and other developers to modify the custom device to suit their specific requirements.

![](images/Picture4.jpg)

Figure: Adding a Custom Device to a System Definition

Most custom devices consist of the two VI libraries and XML file mentioned above. Logically, custom devices consist of three parts.
1.	Custom Device Framework
2.	Custom Code
3.	Custom Device XML File

#### Custom Device Framework

The custom device framework consists of type definitions, specifically-named controls and indicators, template VIs and a LabVIEW API. Together these items for the rules, or framework, that allows any conforming VI to interact with NI VeriStand. There are five prebuilt types of custom devices. Almost any requirement can be accomplished by adding or modifying code in one of the five prebuilt devices.
The five prebuilt devices start with the Custom Device Template Tool. The template tool is located in <vi.lib>\ NI Veristand\Custom Device Tools\Custom Device Template Tool\Custom Device Template Tool.vi.
The developer specifies the type of custom device before running the template tool. The tool generates the LabVIEW Project for the new custom device. The exact resources in the project depend on the type of custom device selected.
The project is pre-populated with VIs, LabVIEW Libraries, an XML File, and two build specifications. These resources provide the framework upon which almost all custom devices are built.


<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td>NI VeriStand evolved from NI Dynamic Test Software (NI-DTS). NI-DTS evolved from Intellectual Property (IP) called EASE obtained from a 3rd party. EASE made basic provisions for add-on LabVIEW code. In a sense this was the first custom device framework. Several “custom devices” were built for the original framework, and NI has mutated them from EASE through NI-DTS and into NI-VeriStand. If you come across a
custom device that doesn’t fit into the framework provided by the Custom Device Template Tool, you may have stumbled upon one of the original custom devices.
      </td>
 	</tr>
</table>

</body> 
<br /> 

For each of the five types of custom devices, you’ll see two VI libraries in the LabVIEW source project: Custom Device API.lvlib and Custom Device Name Custom Device.lvlib.

![](images/Picture5.jpg)

The Custom Device API library contains most of the type definitions, template VIs and LabVIEW API needed to interact with NI VeriStand’s data and timing resources. They give a VI the ability to behave as a native task in the NI VeriStand Engine.
Some of these VIs also appear on the LabVIEW palette in NI VeriStand » Custom Device API.

The <custom device name> library contains the custom device’s configuration and RT Engine VIs. These correspond to the configuration and engine VI libraries (or LLBs) mentioned earlier. Notice the front panel and block diagram of these VIs have been populated with objects from the Custom Device API library.


#### Configuration

The custom device’s configuration defines the operator’s experience adding and configuring the custom device. It is the device's operator interface (OI) or user interface (UI). The Custom Device Template Tool provides two VIs for configuration: Initialization and Main. Additional VIs may be added as needed. 
<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td> 
When a custom device VI’s front panel is presented to the operator in the System
Explorer window, that VI is called a page. Pages are a subset of the VIs that make up a custom device.
      </td>
 	</tr>
</table>

</body>     
<br />

#### Initialization VI

The Custom Device Template Tool names the initialization VI <Custom Device Name> Initialization VI.vi. It runs in the background when the custom device is first added to the system definition. The initialization page does not run again unless the operator removes and re-adds the custom device.

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td> 
While you may rename certain objects in the custom device’s LabVIEW Project, it’s important to understand the ramifications of doing so. For example, the Initialization VI is referenced by name in the custom device XML file. This file is generated when you first run the Custom Device Template Tool. If you rename the Initialization VI after running the tool, you’ll need to manually change the path to the Initialization VI in the
custom device XML file.
      </td>
 	</tr>
</table>

</body> 
<br />

The Initialization Page runs each time a new instance of the same custom device is added to the system definition. NI VeriStand retains state information for each instance of a custom device in the System Definition (.nivssdf) file. State is defined by the value of each control, indicator, and property (properties are covered later) of the page. This file is human-readable XML, so you can open the file with a text editor and take a look. There’s also a .NET API for modifying the System Definition programmatically.

#### Main Page

The Custom Device Template Tool names the main page <Custom Device Name> Main Page.vi. After the custom device has been added to the system definition, the main page runs whenever the operator clicks on the on the custom device’s top-level item in System Explorer’s configuration tree.

![](images/Picture6.png)


#### Engine

The Custom Device Template Tool names the engine <Custom Device Name> RT Driver.vi. It defines the behavior of the custom device on the execution host. The RT Driver VI runs on the execution host regardless of the target’s operating system.

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td>
NI VeriStand 2009 did not support the NI VeriStand Engine on VxWorks operating systems. Starting with NI VeriStand 2010, if you want to support VxWorks targets such as Compact RIO, you must compile the engine library for VxWorks. PharLap and
Windows engines do not require additional compilation.
      </td>
 	</tr>
</table>

</body> 
<br />

The engine runs after the custom device has been added to the system definition, configured by the operator, and deployed to the execution host. The developer usually adds initialization, steady-state, and shutdown code to the engine template. There aren’t any hard boundaries on what code you can put into the engine, only on what code you should put in the engine.
NI VeriStand deploys the engine when the operator clicks Run Project from the NI VeriStand Getting Started Window, selects Operate » Run or Operate » Deploy from the Project Explorer, or when the system definition is deployed using the NI VeriStand Execution API.
Each of the five prebuilt custom devices has a different engine VI. Each engine VI executes at a different time with respect to other NI VeriStand components. The timing requirements of a custom device, and thus the type of device selected, are functions of when the device needs to execute with respect to other NI VeriStand Engine components. We’ll cover this in detail later on.
Not all requirements can be satisfied by one of the five types of prebuilt custom devices. Some custom devices require multiple engine libraries (to support different real-time operating systems for example). NI VeriStand – Set Custom Device Driver VI allows you to programmatically change the driver library for a custom device. Some custom devices use the prebuilt template as a launching pad for multiple parallel processes or complex frameworks.
See the section Beyond the Template Frameworks for more information. Again, custom devices give the developer complete freedom with regard to OI/UI and execution.
#### Custom Code

The custom code performs any functionality desired by the custom device developer. While the initialization and engine frameworks provide access to NI VeriStand data and timing resources, it's up to the developer to implement the code to meet specification.
For example, the custom code might perform a single A/D conversion on a 3rd party digitizer. The framework provides the means for sending the digitized value to the rest of the NI VeriStand system so it can be mapped to channels, used in a stimulus profile, etc. Again, there aren’t any hard boundaries on the code you can put into the driver.

#### Custom Device XML

Each custom device has an XML file that contains information used by NI VeriStand to load, configure, display, deploy and run the device. The basic information includes VI and dependency paths, page names, action and menu items, and Meta data for the various pages that make up the custom device. The Custom Device Template Tool generates an XML file for
 
you and include it in the template LabVIEW Project. Any properly-formatted XML file will be parsed by NI VeriStand. After the XML file is created by the Custom Device Template Tool, all edits to it are manual, i.e. it is not automatically updated to reflect changes made by the developer.

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td>
The custom device XML does not automatically synchronize with changes to the LabVIEW project, nor does it automatically deploy. Be sure to modify the XML in the LabVIEW Project directory when making changes. Building the Initialization
specification overwrites the XML in the <Common Data>\Custom Devices folder.
      </td>
 	</tr>
</table>

</body> 
<br />

The XML file provides the ability to customize the appearance and behavior of the custom device in System Explorer. For example, you can change the default glyph or add a right-click menu to a custom device by adding tags to the custom device XML file.

<head>
<style>
table, th, td {
  border: 1px solid black;
}
</style>
</head>
<body>

<table>
   <tr>
		<td>
Since NI VeriStand parses <Common Data> for custom devices when it launches, a
corrupt custom device XML file can affect the overall NI VeriStand system. You should exercise care and make a backup of the custom device XML before modifying it.
      </td>
 	</tr>
</table>

</body>
<br />

![](images/Picture7.png)