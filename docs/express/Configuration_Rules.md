## Configuration Rules and Constraints

The Express framework enforces a set of rules at generation time and at VeriStand load time. Understanding these rules prevents generation failures and run-time errors.

---

### XML definition rules

These rules are validated against the schema before any code is generated. A violation produces a descriptive error that identifies the problem line in the XML.

#### Type names

- `TypeName` attributes on `CustomDevice`, `Channel`, `Waveform`, `Section`, and enum types must **not contain spaces or special characters**.
- Valid: `AnalogInput`, `SectionA0`, `MyDevice`
- Invalid: `Analog Input`, `Section.A0`, `MyDevice'`
- Property `PropertyName` attributes are **not** restricted this way and may contain spaces, brackets, and other characters.
- Do not use reserved words in C#

#### GUIDs

- Every `TypeGuid` must be a valid GUID string in the format `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.
- GUIDs must be **unique within the document**. Reusing a GUID for two different types causes a generation error.
- GUIDs must be **stable across regenerations** for the same type. Changing a GUID in the XML while an existing system definition references the old GUID will cause VeriStand to fail to load the saved Custom Device.
- Custom Device Express wizard contains XML Editor tool, which creates GUIDs for each type definitions you add. Do not manually add/edit the GUIDs.

#### Node list references

- Every `TypeName` used in a `DefaultXNodes` or `DynamicXNodes` list must match the `TypeName` of a `<Channel>`, `<Waveform>`, or `<Section>` definition in the same document.
- Referencing an undefined type name causes a generation failure.
- Empty node lists are valid and can be written as self-closing tags, for example `<DefaultChannelNodes />`.

#### Enum definitions

- Enum type names must follow the same no spaces or special character rule as other type names.
- Each enum member must have a unique string name within its enum type.
- An `Enum` property's `<DefaultValue>` must reference an enum type by name: `<Enum>EnumTypeName</Enum>`. The enum type must be declared in `<EnumDefinitions>`.

---

### Scripting API and naming rules

These rules affect how the generator maps XML names to C# API members. See [Property name mapping](Auto_Generated_Scripting_API.md#how-property-names-map-to-api-members) for the full algorithm.

- If two properties on the same type produce the **same generated API member name** (after cleaning), generation fails. Add distinguishing characters to one of the XML `PropertyName` values.
- Enum member names are also cleaned by the same algorithm. Members that collide after cleaning cause a generation failure.

---

### Build and file layout constraints

- The finished Custom Device folder must be placed under `<Common Data>\National Instruments\NI VeriStand <version>\Custom Devices\` to be visible in System Explorer.
- Windows libraries (`*.lvlibp`, `*.dll`) must be in the `Windows\` subfolder; Linux x64 libraries must be in the `Linux_x64\` subfolder.
- The scripting API assembly must reside in `Windows\` (for System Explorer, which runs on Windows) folder. VeriStand looks for it at the path recorded in the Custom Device's XML.
- Do not ship the `Auto Generated\` source tree inside the finished Custom Device folder. The `Builds\` output is self-contained.
