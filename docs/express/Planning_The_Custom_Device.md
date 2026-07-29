## Planning the Custom Device

The time you invest in planning before opening the wizard pays off directly in a cleaner XML definition, fewer regenerations, and less rework in the engine VIs. This page walks through the decisions you need to make.

---

### 1. Choose a device execution template

The Express wizard offers three templates. Choose based on how your device interacts with the VeriStand Primary Control Loop (PCL).

| Template | Execution model | When to use |
|---|---|---|
| **Inline HW Interface** | Runs synchronously inside the PCL on every iteration | Device must exchange data with every PCL tick and can complete within the PCL's time budget |
| **Inline HW Interface (Inline-Async)** | Inline PCL loop plus a separate asynchronous loop | Device needs synchronous channel exchange AND a background task running at a different rate (for example, a slow configuration read) |
| **Inline Timing and Sync** | Device controls VeriStand engine timing | Device is the timing master; it triggers when the PCL proceeds |

If your device is not timing-critical and communicates with the rest of VeriStand only occasionally, consider whether the Classic asynchronous template is a better fit. See [Selecting a Framework: Classic vs. Express](../Framework_Selection.md).

---

### 2. Design the device hierarchy

Express devices have a four-level hierarchy: **Device → Sections → Channels / Waveforms**. Sections can be nested.

Start with a sketch of what an operator would see in System Explorer's configuration tree. Ask:

* What logical groups of settings belong together? Each group is a candidate **[Section](../Key_Concepts.md#sections-and-hierarchy)**.
* What data values need to be visible to the rest of the VeriStand system at run time? Each one is a **[Channel](../Key_Concepts.md#channels)** (single `double`) or a **[Waveform](../Key_Concepts.md#waveforms)** (array of `double` or `ComplexDouble`).
* What configuration values are set once and deployed? Each one is a **[Property](../Key_Concepts.md#properties)** on the device, section, or channel.

**Example hierarchy for a multichannel ADC device:**

```
ADC Device
  ├─ Properties: Range, SampleRate
  ├─ Acquisition (Section)
  │    ├─ Properties: TriggerMode, TriggerLevel
  │    ├─ AnalogInput × 8  (Channels, Output — data flows out to VeriStand)
  │    └─ SWTrigger        (Channel, Input  — VeriStand writes this)
  └─ FilterConfig (Section)
       └─ Properties: FilterEnabled × 8
```

---

### 3. Channels vs properties

Use a **Channel** when:
- The value changes while the device is running (live data).
- The operator needs to map the value to/from another VeriStand node.
- The value is always a `double`.

Use a **Property** when:
- The value is set at configuration time and deployed with the system definition.
- The value can be any supported data type (string, integer, enum, array, etc.).
- The value does not need to be mapped in the VeriStand system diagram.

A property can still be modified by the engine at run time (for example, to write a status back), but it is not part of the VeriStand channel data exchange.

---

### 4. Choose property types

For each property, pick the most specific type that represents your data. Common choices:

| Data | Recommended type |
|---|---|
| ON/OFF flag | `Boolean` |
| Selection from a fixed list | `Enum` (define it in `<EnumDefinitions>`) |
| Rate, gain, threshold | `Double` |
| Counter, size, count | `U32` or `I32` |
| File path or string identifier | `String` |
| List of rate values | `DoubleArray` |

Avoid `String` for structured data; use `Enum` when the set of valid values is known and fixed.

---

### 5. Default vs dynamic nodes

For each channel, waveform, and section type:

* **Default** — The node is created automatically every time a new device is added to a system definition. Use for nodes that are always present (for example, a mandatory timing channel).
* **Dynamic** — The operator can add these nodes at run time in System Explorer. Use for optional or variable-count nodes (for example, additional analog inputs).

A type can appear in both lists if some instances are mandatory and others are optional.

---

### 6. Choose a namespace and type name

| Item | Rule | Example |
|---|---|---|
| `NameSpace` | Reverse-domain style, no spaces | `CompanyName.ProductLine` |
| `TypeName` | PascalCase, no spaces or dots | `MultichannelADC` |
| Channel/Waveform/Section `TypeName` | PascalCase, no spaces or dots | `AnalogInput`, `SectionA0` |
| Property `PropertyName` | Any text; spaces and brackets are fine | `High frequency [Hz]` |

The namespace and type name together form the scripting API assembly name (`CompanyName.ProductLine.MultichannelADC.dll`) and the .NET class name (`MultichannelADC`). Choose names that will not conflict with other assemblies on the operator's machine.

---

### 8. Plan operating-system support

The wizard asks which operating systems the device targets. Choose all systems your hardware supports:

- **Windows** — host-side configuration library; required for all devices.
- **Linux x64** — Real-Time engine library for Linux-based cRIO/PXI targets.

You can add support for additional targets later by editing the LabVIEW project and the build specifications, but it is cleaner to decide upfront.
