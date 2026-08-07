## Custom Device Express Framework Overview

This section explains how the Express framework turns a Custom Device definition XML into a complete, deployable Custom Device — the end-to-end development workflow, what the framework generates for you, and the key concepts you need to understand before you start [Implementing the Custom Device](Implementing_the_Custom_Device.md).

---

### The Express framework development workflow

```
1. Plan  ──► Design Custom Device hierarchy, channels, waveforms, sections and properties 
               │
               ▼
2. Define ──► Author the Custom Device Definition XML
              (use the wizard's built-in XML Editor)
               │
               ▼
3. Generate ──► Run the Express Wizard
               Creates the LabVIEW project + C# scripting API project
               Builds the scripting API DLL, LabVIEW PPLs
               Opens the LabVIEW project
               │
               ▼
4. Implement ──► Add Custom Device specific logic to the user-editable engine VIs
               │
               ▼
5. Build ──► Build the LabVIEW project to produce the finished Custom Device Builds folder
               │
               ▼
6. Deploy ──► Copy the finished Custom Device folder present inside Builds folder to the VeriStand Custom Devices directory
```

---

### What the framework generates

The Express framework produces four categories of output, all driven by the [XML definition](XML_Definition_Schema.md).

#### System Explorer UI

Every property you declare in the XML becomes a typed control in System Explorer Pages of Custom Device.

An optional `GUI Layout.yaml` file lets you arrange controls into named sections, apply conditional visibility/editability rules, and control column layout.

#### Scripting API

The wizard calls `api-gen.exe` tool, that translates the XML definition into a C# project containing a strongly typed class for each Custom Device, section, channel, and waveform type you declared. Wizard then Builds that project and produces the scripting API assembly that developers can use to create and configure Custom Device nodes programmatically.

#### LabVIEW typedefs and template projects

The wizard generates the LabVIEW typedefs and projects that make up the Custom Device: the System Explorer configuration UI, a set of hook VIs that run during deployment to compile the settings and populate the values into the auto-generated LabVIEW clusters, and a boilerplate engine built around a generated LabVIEW class with a method for each RT Driver state (`Initialize`, `Start`, `Read Data from HW`, `Write Data to HW`, `Close`). You implement your Custom Device behaviour by overriding these methods.

#### Test Bench and Test Hooks

The Express framework generates a Test Bench and Test Hooks for testing and debugging Custom Device Engine code. The Test Bench acts as a harness for testing your custom code standalone, without having to deploy the Custom Device with VeriStand. The Test Hooks provide a Before and After hook VI for each engine state, where you can add your test-specific code and assertions.
