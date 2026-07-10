## Using the Custom Device Express Wizard

The **NI VeriStand Custom Device Wizard** is an open-source LabVIEW project template tool that provides a complete custom device project from a device definition you provide. It is hosted on GitHub and is the recommended starting point for all new Express custom device projects.

**Repository:** [https://github.com/ni/niveristand-custom-device-wizard](https://github.com/ni/niveristand-custom-device-wizard)

---

### Prerequisites

Before using the Express wizard, install the following:

| Dependency | Minimum Version |
|---|---|
| [NI VeriStand](https://www.ni.com/en-us/support/downloads/software-products/download.veristand.html) | 2026 Q3 |
| [LabVIEW](https://www.ni.com/en-us/support/downloads/software-products/download.labview.html) | 2026 Q3 |
| [LabVIEW Real-Time Module](https://www.ni.com/en-us/support/downloads/software-products/download.labview-real-time-module.html) | 2026 |
| [VeriStand Custom Device Development Tools](https://github.com/ni/niveristand-custom-device-development-tools/releases/latest) | Latest release |
| [JSONtext](https://www.vipm.io/package/jdp_science_jsontext) | Latest |
| [.NET SDK](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) | 8.0 or later |

---

### Getting the Wizard

1. Go to the [Releases page](https://github.com/ni/niveristand-custom-device-wizard/releases/latest) of the wizard repository.
2. Download the latest **`.nipkg`** file for the Express wizard (the package labelled `Custom Device Express`).
3. Double-click the `.nipkg` to open it in **NI Package Manager**, then click **Install**.
   - The package installs the project template into the LabVIEW 2026 64-bit `ProjectTemplates` directory automatically.

After installation, the project template becomes available inside LabVIEW's **Create Project** dialog under the **NI VeriStand** filter.

---

### Available Express Templates

The wizard provides the following Express project templates:

| Template | Description |
|---|---|
| **Inline HW Interface** | Runs inline with the VeriStand Primary Control Loop (PCL). Use when the device must exchange data synchronously on every PCL tick. |
| **Inline HW Interface (Inline-Async)** | An inline HW interface template that also includes an asynchronous loop for tasks that must run independently of the PCL rate. |
| **Inline Timing and Sync** | Use when the device controls timing and synchronization for the VeriStand engine. |

---

### Creating a New Express Custom Device

1. In LabVIEW, select **File → Create Project**.
2. In the **Create Project** dialog, choose **NI VeriStand Custom Device Express** and click **Next**.
3. In the **XML Editor**, define your device schema:
   - Add **Properties**, **Channels**, **Waveforms**, and **Sections** as needed.
4. Save the XML definition file (`.xml`) to a location inside your project folder.
5. Click **Finish** to generate the project.

The wizard generates a fully structured LabVIEW project containing all auto-generated components described in [Auto-Generated Components](Auto_Generated_Components.md).

![XML Editor showing the device definition tree alongside the raw XML file](images/wizard-definition-editor-and-xml.png)

> **Tip:** Keep the XML definition file under source control alongside your project. You can re-open it in the XML Editor at any time to add or modify device nodes, then regenerate the auto-generated code.

---

### Contributing and Reporting Issues

The wizard is open-source under an MIT-style license. Contributions and bug reports are welcome:

- **Issues:** [https://github.com/ni/niveristand-custom-device-wizard/issues](https://github.com/ni/niveristand-custom-device-wizard/issues)
- **Pull requests:** Fork the repository, create a branch, and open a pull request against `main`. See [CONTRIBUTING.md](https://github.com/ni/niveristand-custom-device-wizard/blob/main/CONTRIBUTING.md) for guidelines.

