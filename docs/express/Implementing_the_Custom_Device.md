## Implementing Custom Logic in Express

---

### Custom Device folder hierarchy

When you finish the wizard, it creates a `<CustomDeviceName> Custom Device` folder that holds everything for the Custom Device. The structure is:

```
<CustomDeviceName> Custom Device\
  Custom Device <CustomDeviceName>.xml     ← read by VeriStand System Explorer
  Auto Generated\                          ← generated from the input XML
    APIs\
      Source\                              ← generated C# scripting API project
      Builds\                              ← built scripting API .dll
    Configuration\                         ← System Explorer configuration library (adapter) 
    Engine\                                ← engine library (adapter) containing RT Driver VI.vi
    System Explorer\                       ← System Explorer UI library containing Main Page.vi and Action VIs
  <CustomDeviceName>\                      ← the LabVIEW template you open and edit
    Deployment Hooks\                      ← compiles settings/channel groups on the host during deployment
    Engine\
      Overrides\                           ← the lifecycle VIs you implement
      Types\                               ← engine specific typedefs
      Utilities\                           ← channel-group and settings helper VIs
      Logging\
      BuildVIs\
      <CustomDeviceName> Engine.lvclass    ← your Custom Device class
    Shared\                                ← generated typedefs / clusters
    Test Bench\                            ← standalone debug harness
  Builds\                                  ← the finished distributable Custom Device
```

The folder has three top-level areas:

* **`Auto Generated\`** — everything the framework produces from the XML. Regenerated on every run; no need to edit for most use cases.
* **`<CustomDeviceName>\`** — the LabVIEW source project the wizard opens on **Finish**. This is where you add your custom code.
* **`Builds\`** — deployable Custom Device. See [Distributing the Custom Device](../Distributing_the_Custom_Device.md).

---

### Generated template code

#### The `Auto Generated` folder

The wizard generates these components from the [XML definition](XML_Definition_Schema.md) every time it runs, overwriting whatever was there before. Back up any changes you make to the generated code, otherwise they are lost on the next iteration.

##### APIs

The wizard produces the scripting API in two steps. First, it generates the C# source (under the `APIs\Source\` folder) by internally calling the **API auto-generation executable** (`api-gen.exe`), which is installed with VeriStand. Second, it compiles that source into the scripting API assembly (`APIs\Builds\<NameSpace>.<CustomDeviceName>.dll`). The Custom Device's System Explorer PPL then consumes this `.dll` to create and configure the Custom Device. For a full walkthrough of how the API is generated—and how to run the generator yourself—see [Auto-Generated Scripting API](Auto_Generated_Scripting_API.md).

##### Configuration

The Configuration library (`<CustomDeviceName> Configuration.lvlibp`) runs on Windows inside System Explorer. Two VIs in it are called by VeriStand itself:

| Configuration VI | When called | Purpose |
|---|---|---|
| `Initialization VI.vi` | When VeriStand loads the Custom Device | Adds default channel/waveform/section nodes to the Custom Device tree in System Explorer |
| `ActionVIOnCompile.vi` | When the operator compiles the system definition | Sets RT driver file paths so VeriStand knows where to find the engine PPL on the target and calls into deployment hooks to get the compiled settings |

---

##### Engine

##### System Explorer

###### Auto Generated GUI

#### The `<CustomDeviceName>` folder — where you add your custom code

This is the project the wizard opens when you click **Finish**. It contains the class and VIs you fill in with hardware-specific behaviour.

| Location | Contents | Edit? |
|---|---|---|
| `Engine\Overrides\` | The lifecycle VIs — `Initialize.vi`, `Start.vi`, `Read Data from HW.vi`, `Write Data to HW.vi`, `Close.vi` | **Yes — your code goes here** |
| `Engine\<CustomDeviceName> Engine.lvclass` | Your device class; add private data and subVIs here | **Yes** |
| `Shared\` | Generated typedefs/clusters — device settings, channel-group, and per-channel-type property controls (for example `CustomDeviceSettings.ctl`, `ChannelGroups.ctl`, `GroupSettings.ctl`) | No — regenerated |
| `Engine\Utilities\` | Channel-group and settings helper VIs | No — framework |
| `Engine\Logging\` | Logging framework VIs | No — framework |
| `Deployment Hooks\` | Host-side VIs that compile settings and channel data | No — framework |
| `Test Bench\` | Standalone debug harness | No — framework |

##### Engine Custom Code

The Express wizard generates the engine scaffolding for your Custom Device using a **LabVIEW class-based design**. You implement hardware-specific behaviour by overriding a set of well-defined methods.

The engine is built around a LabVIEW class (`<CustomDeviceName>.lvclass`) that inherits from the generated base class. You customise the Custom Device by overriding five methods.

| Override VI | When called | What to do here |
|---|---|---|
| `Initialize.vi` | Once, when the Custom Device starts | Open hardware handles, read deployed property values, allocate buffers |
| `Start.vi` | Once, after `Initialize.vi`, before the first PCL tick | Start hardware acquisition or arm triggers |
| `Read Data from HW.vi` | Every PCL tick | Read hardware data and write values into the **output** channel group |
| `Write Data to HW.vi` | Every PCL tick | Read values from the **input** channel group and send them to the hardware |
| `Close.vi` | Once, when the Custom Device stops or on error | Stop hardware, close handles, release all resources |

For the **Inline HW Interface (Inline-Async)** template, the wizard also generates:

| VI | Description |
|---|---|
| `<CustomDeviceName>.Async.vi` | Runs in a separate loop independently of the PCL; use for slow or background tasks (status polling, configuration reads) |


##### Reading and writing channel data

The generated framework provides pre-built utility VIs for channel data access. Use these inside your override VIs.

| Utility VI | Purpose |
|---|---|
| `Get Output Channel Group.vi` | Returns a group reference for all output channels (Custom Device → VeriStand) |
| `Get Input Channel Group.vi` | Returns a group reference for all input channels (VeriStand → Custom Device) |
| `Get Grouped Channel Settings.vi` | Returns the compiled channel metadata (names, indices, types) |
| `Get Channel Values by Block Data Ref.vi` | Reads all channel values in a group at once using a block data reference |
| `Set Channel Values by Block Data Ref.vi` | Writes all channel values in a group at once using a block data reference |

Typical `Read Data from HW.vi` pattern:

```
1. Call hardware API to acquire data
2. Build a DBL array of acquired values
3. Call Set Channel Values by Block Data Ref.vi  →  writes values to the output channel group
```

Typical `Write Data to HW.vi` pattern:

```
1. Call Get Channel Values by Block Data Ref.vi  →  reads input channel group values into a DBL array
2. Index the array to get individual channel values
3. Call hardware API to apply the values
```

---

##### Reading properties at run time

Properties declared in the XML are compiled into the system definition and available to the engine after deployment. Retrieve them in `Initialize.vi` using the VeriStand **Get Item Property VI**.

```labview
NI VeriStand - Get Item Property.vi
   Device Ref In  ← (passed in by the framework via Initialize Custom Device Settings.vi)
   Property Name  ← "Range"        (the XML PropertyName string, case-sensitive)
   Default Value  ← 0              (returned if property not found)
   → Value        → use to configure hardware
