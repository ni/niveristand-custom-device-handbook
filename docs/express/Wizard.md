## Using the Custom Device Express Wizard

The **NI VeriStand Custom Device Wizard** is an open-source LabVIEW project template tool that provides a complete Custom Device project from a device definition you provide. It is hosted on GitHub and is the recommended starting point for all new Express Custom Device projects.

**Repository:** [https://github.com/ni/niveristand-custom-device-wizard](https://github.com/ni/niveristand-custom-device-wizard)

---

### Prerequisites

Before using the Express wizard, install the following:

| Dependency | Minimum Version |
|---|---|
| [NI VeriStand](https://www.ni.com/en-us/support/downloads/software-products/download.veristand.html) | 2026 Q3 onwards |
| [LabVIEW](https://www.ni.com/en-us/support/downloads/software-products/download.labview.html) | 2026 Q3 onwards |
| [LabVIEW Real-Time Module](https://www.ni.com/en-us/support/downloads/software-products/download.labview-real-time-module.html) | 2026 onwards |
| [VeriStand Custom Device Development Tools](https://github.com/ni/niveristand-custom-device-development-tools/releases/latest) | 2026 Q3 onwards |
| [JSONtext](https://www.vipm.io/package/jdp_science_jsontext) | Latest |
| [.NET SDK](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) | 8.0 or later |

> **Note:** Installing the **VeriStand Custom Device Development Tools** deploys the Express Custom Device LabVIEW add-on to `C:\Program Files\NI\LVAddons\nivscustomdeviceexpress\1`. 
>
> Because it is installed as a LabVIEW add-on (`LVAddons`), its shared libraries and support VIs are available to LabVIEW automatically without being copied into each project. The folder contains `lvaddoninfo.json` (add-on metadata) and a `Targets\` directory with per-OS components—`Targets\win64\user.lib\` for the **Windows x64** target and `Targets\NI\RT\Linux\PXI\user.lib\` for the **NI Linux Real-Time (PXI)** target. 
> Each target folder holds `Custom Device Interfaces_v1.lvlibp` (the Custom Device interface APIs), the `NIVS Inline Async API (Express)\` and `Custom Device Test Bench\` library used by the Express framework. 
>
> These files are managed by the installer—do not edit or move them manually, as this can break wizard-generated projects that depend on them.

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
| **Inline HW Interface** | Runs inline with the VeriStand Primary Control Loop (PCL). Use when the Custom Device must exchange data synchronously on every PCL tick. |
| **Inline HW Interface (Inline-Async)** | An inline HW interface template that also includes an asynchronous loop for tasks that must run independently of the PCL rate. |

---

### Creating a New Express Custom Device

The wizard generates a fully structured LabVIEW project along with Scripting APIs DLL.

1. In LabVIEW, select **File → Create Project**.
2. In the **Create Project** dialog, choose **NI VeriStand Custom Device Express** and click **Next**.
3. Select **Execution Mode** and *Target Operating System**
4. In the **XML Editor**, define your Custom Device hierarchy and type definitions
   - Add **Properties**, **Channels**, **Waveforms**, and **Sections** as needed
   - XML Editor creates XML file based on [XML Schema](XML_Definition_Schema.md). While adding type definitions, follow [Configuration Rules](Configuration_Rules.md)
5. Save the XML definition file (`.xml`) to a location inside your project folder.
6. Click **Finish** to generate the project.

![Express Custom Device wizard](images/wizard.png)

> **Tip:** Click the **Launch XML Editor** button to create a new XML file or edit an existing one. Always use this tool and avoid editing the XML manually.

![XML Editor showing the Custom Device definition tree alongside the raw XML file](images/wizard-definition-editor-and-xml.png)

> **Tip:** Keep the XML definition file under source control alongside your project. You can re-open it in the XML Editor at any time to add or modify device nodes, then regenerate the code.

---

### Contributing and Reporting Issues

The wizard is open-source under an MIT-style license. Contributions and bug reports are welcome:

- **Issues:** [https://github.com/ni/niveristand-custom-device-wizard/issues](https://github.com/ni/niveristand-custom-device-wizard/issues)
- **Pull requests:** Fork the repository, create a branch, and open a pull request against `main`
