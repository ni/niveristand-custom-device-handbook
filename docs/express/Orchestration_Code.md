## Auto-Generated Orchestration Code

The Express framework generates the orchestration layer that coordinates your device's lifecycle inside VeriStand. This page describes what is generated, how the pieces fit together, and what you can customise.

---

### What orchestration code is

*Orchestration* refers to the code that manages the device's lifecycle transitions — initialization, execution, and shutdown — and that handles channel data exchange between your device and the VeriStand engine. In Classic custom devices, developers write all of this by hand. In Express, it is auto-generated from the XML definition so the framework already knows the structure of your channels, properties, and sections.

---

### Generated project structure

The wizard generates separate LabVIEW projects for each target:

```
<DeviceName> Custom Device\
  <DeviceName> Engine.lvproj          ← Windows engine project
  <DeviceName> Engine Linux x64.lvproj ← Real-Time (Linux x64) engine project
  <DeviceName>.lvclass                ← Your device class (user-editable overrides)
  <DeviceName>.lvlib                  ← Engine library
  Logging\
    Initialize Logging.vi             ← Framework; do not edit
    Log.vi, Log.Info.vi, ...
  Utilities\
    Initialize Custom Device Settings.vi  ← Reads deployed property values
    Initialize Resources.vi               ← Sets up channel group references
    Get Channel Group.vi
    Get Input Channel Group.vi
    Get Output Channel Group.vi
    Get Grouped Channel Settings.vi
  VS Custom Device API\
    Create NIVS Custom Device API.vi
    Get Channel Values by Block Data Ref.vi
    Set Channel Values by Block Data Ref.vi
    Get Channel Value by Data Reference.vi
    Set Channel Value by Data Reference.vi
    Print Debug Line.vi
  RT Driver VI.vi                     ← Entry point called by VeriStand; do not edit
```

The `RT Driver VI.vi` is the entry point that VeriStand calls on the real-time target. It calls the override VIs on your device class in the correct order.

---

### Deployment Hooks library

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

### Configuration library

The Configuration library (`<DeviceName> Configuration.lvlibp`) runs on Windows inside System Explorer. Two VIs in it are called by VeriStand itself:

| Configuration VI | When called | Purpose |
|---|---|---|
| `Initialization VI.vi` | When VeriStand loads the device | Adds default channel/section nodes to the device tree in System Explorer |
| `ActionVIOnCompile.vi` | When the operator compiles the system definition | Sets RT driver file paths so VeriStand knows where to find the engine PPL on the target |

---

### Orchestration flow

#### Inline HW Interface

```
[System definition deployed]
   │
   ▼
RT Driver VI.vi  ──► Initialize Custom Device Settings.vi  (reads properties from deployed sysdef)
                 ──► Initialize Resources.vi               (creates channel group refs)
                 ──► your Initialize.vi
                 ──► your Start.vi
   │
   └── PCL tick N ──► your Write Data to HW.vi
                  ──► your Read Data from HW.vi
   │
   ▼  (on stop or error)
RT Driver VI.vi  ──► your Close.vi
                 ──► releases channel refs
```

#### Inline HW Interface (Inline-Async)

Identical to the above, plus a concurrent loop:

```
[Starts after Initialize Resources.vi]
   │
   └── <DeviceName>.Async.vi  (independent loop; runs until Close.vi returns)
```

The async loop has its own error output. If it returns an error, VeriStand stops the device and calls `Close.vi`.

#### Inline Timing and Sync

Same override VIs as Inline HW Interface, but the `Read Data from HW.vi` blocks until the hardware trigger fires. This makes the device the engine's timing source.

---

### Channel group data model

The Deployment Hooks library compiles all channel references into a **channel group** structure at system-definition compile time. At run time, the engine uses block data references to read and write all channels in a group with a single API call, which is more efficient than per-channel access. The channel group metadata is stored in the compiled settings cluster and read by `Initialize Custom Device Settings.vi` on startup.

This compiled structure is why adding, removing, or renaming channels requires a **recompile** (redeploy) of the system definition — the channel group indices must be rebuilt.

---

### Debug PPL build spec

The engine project includes a **Debug PPL** build specification (with pre- and post-build action VIs). Building this spec produces a Packed Project Library you can use for LabVIEW debugging. It links the engine against the LabVIEW development environment so you can set breakpoints and probes in your override VIs while the device is running. See [Standalone Test Bench](Test_Bench.md) for how to use it.