```

Call `Initialize Custom Device Settings.vi` first to obtain the device reference cluster that contains the device ref. Read configuration properties once during `Initialize.vi`; they cannot be updated from the engine after the system definition deploys.

---

##### Logging framework

The wizard includes a built-in logging framework in `<CustomDeviceName>\Logging\`. Use it instead of `One Button Dialog.vi` or `Print To Standard Output.vi`, which are not safe in real-time execution loops.

| VI | Use for |
|---|---|
| `Initialize Logging.vi` | Call once in `Initialize.vi` to set up the logger |
| `Log.Info.vi` | Informational messages |
| `Log.Warning.vi` | Non-fatal warnings |
| `Log.Error.vi` | Errors (use when you also return an error cluster) |
| `Log.Debug.vi` | Verbose messages for development; disable in release builds |

Log messages are routed to the VeriStand log and appear in the VeriStand workspace log viewer. On a Real-Time target they are also written to the target system log.

---

##### Handling errors in engine VIs

Wire the error cluster through all engine override VIs. When `Initialize.vi` or `Start.vi` returns an error, VeriStand stops the Custom Device and reports the error in the workspace. When `Read Data from HW.vi` or `Write Data to HW.vi` returns an error, the Custom Device is stopped and `Close.vi` is called. Always set the error source string to identify the Custom Device and the failing operation.

##### Deployment Hooks

The wizard also generates a **Deployment Hooks** library (`<DeviceName> Deployment Hooks.lvlibp`). This runs on the **host** (Windows) when the operator compiles the system definition and produces the compiled channel group data that the engine reads at run time.

| Deployment Hooks VI | Purpose |
|---|---|
| `Compile Settings.vi` | Entry point; called by VeriStand on compile |
| `Compile Channel Groups.vi` | Builds channel group metadata from the deployed device tree |
| `Compile Channel Group Refs.vi` | Resolves channel block data references |
| `Build Channel Group Info.vi` | Packages compiled group info into the settings cluster |
| `Parse JSON for Channels.vi` | Reads JSON-encoded channel configuration |

You do not normally need to edit the Deployment Hooks library. It is auto-generated from the XML and handles all channel compilation automatically.

---

##### Test Bench


#### Customizing the generated behavior

You customize an Express Custom Device by **extending** the generated scaffolding rather than editing the generated components:

* **Extend (preferred).** Implement your hardware logic inside the `Overrides\` VIs, and add private data, methods, and subVIs to `<CustomDeviceName> Engine.lvclass`. This code lives in the source project and is not part of the `Auto Generated` folder.
* **Change the UI** by adding a [`GUI Layout.yaml`](Auto_Generated_UI.md#gui-layoutyaml-reference) file instead of editing the generated System Explorer VIs.
* **Change the structure** (channels, properties, sections, enums) by editing the [XML definition](XML_Definition_Schema.md) and regenerating — never by hand-editing the generated clusters or API.

> **Note:** Anything under `Auto Generated\` (and the `Shared\` typedefs) is rebuilt from the XML on every regeneration. Keep all of your customizations in the source project's `Overrides\` VIs and device class so they survive a regenerate.

---

