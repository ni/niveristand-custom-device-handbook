#### When do you Need a Custom Device?

The built-in components of a VeriStand Project are listed in **[VeriStand Help](https://www.ni.com/documentation/en/veristand/latest/manual/manual-overview/)** » **[VeriStand Environment](https://www.ni.com/documentation/en/veristand/latest/manual/environment/)** » **System Explorer** section in the table. If the built-in components do not fulfill a specification, it can most likely be fulfilled by one of the customization methods shown in **[Using NI VeriStand with Other Software Environments to Create Real-Time Test Applications](https://www.ni.com/en-us/innovations/white-papers/09/using-ni-veristand-with-other-software-environments-to-create-re.html)**.


Although VeriStand provides most of the general functionality required by a real-time testing application, NI has designed the environment so that you can customize and extend it when necessary to ensure it always meets your application requirements. A collection of **[NI supported VeriStand add-ons as well as additional add-ons](https://www.ni.com/en-us/support/documentation/supplemental/15/veristand-add-ons.html)** gathered from internal NI developers and the VeriStand community has been developed and these custom devices may fulfill your specifications.

Several hardware vendors have implemented custom devices for their hardware. You should check with the manufacturer that a custom device doesn’t exist before you build one.
 
In general, there are three specifications that are best-suited for a custom device.
1.	3rd Party Hardware
2.	Unsupported Measurement or Generation Mode
3.	Feature

#### 3rd Party Hardware

A list of hardware natively supported by VeriStand is found in **[VeriStand Help](https://www.ni.com/documentation/en/veristand/latest/manual/manual-overview/)** » **[VeriStand Reference](https://www.ni.com/documentation/en/veristand/latest/manual/veristand-reference/)** » **[NI Hardware Support](https://www.ni.com/documentation/en/veristand/latest/manual/ni-hardware-support/)**. If the application requires other hardware, it can probably be implemented in a custom device.

#### Unsupported Measurement or Generation Mode

Check **[VeriStand Help](https://www.ni.com/documentation/en/veristand/latest/manual/manual-overview/)** » **[Configuring and Running a Project](https://www.ni.com/documentation/en/veristand/latest/manual/configure-run-project/)** » **[Configuring a System Definition File](https://www.ni.com/documentation/en/veristand/latest/manual/configure-system-definition-file/)** » **[Adding and Configuring Hardware Devices](https://www.ni.com/documentation/en/veristand/latest/manual/add-configure-hardware-device/)** to determine if the required measurement or generation mode of your hardware is supported. If not, it can probably be implemented in a custom device. For example, single-point hardware-timed analog acquisition on NI-DAQ devices is supported out-of-the-box. Continuous analog acquisition can be implemented as a custom device.

#### Feature

All of the common functionality necessary for most real-time testing applications such as host interface communication, data logging, stimulus generation, etc, is provided by VeriStand – ready to configure and use. You should first try to meet specifications with the built-in functionality because it is engineered, tested, and supported by National Instruments.

If a required feature is not built-in, it can be implemented by extending VeriStand. See **[Using NI VeriStand with Other Software Environments to Create Real-Time Test Applications](https://www.ni.com/en-us/innovations/white-papers/09/using-ni-veristand-with-other-software-environments-to-create-re.html)** for a complete list of ways to customize and extend VeriStand. Certain features are best implemented as custom devices. To determine when a custom device is the most appropriate mechanism to meet a specification, you should be familiar with all the customization methods available. A general rule-of-thumb is that custom devices implement features that require or use VeriStand channel data on the execution host.

For example, there is a **[TDMS File Viewer](https://www.ni.com/documentation/en/veristand/latest/manual/enhance-workspace-tools/)** tool built into the VeriStand Workspace. If you need to log VeriStand channels to TDMS without first sending it back to the Workspace (as with high-speed streaming), a custom device called the **[Embedded Data Logger](https://www.ni.com/documentation/en/veristand/latest/manual/log-target-data-embedded-data-logger/)** fulfills this requirement. On the other hand, if you need to display previous test results on the workspace while a new test is running, a custom workspace object may be more appropriate than a custom device. See **[Creating Custom Workspace Objects for VeriStand](https://knowledge.ni.com/KnowledgeArticleDetails?id=kA03q000000x4QfCAI&l=en-US)** for more information.

#### Custom Device Risk Analysis

The open nature of VeriStand is a strong advantage over other real-time/HIL testing solutions. It’s easy to take advantage of this extensibility by using custom devices written by other developers. Writing your own custom device incurs a set of manageable risks. This section provides a list of risks that should be considered before custom device development begins.

#### LabVIEW Application Development

Custom devices are written in LabVIEW. The framework generated by the **[NI VeriStand Custom Device Wizard](https://github.com/ni/niveristand-custom-device-wizard)** is single-loop or action-engine VI. This architecture may be suitable for simple custom devices. Non-trivial devices will require more advanced architecture. A requisite for custom device development is thorough knowledge of LabVIEW programming and application architectures. This knowledge represents **[NI Certified LabVIEW Developer](https://education.ni.com/badges/resources/1255)** (CLD) level expertise, and is typically obtained through **[NI's Training and Certification](https://www.ni.com/en-us/shop/services/education-services.html)** program by completing the LabVIEW **[Core 1](https://www.ni.com/en-us/shop/services/education-services/customer-education-courses/labview-core-1-course-overview.html)**, **[Core 2](https://www.ni.com/en-us/shop/services/education-services/customer-education-courses/labview-core-2-course-overview.html)**, and **[Core 3](https://www.ni.com/en-us/shop/services/education-services/customer-education-courses/labview-core-3-course-overview.html)** courses.

It should be mentioned that VeriStand custom devices are typically not large LabVIEW applications. Custom devices are designed to be modular, self-contained plug-ins that add a specific functionality to VeriStand. While custom devices are typically developed by a single programmer, large application development best-practices may still apply. See **[LabVIEW 2018 Help: Best Practices for Large Application Development](https://zone.ni.com/reference/en-XX/help/371361R-01/lvdevconcepts/best_practices_large_apps/)** for more information.

#### LabVIEW Real-Time Application Development

Custom devices are typically designed to execute on real-time systems. This allows the operator to perform deterministic HIL and RT test procedures. Programming for a real-time system requires knowledge of real-time operating systems (RTOS) and specialized LabVIEW development techniques. This knowledge is typically obtained through **[NI's Training and Certification](https://www.ni.com/en-us/shop/services/education-services.html)** program by completing the **[Real-Time Application Development](https://www.ni.com/en-us/shop/services/products/labview-real-time-1-course.html)** course, and it is refined by working on several LabVIEW Real-Time applications.

#### VeriStand Background

Familiarity with the VeriStand Engine is crucial to successful custom device development. The correct type of custom device cannot be selected in the Custom Device Template Tool without understanding the implications of each. This knowledge is typically obtained by reading the **[VeriStand Help](https://www.ni.com/documentation/en/veristand/latest/manual/manual-overview/)**, with an emphasis on **[Understanding the VeriStand Engine](https://www.ni.com/documentation/en/veristand/latest/manual/vs-engine/)**.

Experience with VeriStand from an operator's perspective is highly desired. This experience enables you to build operator-friendly interfaces that conform to the standard look and feel of other VeriStand components. Familiarity with VeriStand allows the developer to build-up a complex system definition, which allows thorough and realistic testing and benchmarking.

#### Hardware Driver Development

The custom device must call a hardware or instrument driver to support 3rd-party hardware. All National Instruments hardware comes with a LabVIEW Application Program Interface (API) that can be used in the custom device. However, just because a LabVIEW API exists does not guarantee the custom device can be easily implemented. Consider the following points when evaluating the feasibility of a custom device for 3rd-party hardware.

1. Does an Instrument Driver exist? See **[Instrument Driver Network](https://www.ni.com/en-us/support/downloads/instrument-drivers.html)** for more information.<br />
2. Is a hardware driver available?<br />
3. Is the driver well documented?<br />
4. Can the hardware requirement be met by passing LabVIEW DBLs to and from the custom device during steady state operation?

VeriStand uses channels to pass data between different parts of the system, including to and from custom devices. All VeriStand channels are LabVIEW double data type (DBL). See **[LabVIEW Help](https://zone.ni.com/reference/en-XX/help/371361R-01/)** » **[Fundamentals](https://zone.ni.com/reference/en-XX/help/371361R-01/TOC5.htm)** » **[Building the Block Diagram](https://zone.ni.com/reference/en-XX/help/371361R-01/TOC12.htm)** » **[How-To](https://zone.ni.com/reference/en-XX/help/371361R-01/TOC14.htm)** » **[Floating Point Numbers](https://zone.ni.com/reference/en-XX/help/371361R-01/lvhowto/floating_point_numbers/)** for more information on LabVIEW data types.
 
If the hardware driver returns a vector, structure, or any non-DBL data, it cannot be passed directly from the custom device to the rest of the VeriStand system. The developer is responsible for coercing the data (or using an alternative communication mechanism) to pass data from the custom device to the rest of the system. For more information on the available communication mechanisms, see **[LabVIEW 2018 Real-Time Module Help](https://zone.ni.com/reference/en-XX/help/370715P-01/)** » **[Real-Time Module Concepts](https://zone.ni.com/reference/en-XX/help/370715P-01/lvrtconcepts/rtmoduleconcepts/)** » **[Sharing Data in Deterministic Applications](https://zone.ni.com/reference/en-XX/help/370715P-01/lvrtconcepts/rtsharedatadeterm/)** » **[Exploring Remote Communication Methods](https://zone.ni.com/reference/en-XX/help/370715P-01/lvrtconcepts/exploring_communication_methods/)**.

VeriStand also exposes its TCP pipe via dynamic event registration. This pipe may suite your remote communication requirements. See the Custom Device Engine Events section for more information.

#### Testing

A custom device is one part of a VeriStand system. The complete state of the operator's system is seldom known by the custom device developer. System state includes the following information.<br />

1. What are the specifications of the Target and Host Computer? Check the **[Glossary](https://www.ni.com/documentation/en/veristand/latest/manual/veristand-glossary/)** section of the **[VeriStand Manual](https://www.ni.com/documentation/en/veristand/latest/manual/manual-overview/)**.<br />
2. What components are in the system definition?<br />
3. How computationally intense are the simulation models?<br />
4. What loop rates are required?<br />
5. What is the health and resource utilization of the system?<br />

Ideally, the custom device is implemented to be minimally burdensome, extremely efficient, and easy to use. Depending on its complexity, it may become necessary to test, debug, and optimize the code on systems representative of the operator’s system.

Consider the following example. A custom device developer needs to benchmark a 3rd-party hardware custom device. He adds the custom device to the Engine Demo example that ships with VeriStand. He deploys the system definition to a quad-core PXIe-8880 Controller. Adding the custom device to the system increased the target’s CPU load by 10% per-core and RAM utilization increased 120KB. If the operator is deploying the same custom device to a single-core PXIe-8821 Controller, with an average CPU load of 60% because of a computationally intense model, it’s unlikely the operator will achieve the same loop rate after adding the custom device. This system may be incapable of running the custom device at all.

Time to test, debug and optimize the code must be factored into the development timeline. If you’re developing for a specific operator, then it's best to test on a system representative of their system. If you’re developing for unknown systems, then it may be appropriate to include the specifications of the system used to obtain benchmarking and timing information with the custom device documentation.
