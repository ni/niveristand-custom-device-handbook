## Choosing Between Classic and Express

VeriStand provides two frameworks you can use to create the LabVIEW project for a Custom Device. The **Classic** framework is the original, long-established framework; it contains basic boilerplate code and provides greater flexibility over the Custom Device architecture. The **Express** framework is the newer framework; its boilerplate code offers a better starting point by providing additional functionality out of the box, at the cost of more constraints on how you design and architect the Custom Device.

Both the **Classic** and **Express** frameworks produce fully functional VeriStand custom devices. They differ in how much you author versus how much the framework generates for you, and in the amount of architectural control you retain as a result. Use the guidance below to decide which framework best fits your project.

### Choose Classic when you need control and flexibility

Choose the [Classic](classic/index.md) framework when you do **not** need auto-generated Scripting APIs, UI, or LabVIEW clusters that represents the settings, and you want more control over the overall architecture. Classic is the right choice when the following apply to your project.

* **You do not need auto-generated components.** The scripting API, System Explorer UI, and LabVIEW cluster type definitions that represent your settings and channels are not generated for you — and you are comfortable authoring them yourself.
* **You want full control over the architecture.** Classic gives you maximum flexibility in how the UI is created, how settings are set and retrieved, and how the [Custom Device API](https://www.ni.com/docs/en-US/bundle/veristand/page/custom-device-api-library.html) is used within the engine. There are no framework-imposed constraints on where or how you access configuration data.
* **You do not need a standalone test bench.** If you do not require the ability to test and debug the custom device engine code in standalone mode — outside of VeriStand — then the additional tooling Express provides offers no benefit for your workflow.

In short, Classic trades convenience for flexibility. You write more code up front, but you are free to structure the Custom Device however your requirements demand.

### Choose Express when you want a richer starting template

Choose the [Express](express/index.md) framework when you want a better boilerplate template as a starting point and are willing to work within the framework's structure in exchange for the productivity it provides. Express is the right choice when the following apply to your project.

* **You want out-of-the-box components.** Express provides a ready-made [System Explorer UI](express/Auto_Generated_UI.md), [scripting APIs](express/Auto_Generated_Scripting_API.md), and LabVIEW clusters that represent your settings and channel groupings — all generated for you from a single XML definition.
* **You want standalone testing and debugging.** Express includes a [Test Bench](express/Test_Bench.md) (the *Custom Device Test Bench* library) that lets you test and debug your engine code in standalone mode, outside of VeriStand, using breakpoints, probes, and logs on your development PC without a real-time target or a full deployment.
* **You are able to plan the device up front.** Express requires you to plan the custom device at the beginning — which sections, channels, and waveform types you need, how they are arranged in the System Explorer hierarchy, and how each setting is accessed in the engine. This plan is captured in the [XML definition](express/XML_Definition_Schema.md) you provide to the wizard when creating the project, and the rest of the template is auto-generated from it.

In exchange for this convenience, Express adds constraints on how you define the Custom Device, where you set and retrieve settings, and how settings are compiled and grouped. Because most of the template is generated from your initial plan, changing the device structure later means updating the definition and regenerating. For guidance on this planning step, refer to [Planning the Custom Device](express/Planning_The_Custom_Device.md).

### Summary

| Consideration | Classic | Express |
| --- | --- | --- |
| Auto-generated System Explorer UI | No — author manually | Yes |
| Auto-generated scripting API | No — author manually | Yes |
| Auto-generated LabVIEW clusters for settings and channel groups | No — author manually | Yes |
| Control over architecture, UI, and settings access | Maximum flexibility | Constrained by the framework |
| Standalone engine testing and debugging outside VeriStand | Not provided | Provided |
| Up-front planning required | Minimal | Plan sections, channels, waveforms, hierarchy, and engine access before generating |