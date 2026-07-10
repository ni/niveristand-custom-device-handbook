## Implementing Custom Logic in Express

The Express wizard generates the engine scaffolding for your device using a **LabVIEW class-based design**. You implement hardware-specific behaviour by overriding a set of well-defined methods. This page explains the generated structure, the override VIs you edit, how to exchange channel data, and how to use the built-in logging framework.

---

### Auto-generated vs user-editable files

The generated project contains two categories of content.

| Location | Contents | Editable? |
|---|---|---|
| `Auto Generated\Configuration\` | Configuration library VIs and controls | No — regenerated from XML |
| `Auto Generated\System Explorer\` | System Explorer library VIs | No — regenerated from XML |
| `Auto Generated\APIs\Source\` | Generated C# scripting API project | No — regenerated from XML |
| `Auto Generated\APIs\Builds\` | Built scripting API assembly | No — build artifact |
| `<DeviceName>\` | Your device class (`<DeviceName>.lvclass`) and its override VIs | **Yes** |
| `<DeviceName>\Logging\` | Logging framework VIs | No — generated framework |
| `<DeviceName>\Utilities\` | Channel group utilities | No — generated framework |


---

### Engine class design

The engine is built around a LabVIEW class (`<DeviceName>.lvclass`) that inherits from the generated base class. You customise the device by overriding five methods.

| Override VI | When called | What to do here |
|---|---|---|
| `Initialize.vi` | Once, when the device starts | Open hardware handles, read deployed property values, allocate buffers |
| `Start.vi` | Once, after `Initialize.vi`, before the first PCL tick | Start hardware acquisition or arm triggers |
| `Read Data from HW.vi` | Every PCL tick | Read hardware data and write values into the **output** channel group |
| `Write Data to HW.vi` | Every PCL tick | Read values from the **input** channel group and send them to the hardware |
| `Close.vi` | Once, when the device stops or on error | Stop hardware, close handles, release all resources |

For the **Inline HW Interface (Inline-Async)** template, the wizard also generates:

| VI | Description |
|---|---|
| `<DeviceName>.Async.vi` | Runs in a separate loop independently of the PCL; use for slow or background tasks (status polling, configuration reads) |

---

### PCL execution flow

#### Inline HW Interface

```
[VeriStand deploys system definition]
   │
   ▼
Initialize.vi  ──► Start.vi
   │
   └─── PCL tick N
           ├── Write Data to HW.vi  (input channels → hardware)
           └── Read Data from HW.vi (hardware → output channels)
   │
   ▼  (on stop or error)
Close.vi
```

`Write Data to HW.vi` and `Read Data from HW.vi` must both complete within the PCL period. If either overruns, VeriStand reports a timing violation.

#### Inline HW Interface (Inline-Async)

The same PCL flow as above, plus:

```
[Starts concurrently with Initialize.vi]
   │
   └─── <DeviceName>.Async.vi (loop, independent of PCL)
           (slow work: status polling, async config reads)
```

Use the `TargetSettings` cluster or a notifier/functional global to share state between the async loop and the PCL overrides.

#### Inline Timing and Sync

The device is the timing master for the VeriStand engine. Structure `Read Data from HW.vi` to:
1. Wait for the hardware trigger or timer event.
2. Read hardware data and populate output channels.
3. Return — VeriStand proceeds to the next PCL iteration.

The device controls the engine rate by how long `Read Data from HW.vi` blocks.

---

### Reading and writing channel data

The generated framework provides pre-built utility VIs for channel data access. Use these inside your override VIs.

| Utility VI | Purpose |
|---|---|
| `Get Output Channel Group.vi` | Returns a group reference for all output channels (device → VeriStand) |
| `Get Input Channel Group.vi` | Returns a group reference for all input channels (VeriStand → device) |
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

### Reading properties at run time

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

### Logging framework

The wizard includes a built-in logging framework in `<DeviceName>\Logging\`. Use it instead of `One Button Dialog.vi` or `Print To Standard Output.vi`, which are not safe in real-time execution loops.

| VI | Use for |
|---|---|
| `Initialize Logging.vi` | Call once in `Initialize.vi` to set up the logger |
| `Log.Info.vi` | Informational messages |
| `Log.Warning.vi` | Non-fatal warnings |
| `Log.Error.vi` | Errors (use when you also return an error cluster) |
| `Log.Debug.vi` | Verbose messages for development; disable in release builds |

Log messages are routed to the VeriStand log and appear in the VeriStand workspace log viewer. On a Real-Time target they are also written to the target system log.

---

### Handling errors in engine VIs

Wire the error cluster through all engine override VIs. When `Initialize.vi` or `Start.vi` returns an error, VeriStand stops the device and reports the error in the workspace. When `Read Data from HW.vi` or `Write Data to HW.vi` returns an error, the device is stopped and `Close.vi` is called. Always set the error source string to identify the device and the failing operation.

