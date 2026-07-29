## Key Concepts

Custom Device development uses a vocabulary of recurring terms. This page defines the fundamental concepts once, so the rest of the handbook can refer back to them. Read this page before the framework-specific chapters ([Classic](classic/index.md) and [Express](express/index.md)); both frameworks build on the same concepts even though they expose them differently.

---

### System Definition (.nivssdf)

The *system definition* is the file that describes an entire VeriStand system — its targets, hardware, models, mappings, and Custom Devices. It is a human-readable XML file with the `.nivssdf` extension. VeriStand stores the state of every Custom Device instance (the value of each control, indicator, and property) in this file. You can also modify it programmatically with the [.NET API](https://www.ni.com/docs/en-US/bundle/veristand/page/veristand-net-reference.html).

### System Explorer

*System Explorer* is the VeriStand configuration environment in which an operator adds, arranges, and configures a system definition. A Custom Device presents its configuration to the operator as a tree of items in the System Explorer configuration pane. For more information, refer to the *VeriStand Help* topic [System Explorer](https://www.ni.com/docs/en-US/bundle/veristand/page/environment.html).

### Sections and hierarchy

VeriStand presents each Custom Device as a *hierarchy* in System Explorer, which lets developers organize and present the Custom Device to the operator. Every item in the tree is a channel, a waveform, or a section. A *section* is an item that groups other items in the hierarchy. The following rules apply:

* You cannot create additional levels of a hierarchy beneath a channel or a waveform.
* You cannot map sections to other items in VeriStand.
* You cannot use sections to exchange data at run time.

### Channels

*Channels* exchange data between the Custom Device and the rest of the VeriStand system. Every channel value is a 64-bit floating point number (LabVIEW `double`); there is no built-in mechanism for other channel data types. A channel has a direction:

* An **output channel** sources data to the rest of the VeriStand system (data the Custom Device produces).
* An **input channel** sinks data from the rest of the system (data the Custom Device consumes).

Once the Custom Device is loaded, the operator maps each input channel to a single data source and each output channel to any number of sinks.

### Waveforms

A *waveform* is a channel-like item whose value is an array of data (`double` or `ComplexDouble`) rather than a single sample. Waveforms let a Custom Device exchange a block of samples per iteration instead of one value at a time.

### Properties

*Properties* store configuration information on a Custom Device item. Unlike channels, a property value can be any standard LabVIEW data type, and property names are case-sensitive strings. Properties are typically used to transfer configuration from the configuration side to the engine when the system definition is deployed. Properties do not inherit: a property must be read from the same item it was set on. Use a property (rather than a channel) for a value that is set at configuration time and does not need to be mapped in the VeriStand system diagram.

### Custom Device items and GUIDs

Every node in a Custom Device hierarchy — the Custom Device itself, each section, each channel, and each waveform — is a *Custom Device item*. Each item has a *Globally Unique IDentifier (GUID)* that VeriStand uses to identify the item's type and to associate it with a page. Overriding an item's default page, for example, is done by assigning it a GUID that the Custom Device XML maps to a page VI.

### Pages

*Pages* are VIs that System Explorer displays in the configuration pane subpanel. When the operator clicks an item in the configuration tree, its page runs on the host computer and defines the appearance and configuration experience for that item. There are a few kinds of page:

* **Main Page** — The page shown when the operator selects the top-level Custom Device item.
* **Default page** — The built-in section or channel page shown when a developer has not assigned a custom page to an item.
* **Extra page** — A developer-authored page that overrides an item's default page to customize its front panel and behavior.

In the Classic framework you author page VIs yourself; in the Express framework the System Explorer UI is [generated automatically](express/Auto_Generated_UI.md) from the XML definition.

### Action VIs

*Action VIs* are VIs that VeriStand calls in response to specific events in the Custom Device's lifecycle, such as loading an item, deleting an item, saving the system definition, or deploying to a target. They let a Custom Device run logic outside of a page — for example, launching a background process on load, preventing deletion of an item, or finalizing target configuration on download. VeriStand provides a set of action VI templates in the [Custom Device API library](https://www.ni.com/docs/en-US/bundle/veristand/page/custom-device-api-library.html).

### Configuration and Engine (RT Driver VI) 

A Custom Device has two sides. The *configuration* side runs on the host computer and defines how the operator adds and configures the Custom Device through System Explorer (initialization VI, pages, action VIs). On the configuration side, the *Initialization VI* is the configuration VI that System Explorer runs whenever the Custom Device is added to the system definition. It builds up the default channel/section list and does not populate the subpanel.

The *engine* side runs on the target inside the [VeriStand Engine](https://www.ni.com/docs/en-US/bundle/veristand/page/vs-engine.html) and defines the Custom Device's run-time behavior. Its behavior is defined by the *RT Driver VI*, which runs after the Custom Device deploys to the execution host and is where the Custom Device does its real work — reading and writing channel data and communicating with hardware. When the engine executes with respect to the rest of the system is determined by its [execution mode](Custom_Device_Types.md). Configuration data reaches the engine through properties stored in the system definition and deployed to the target.
