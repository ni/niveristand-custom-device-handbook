## Selecting a Framework: Classic vs. Express

VeriStand provides two frameworks you can use to create the LabVIEW project for a Custom Device, and both produce fully functional VeriStand Custom Devices. Regardless of which framework you choose, Custom Devices work the same way and the basic concepts remain the same — the framework only affects your starting point, not how the Custom Device ultimately operates. The **Classic** framework is the original, long-established framework; it contains basic boilerplate code and provides greater flexibility over the Custom Device architecture. The **Express** framework is the newer framework; its boilerplate code offers a better starting point by auto-generating some aspects of the Custom Device, at the cost of more constraints on how you design and architect it. The two frameworks differ in how much you author versus how much the framework generates for you, and in the amount of architectural control you retain as a result. Use the details below to decide which framework best fits your project.

### Select Classic when you need control and flexibility

Select the [Classic](classic/index.md) framework when you do **not** need auto-generated Scripting APIs, UI, or LabVIEW clusters that represents the settings, and you want more control over the overall architecture. Classic is the right choice when the following apply to your project.

* **You do not need auto-generated components.** The scripting API, System Explorer UI, and LabVIEW cluster type definitions that represent your settings and channels are not generated for you — and you are comfortable authoring them yourself.
* **You want full control over the architecture.** Classic gives you maximum flexibility in how the UI is created, how settings are set and retrieved, and how the [Custom Device API](https://www.ni.com/docs/en-US/bundle/veristand/page/custom-device-api-library.html) is used within the engine. There are no framework-imposed constraints on where or how you access configuration data.
* **You do not need a standalone test bench.** If you do not require the ability to test and debug the Custom Device engine code in standalone mode — outside of VeriStand — then the additional tooling Express provides offers no benefit for your workflow.

In short, Classic trades convenience for flexibility. You write more code up front, but you are free to structure the Custom Device however your requirements demand.

### Select Express when you want a richer starting template

Select the [Express](express/index.md) framework when you want a better boilerplate template as a starting point and are willing to work within the framework's structure in exchange for the productivity it provides. Express is the right choice when the following apply to your project.

* **Your Custom Device fits a supported execution mode.** Express supports only the [Inline Hardware Interface](Custom_Device_Types.md#inline-hardware-interface) and [Inline-Async Hardware Interface](Custom_Device_Types.md#inline-async-hardware-interface) execution modes. If your Custom Device requires any of the other execution modes — Asynchronous, Inline Model Interface, Inline Timing and Sync, or Asynchronous Timing and Sync — you must use the Classic framework.
* **You want out-of-the-box components.** Express provides a ready-made [System Explorer UI](express/Auto_Generated_UI.md), [scripting APIs](express/Auto_Generated_Scripting_API.md), and LabVIEW clusters that represent your settings and channel groupings — all generated for you from a single XML definition. While these out-of-the-box components boost productivity, they also introduce the following constraints that you should weigh when selecting a framework.

  - **Configuration and channel grouping happen at compile time on the host.** The Custom Device configuration and channel grouping are resolved during compilation on the host side, and the engine only transforms them when needed. Reading configuration and channel information on the engine through the VeriStand Custom Device API — as is traditionally done in Classic Custom Devices — is not recommended.
  - **API access is limited.** You can only access the minimal set of APIs exposed through the template's interface. To use additional APIs, you must extend the template's API class, which requires more effort and makes your Custom Device untestable with the Test Bench.
  - **Channels are accessed through predefined groups.** Channels are accessed using the groups you defined in the input Custom Device type definition XML file. Because the channel groups are represented in a standardized way, you lose the flexibility to group channels on the fly while writing your custom code, and you must plan your channel design more carefully up front.
  - **Manual additions break auto-generation.** The sections, channels, and waveforms you define in the XML file drive the auto-generation of the UI, APIs, and LabVIEW clusters for settings. As a result, any section, channel, or waveform you add manually through the Custom Device API — as is traditionally done — breaks your Custom Device, and you cannot use the auto-generated code without modifications.
  - **The Custom Device is class based.** Express Custom Devices are class based, so you need to understand how to transfer data and configuration between the different RT Driver states by overriding the methods of the class.

* **You want standalone testing and debugging.** Express includes a [Test Bench](express/Test_Bench.md) (the *Custom Device Test Bench* library) that lets you test and debug your engine code in standalone mode, outside of VeriStand, using breakpoints, probes, and logs on your development PC without a real-time target or a full deployment.
* **You are able to plan the Custom Device up front.** Express requires you to plan the Custom Device at the beginning — which sections, channels, and waveform types you need, how they are arranged in the System Explorer hierarchy, and how each setting is accessed in the engine. This plan is captured in the [XML definition](express/XML_Definition_Schema.md) you provide to the wizard when creating the project, and the rest of the template is auto-generated from it.

In exchange for this convenience, Express adds constraints on how you define the Custom Device, where you set and retrieve settings, and how settings are compiled and grouped. Because most of the template is generated from your initial plan, changing the Custom Device structure later means updating the definition and regenerating. For guidance on this planning step, refer to [Planning the Custom Device](express/Planning_The_Custom_Device.md).

### Summary

| Consideration | Classic | Express |
| --- | --- | --- |
| Supported execution modes | All modes | Inline Hardware Interface and Inline-Async Hardware Interface only |
| Auto-generated System Explorer UI | No — author manually | Yes |
| Auto-generated scripting API | No — author manually | Yes |
| Auto-generated LabVIEW clusters for settings and channel groups | No — author manually | Yes |
| Control over architecture, UI, and settings access | Maximum flexibility | Constrained by the framework |
| Standalone engine testing and debugging outside VeriStand | Not provided | Provided |
| Up-front planning required | Minimal | Plan sections, channels, waveforms, hierarchy, and engine access before generating |