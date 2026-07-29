## Custom Device Express Framework Overview

The **Express** framework is an enhanced development model for VeriStand custom devices. Instead of hand-coding every System Explorer UI page VIs, configuration VIs, engine VIs, and scripting APIs, you define your device once in an XML definition file. The framework generates all the repetitive parts from that definition, so you can focus on the logic that is unique to your Custom Device.

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

Every property you declare in the XML becomes a typed control in System Explorer's configuration pane — no LabVIEW UI code required. An optional `GUI Layout.yaml` file lets you arrange controls into named sections, apply conditional visibility/editability rules, and control column layout. See [Auto-Generated System Explorer UI](Auto_Generated_UI.md).

#### Scripting API

The `api-gen.exe` tool translates the XML definition into a C# project containing a strongly typed class for each device, section, channel, and waveform type you declared. Building that project produces the scripting API assembly that operators and automation scripts use to create and configure device nodes programmatically. See [Auto-Generated Scripting API](Auto_Generated_Scripting_API.md).

#### LabVIEW clusters and template projects

The wizard generates the RT Driver VI, Deployment Hooks library, and channel group utility VIs that coordinate device lifecycle (initialization, PCL execution, shutdown) and compile channel group data at system-definition build time. You implement device behaviour by overriding five methods on the generated LabVIEW class (`Initialize`, `Start`, `Read Data from HW`, `Write Data to HW`, `Close`). See [Auto-Generated Orchestration Code](Orchestration_Code.md).

#### Test Bench and Test Hooks

The engine LabVIEW project includes a **Debug PPL** build spec that links your override VIs against the LabVIEW development environment. Build it to use LabVIEW's full debugger (breakpoints, probes, highlight execution) on your Windows PC, without a real-time target or a VeriStand deployment. See [Standalone Test Bench](Test_Bench.md).

---

### Key concepts

**XML definition** — The single source of truth for the device. It declares the device's type name, namespace, GUID, properties, and its full hierarchy of sections, channels, and waveforms. The wizard reads it to generate all four output categories. You keep it under source control alongside the generated project.

**Auto-generated vs user-editable files** — Files inside `Auto Generated\` are overwritten every time you regenerate. Files outside that folder (your engine logic, shared VIs, and custom helpers) are preserved. The distinction is maintained by folder location, not file names.

**Type GUIDs** — Every device, section, channel, and waveform type has a unique GUID declared in the XML. VeriStand uses these GUIDs to identify node types in a system definition. A GUID must never be reused across different device versions that are meant to coexist in the same system definition.

**Build outputs** — Building the LabVIEW project produces a `Builds\<DeviceName>\` folder that contains the finished custom device. This folder is self-contained and ready to copy into the VeriStand Custom Devices directory.

