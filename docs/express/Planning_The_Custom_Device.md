## Planning the Custom Device

The most critical phase of Custom Device development is planning. Several VeriStand idiosyncrasies require more thorough planning than smaller stand-alone LabVIEW applications.

As the use-cases and flexibility of a Custom Device increases, so does the complexity of planning and implementing. The design tradeoff of this increase is a more robust Custom Device that requires less customization by the user.

The time you invest in planning before opening the wizard pays off directly in a cleaner XML definition, fewer regenerations, and less rework in the engine VIs. This section walks through the decisions you need to make.

---

### Choose Custom Device execution template

The Express wizard offers following templates. Choose based on how your Custom Device interacts with the VeriStand Primary Control Loop (PCL).

| Template | Execution model | When to use |
|---|---|---|
| **Inline HW Interface** | Runs synchronously inside the PCL on every iteration | Custom Device must exchange data with every PCL tick and can complete within the PCL's time budget |
| **Inline HW Interface (Inline-Async)** | Inline PCL loop plus a separate asynchronous loop | Custom Device needs synchronous channel exchange AND a background task running at a different rate (for example, a slow configuration read) |

---

### Plan operating-system support

The wizard asks which operating systems the Custom Device targets. Choose all systems your hardware supports:

- **Windows** — host-side configuration library; required for all Custom Devices.
- **Linux x64** — Real-Time engine library for Linux-based cRIO/PXI targets.

You can edit targets later by editing the LabVIEW project and the build specifications, but it is cleaner to decide upfront.

### Design the Custom Device hierarchy

Custom Devices have a four-level hierarchy: **Custom Device → Sections → Channels / Waveforms**. Sections can be nested.

Start with a sketch of what a user would see in System Explorer's configuration tree. Ask:

* What logical groups of settings belong together? Each group is a candidate **[Section](../Key_Concepts.md#sections-and-hierarchy)**.
* What data values need to be visible to the rest of the VeriStand system at run time? Each one is a **[Channel](../Key_Concepts.md#channels)** (single `double`) or a **[Waveform](../Key_Concepts.md#waveforms)** (array of `double` or `ComplexDouble`).
* What configuration values are set once and deployed? Each one is a **[Property](../Key_Concepts.md#properties)** on the Custom Device, section, channel or waveform.

**Example hierarchy for a multichannel ADC Custom Device:**

```
ADC Custom Device
  ├─ Properties: Range, SampleRate
  ├─ Acquisition (Section)
  │    ├─ Properties: TriggerMode, TriggerLevel
  │    ├─ AnalogInput × 8  (Channels, Output — data flows out to VeriStand)
  │    └─ SWTrigger        (Channel, Input  — VeriStand writes this)
  └─ FilterConfig (Section)
       └─ Properties: FilterEnabled × 8
```

---

### Choose property types

For each property, pick the most specific type that represents your data. For example, common choices could be:

| Data | Recommended type |
|---|---|
| ON/OFF flag | `Boolean` |
| Selection from a fixed list | `Enum` |
| Rate, gain, threshold | `Double` |
| Counter, size, count | `U32` or `I32` |
| File path or string identifier | `String` |
| List of rate values | `DoubleArray` |

Avoid `String` for structured data; use `Enum` when the set of valid values is known and fixed. While translating your plan to XML definition in wizard, you can explore and select all the possible data types for properties.

---

### Choose a namespace and type name

Once you have settled on the hierarchy, the nodes, and the configurations you need, define the following:

- **Namespace and name** — Assign a namespace (`CompanyName.ProductLine`) and a name (`MultichannelADC`) to your Custom Device. Together they form the scripting API assembly name, for example `CompanyName.ProductLine.MultichannelADC.dll`. Pick names that will not clash with other assemblies on the user's machine.
- **Section, Channel, Waveform, and Enum types** — Using your hierarchy plan, identify and define the types you need. Two Section types differ when they contain different properties/configurations or different child sections, channels, or waveforms. The same distinction applies to Channel and Waveform types, which vary by their data type and the configurations/properties they carry.

Each type you define drives all of the auto-generation: UI, Scripting APIs, and the LabVIEW settings clusters. A .NET class is generated for every type, so make sure each Type Name follows these rules strictly:

| Item | Rule | Example |
|---|---|---|
| `NameSpace` | Reverse-domain style, no spaces | `CompanyName.ProductLine` |
| Custom Device `TypeName` | PascalCase, no spaces or dots | `MultichannelADC` |
| Channel/Waveform/Section `TypeName` | PascalCase, no spaces or dots | `AnalogInput`, `SectionA0` |
| Property `PropertyName` | Any text; spaces and brackets are fine | `High frequency [Hz]` |

For more details on rules, refer [Configuration Rules](Configuration_Rules.md)

---


### Default vs dynamic nodes

For each channel, waveform, and section type:

* **Default** — The node is created automatically every time a new Custom Device is added to a system definition. Use for nodes that are always present (for example, a mandatory timing channel). Initialization VI of the Custom Device adds these default nodes
* **Dynamic** — The user can add these nodes at run time in System Explorer, typically using Runtime Menu Options of a node. Use for optional or variable-count nodes (for example, additional analog inputs).

A type can appear in both lists if some instances are mandatory and others are optional.

---

You can build Type Definition XML file directly in the [Express Wizard](Wizard.md) once you have planned the above details.
