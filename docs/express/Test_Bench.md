## Standalone Test Bench

The Express wizard generates a **Custom Device Test Bench** library backed by a **Debug PPL** (Packed Project Library) build spec inside the engine LabVIEW project. Building this spec lets you load and run your override VIs inside the LabVIEW development environment on Windows, so you can set breakpoints, use probes, and inspect data flow without deploying to a real-time target.

---

### What the Debug PPL provides

- Runs `Initialize.vi`, `Start.vi`, `Read Data from HW.vi`, `Write Data to HW.vi`, and `Close.vi` on your Windows development PC.
- Links the engine against the LabVIEW development environment so all LabVIEW debugging tools (breakpoints, probes, highlight execution) work in the override VIs.
- Skips the VeriStand deploy cycle — errors in initialization surface immediately in LabVIEW.
- Does not require a real-time target or a VeriStand system definition.

---

### Building the Debug PPL

1. Open the engine LabVIEW project (`<DeviceName> Engine.lvproj`).
2. In the **Build Specifications** section, locate **Debug PPL**.
3. Right-click **Debug PPL** and select **Build**.
   - The **Debug PPL Pre-Build Action.vi** runs first to configure the build.
   - The PPL is produced in the project's build output folder.
   - The **Debug PPL Post-Build Action.vi** runs last to finalize the output.

---

### Running override VIs with the LabVIEW debugger

1. Open `<DeviceName>.lvclass` in your LabVIEW project.
2. Open the override VI you want to debug (for example, `Read Data from HW.vi`).
3. Set breakpoints and probes on the block diagram.
4. Load the Debug PPL output (or run the VI directly from LabVIEW if the class is configured for non-reentrant execution).
5. When the VI hits a breakpoint, the LabVIEW execution pauses and you can inspect wire values and step through the code.

For the **Inline-Async** template, you can also debug `<DeviceName>.Async.vi` by opening it and setting breakpoints before starting the loop.

---

### Stubbing hardware for PC-only debugging

The override VIs run on Windows during debug. Hardware drivers that require a Real-Time OS will fail. Add a Boolean control or conditional disable structure to your `Initialize.vi` that switches between a real hardware path and a simulated path:

```
Simulation mode?
  TRUE  → use hardcoded test values, skip hardware API calls
  FALSE → normal hardware path
```

This lets you exercise the full override VI logic, including channel data writes and reads, without physical hardware.

---

### Debugging tips

**Channel data in debug mode.** The Debug PPL does not connect to a live VeriStand system, so the channel block data references that `Get Channel Values by Block Data Ref.vi` and `Set Channel Values by Block Data Ref.vi` use will not contain real VeriStand channel values. Inject test values directly in the override VI during debugging.

**Property values.** `Initialize Custom Device Settings.vi` reads compiled settings from a deployed system definition. During debug, these settings are not available. Wire in a test `TargetSettings` cluster constant in your `Initialize.vi` for debugging.

**Logging.** Log output from the `Log.Info.vi` / `Log.Error.vi` VIs appears in the LabVIEW output window during debug, making it easy to trace execution without breakpoints.

**Timing.** The Debug PPL runs at PC speed and does not enforce PCL timing constraints. Test timing correctness on the real-time target using a deployed system definition.

