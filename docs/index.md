

# Veristand Custom Device Handbook


* About

* Content

* Introduction

* Basic information


### Conventions
This document uses the following formatting and typographical conventions.

<>	Angle brackets that contain numbers separated by an ellipsis represent a range of values associated with a bit or signal name—for example, AO <0..3>.

»	The » symbol leads you through nested menu items and dialog box options to a final action. The sequence File » Page Setup » Options directs you to pull down the File menu, select the Page Setup item, and select Options from the last dialog box.

![](images/Picture1.png) This icon denotes a tip, which alerts you to advisory information.

 	
![](images/Picture2.png) This icon denotes a note, which alerts you to important information.

 	
![](images/Picture3.png) This icon denotes a caution, which advises you of precautions to take to avoid injury, data loss, or a system crash.

**bold**	Bold text denotes items that you must select or click in the software, such as menu items and dialog box options. Bold text also denotes parameter names, controls and indicators on the front panel, dialog boxes, sections of dialog boxes, menu names, and palette names.

<span style="color:green">*green*</span>	Underlined text in this color denotes a link to a help topic, help file, or Web address.

<span style="color:purple">*purple*</span>	Underlined text in this color denotes a visited link to a help topic, help file, or Web address.

italic	Italic text denotes variables, emphasis, cross-references, or an introduction to a key concept. Italic text also denotes text that is a placeholder for a word or value that you must supply.

monospace	Text in this font denotes text or characters that you should enter from the keyboard, sections of code, programming examples, and syntax examples. This font is also used for the proper names of disk drives, paths, directories,
programs, subprograms, subroutines, device names, operations, variables, filenames, and extensions.



## Indices and tables

### Introduction


NI VeriStand is a ready-to-use, open software environment for configuring real-time testing applications, including hardware-in-the-loop (HIL) test systems. With NI VeriStand, you can configure real-time input/output (IO), stimulus profiles, data logging, alarming, and other tasks; implement control algorithms or system simulations by importing models from a variety of software environments; and build test system interfaces quickly with a run-time editable user interface complete with ready-to-use tools. See <font color='green'>[NI Developer Zone Tutorial: What is NI VeriStand](https://www.ni.com/en-us/shop/data-acquisition-and-control/application-software-for-data-acquisition-and-control-category/what-is-veristand.html)</font> for more information.

When necessary, you can customize and extend NI VeriStand’s open environment with LabVIEW, ensuring it always meets application requirements. The purpose of this document is to provide the background, design decisions,and technical information required to understand and develop custom devices in NI VeriStand 2020. 

![](images/Picture2.png) Understanding the NI VeriStand Engine is prerequisite to this document.  **[See NI VeriStand Help](https://zone.ni.com/reference/en-XX/help/372846B-01/)** » **[Components of a Project](https://zone.ni.com/reference/en-XX/help/372846B-01/TOC5.htm)** » **[Understanding the VeriStand Engine](https://zone.ni.com/reference/en-XX/help/372846B-01/veristand/understanding_vs_engine/)** for more information.


#### What is a Custom Device?

While NI VeriStand provides most of the functionality required by a real-time testing application, NI has designed the environment to be customized and extended when necessary to ensure it always meets application requirements.  Custom devices are one of several ways to customize and extend NI VeriStand.To learn about other ways you can customize NI VeriStand, see NI Developer Zone Tutorial: Using LabVIEW and Other Software Environments with NI VeriStand.

Custom devices give the developer complete freedom in regards to execution.  Any LabVIEW code, or any code you can call from LabVIEW,can bemade into a custom device.Custom devices give the developer complete freedom to customize the operator interface to within System Explorer.Custom devices may present whatever configuration experience desired by the developers.  From simple controls on a VI front panel, to a company branded pop-up window, to a silent routine that scrapes the configuration from an ActiveX database–the developer defines the configuration experience.

Custom devices typically consist of two VI libraries(configuration and engine) that define the behavior of the device, and an XML file that tells NI VeriStand how to load, display, use and deploy the device.  Custom devices come from developers including National Instruments, 3rd parties, and in-house developers.  The developer builds the configuration and engine libraries and the XML file from Source Distributions in LabVIEW.The LabVIEW Project for most custom devices starts with a templateproject.  A VI called the Custom Device Template Tool scripts the template project based on a few inputs from the developer. The developer then adds-to and changes the template project to fulfill the requirements of the custom device.  The Custom Device Template Tool installs on top of NI LabVIEW with the Full and PC versions of NI VeriStand.



After obtaining (or building himself)the custom device’s libraries, the operator places them in the NI VeriStand <CommonData>\Custom Devices directory.  This directory varies with the host operating system.

#### Table of Directories and Aliases<Common Data>Alias:




![Table]
First Header | Second Header | Third Header
Content Cell | Content Cell  | Content Cell
Content Cell | Content Cell  | Content Cell



To Common Doc DirGeneric Windows OS<Public Documents>\National Instruments\NI VeriStand 2010Default Windows XPC:\Documents and Settings\All Users\Shared Documents\National Instruments\NI VeriStand2010Default Windows Vista & 7C:\Users\Public\Documents\National Instruments\NI VeriStand 2010<Application Data>Alias: To Application Data DirGeneric Windows OS<Application Data>\National Instruments\NI VeriStand 2010Default Windows XPC:\Documents and Settings\All Users\Application Data\National Instruments\NI VeriStand 2010Default Windows Vista & 7C:\ProgramData\National Instruments\NI VeriStand 2010<Base>Alias: To BaseGeneric Windows OS<Program Files>\National Instruments\NI VeriStand 2010Default Windows XP, Vista & 7C:\Program Files\National Instruments\VeriStand 2010<Custom Device Engine Destination>PharLap / ETXC:\ni-rt\veristand\custom devices\<custom device name>\NI VeriStand parses <Common Data>\Custom Devices for custom deviceXML fileswhen it first launches.  Youmust restart NI VeriStand to recognize newly added or modified custom device XML files.The custom device may thenbe added to the system definition by right-clickingCustom DevicesfromSystem Definition» Targets» Controllerin the configuration tree.It’s not necessary for the operator to have any knowledge of LabVIEW or custom device development to use the custom device.  It’s not necessary to have the LabVIEW Project to use 
Custom Device Developer’s Guide© 2010 National Instruments9of 85a custom device.  It’s courteous common practice to provide the LabVIEW Project along with the custom device.Providing the project allows operators and other developersto modify the custom device to suit their specific requirements.Figure: Adding a Custom Device to a System DefinitionMost customdevices consist of thetwo VI libraries and XML filementioned above.  Logically, custom devices consist of three parts.1.Custom Device Framework2.Custom Code3.Custom Device XML File

