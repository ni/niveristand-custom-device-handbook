## Auto-Generated Scripting API

The Express framework turns a [Custom Device XML Definition](XML_Definition_Schema.md) into a fully built Custom Device. Part of what it produces is a **.NET scripting API** — a set of strongly typed classes you can use to create and configure the Custom Device programmatically in a VeriStand system definition.

This page explains how the API is generated, where the generated files and the finished Custom Device end up, what the API looks like, and how to run the generator yourself if you ever need to.

---

### Where the scripting API comes from

When you finish the Express wizard, the Custom Device is created end-to-end:

```
Definition XML  ──►  api-gen.exe  ──►  C# project (Auto Generated\APIs\Source)
                                          │
                                          ▼  build
                                 Scripting API .dll (Auto Generated\APIs\Builds)
                                          │
                  wizard opens the LabVIEW project on Finish ──► you build it
                                          │
                                          ▼
                       Finished Custom Device folder (Builds\<CustomDeviceName>)
                                          │  (+ optional GUI Layout.yaml)
                                          ▼
                          Custom Devices folder in Public Documents
```

1. The wizard runs the VeriStand **API generator** (`api-gen.exe`) on your definition XML to produce a ready-to-build C# project, then compiles it into the scripting API assembly.
2. On **Finish**, the wizard opens the generated LabVIEW project. When you build it, LabVIEW packages the configuration/engine/System Explorer libraries together with the scripting API assembly into the finished Custom Device folder.
3. You copy that finished folder into the VeriStand **Custom Devices** directory to use it. See [Distributing the Custom Device](../Distributing_the_Custom_Device.md).

> **Note:** The generated C# project *source* is a build artifact kept under `Auto Generated\APIs\Source`. It is not copied into the finished Custom Device — only the *built* scripting API DLL is included in the final folder along with the LabVIEW components.

---

### Where things are kept

When you point the wizard at a folder, it creates a `<CustomDeviceName> Custom Device` subfolder and generates everything inside it. For the full layout of that folder, see [Custom Device folder hierarchy](Implementing_the_Custom_Device.md#custom-device-folder-hierarchy). The locations that matter for the scripting API are:

| Item | Location |
|---|---|
| The definition XML (input) | Wherever you (or the wizard) saved it. The wizard records this path. |
| The generated C# project (source) | `…\Auto Generated\APIs\Source\GeneratedCode\<CustomDeviceName>\` |
| The built scripting API assembly | `…\Auto Generated\APIs\Builds\<NameSpace>.<CustomDeviceName>.dll` |

The built scripting API assembly (`<NameSpace>.<CustomDeviceName>.dll`) is also copied into the `Windows\` folder of the finished Custom Device (`…\<CustomDeviceName> Custom Device\Builds\<CustomDeviceName>\Windows\`).

![The generated Auto Generated\APIs folder and the finished Builds\<CustomDeviceName> folder](images/auto-generated-apis-and-builds-folders.png)

---

### The generated C# project

The scripting API project lives at `Auto Generated\APIs\Source\GeneratedCode\<CustomDeviceName>\`. For a Custom Device whose `TypeName` is `ExampleCustomDevice`, it contains:

```
ExampleCustomDevice\
    ExampleCustomDevice.csproj
    nuget.config
    GeneratedCode\
        ExampleCustomDevice.cs          (the Custom Device class)
        ExampleCustomDeviceFactory.cs   (factory used by System Explorer)
        <Channel>.cs / <Channel>Factory.cs
        <Waveform>.cs / <Waveform>Factory.cs
        <Section>.cs / <Section>Factory.cs
        EnumDefinitions.cs              (only if the XML declares enums)
        TypeGuids.cs
        BaseNodeUtilities.cs
        GroupNameExpressionEvaluator.cs
        DependentNodeJsonSerializer.cs
```

* A class is generated for the Custom Device and for each `Channel`, `Waveform`, and `Section` type, plus a matching **factory** class for each. The factory is what System Explorer uses to create the node when an operator adds it.
* `EnumDefinitions.cs` is generated only when your XML declares an `<EnumDefinitions>` block.
* Building this project produces `<NameSpace>.<CustomDeviceName>.dll` (for example, `CompanyName.Product.ExampleCustomDevice.dll`), the scripting API assembly that the finished Custom Device includes.
* The project targets the .NET Framework and references the VeriStand system-definition assemblies. It is a standard project that builds with the LabVIEW Express plugin (during the automated build) or with the .NET build tools.

---

### What the scripting API gives you

The generated API mirrors the structure you described in the XML. Using the `ExampleCustomDevice` from the handbook examples, the API surface looks like the following.

#### Constructors

```csharp
// Create a new, fully initialized Custom Device (adds default channels, waveforms, sections,
// and applies default property values from the XML).
var device = new ExampleCustomDevice("My Device");
```

There is also a constructor that wraps an existing node, which VeriStand uses internally when loading a system definition.

#### Properties

Every `<Property>` becomes a strongly typed .NET property whose type matches the [property type](XML_Definition_Schema.md#property-types) from the XML.

```csharp
device.OperationMode = OperationMode.Running;   // an enum property
device.HighFrequencyHz = 1000;                  // a numeric property
device.StringArrayPropertyExample = new[] { "Windows", "Linux" };
```

| XML property type | Generated .NET type |
|---|---|
| `String` | `string` |
| `BinaryString` | `byte[]` |
| `Boolean` | `bool` |
| `I16` / `I32` / `I64` | `short` / `int` / `long` |
| `U16` / `U32` / `U64` | `ushort` / `uint` / `ulong` |
| `Double` | `double` |
| `*Array` types | the corresponding array (`string[]`, `double[]`, `int[]`, ...) |
| `Enum` | the generated enum type |
| `DependentFile` | `DependentFile` |
| `DependentNode` | `DependentNode` |
| `Variant` | `Variant` |

#### Channels, waveforms, and sections

For each child node type, the API generates methods to add and retrieve nodes:

```csharp
// Add a new child by name (returns the existing one if a node with that name already exists).
TimeChannel time = device.AddTimeChannel("CD Time", out bool isNew, out Error error);

// Add a pre-built child node.
device.AddAnalogInput(new AnalogInput("CD Analog Input 1"));

// Retrieve children.
TimeChannel[] times       = device.GetTimeChannels();   // by specific type
CustomDeviceChannel[] all = device.GetChannels();        // all channels

SectionA0[] sections    = device.GetSectionA0s();
AnalogInput[] waveforms = device.GetAnalogInputs();
```

The `Default` node lists in the XML are created for you automatically when you construct a new Custom Device. The `Dynamic` node lists determine which `Add...` methods exist for operator-addable types.

#### Configuration import and export

Every generated Custom Device supports round-tripping its configuration to and from JSON and XML:

```csharp
device.ExportToJson(@"C:\configs\device.json");   // write configuration to a JSON file
device.ExportToJson(out string json);             // or to a string

device.ImportFromJson(@"C:\configs\device.json"); // load configuration from JSON
device.ImportFromXml(@"C:\configs\device.xml");   // load configuration from a definition XML
device.ExportToXml(@"C:\configs\device.xml");     // write configuration to XML
```

These same operations are available to operators as right-click actions in System Explorer. See [Importing and exporting a configuration](Auto_Generated_UI.md#importing-and-exporting-a-configuration).

---

### How property names map to API members

Property and enum-member *display names* in the XML can contain spaces and special characters. The generator keeps the original display name for the UI and the configuration data, and derives a clean API member name from it using these rules:

* If the name contains a `:` or `.`, only the part after the **last** one is used. For example, `Frequency.High frequency` becomes `HighFrequency`.
* The characters `( ) [ ] { } : . -` and spaces are treated as word separators.
* Each word is capitalized and the words are joined (PascalCase).

| XML `PropertyName` | Generated API member |
|---|---|
| `High frequency [Hz]` | `HighFrequencyHz` |
| `Low frequency (Hz)` | `LowFrequencyHz` |
| `Frequency : Low Level frequency` | `LowLevelFrequency` |
| `Frequency.High frequency` | `HighFrequency` |
| `I16_PropertyExample` | `I16PropertyExample` |

When you reference a property elsewhere — for example in a [GUI Layout.yaml](Auto_Generated_UI.md#gui-layoutyaml-reference) file — you use the **original** display name from the XML, not the PascalCase API name.

---

### Generating the API manually with api-gen.exe

The full build is automated by the Express wizard. You normally never run the generator yourself. However, if you already have a definition XML and only want the intermediate C# project — for example, to inspect the generated API or to build it with your own tooling — you can run the generator directly. It installs with VeriStand.

```
api-gen.exe "<PathToDefinitionXml>" "<OutputDirectory>"
```

| Argument | Required? | Description |
|---|---|---|
| `<PathToDefinitionXml>` | Yes | Path to your Custom Device Definition XML. |
| `<OutputDirectory>` | No | Where to write the generated project. Defaults to the current working directory. |

Behavior to be aware of:

* On success, it prints `The Generated files and project are located at: <dir>` and writes the project described in [The generated C# project](#the-generated-c-project).
* `api-gen.exe` **only generates source and a project file. It does not compile the Custom Device DLL.** Build the generated `.csproj` yourself to produce the assembly.
* It validates the XML first. On a validation error it prints `ERROR: <message>` describing the problem (missing attribute, invalid `TypeName`, wrong child order, unsupported type, invalid group-name expression, and so on).
* `api-gen.exe` does **not** generate or read a `GUI Layout.yaml` file — that file is UI-only and is added separately. See [Auto-Generated System Explorer UI](Auto_Generated_UI.md).

> **Note:** Running the generator manually is an optional shortcut for the C# project step only. The recommended path for building a complete, distributable Custom Device is the Express wizard, which runs the generator, builds the assembly, and assembles the final Custom Device folder for you.

![A successful api-gen.exe run](images/api-gen-exe-successful-run.png)

