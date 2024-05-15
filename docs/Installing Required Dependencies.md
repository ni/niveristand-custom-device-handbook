## Installing Required Dependencies

When setting up a system for custom device development, we must ensure that all the necessary dependencies are installed.

### Required Dependencies

* LabVIEW
* LabVIEW Real-Time (if you will be deploying the custom device to a Linux Real-Time target)

The version of LabVIEW/LabVIEW Real-Time must match the major version (i.e. the year version) and bitness of VeriStand you will use to deploy the custom device. Refer to the following table for bitness compatibility.

| VeriStand | LabVIEW |
| --------- | ------- |
| 2021 and later | 64-bit |
| 2020 R6 and earlier | 32-bit |

For example, to create a custom device for VeriStand 2021 R3, we could use LabVIEW 2021 64-bit or LabVIEW 2021 SP1 64-bit.

* VeriStand Support for LabVIEW

This package is included in the VeriStand installer. If you have a compatible version of LabVIEW installed, support for that LabVIEW version will be installed when you install VeriStand.

* [VeriStand Custom Device Development Tools](https://github.com/ni/niveristand-custom-device-development-tools/releases)

This repository contains 3 different packages used by many of the NI-supported custom devices, but also includes APIs and toolkits that could be useful to custom device developers (such as the Inline-Async API). Download and install the compatible package from the Releases page of that repository.

* [VeriStand Custom Device Wizard](https://github.com/ni/niveristand-custom-device-wizard/releases)

This package installs the Custom Device Wizard that is used to create custom device projects from templates. Starting from the templates is highly recommended since it greatly simplifies the amount of work needed to create a new custom device. Install the package from the Releases page of that repository that matches the LabVIEW version you are using.

### Additional Dependencies

If you are building your custom device from scratch, you will need to ensure that any drivers or toolkits being used in the custom device code are installed and compatible with the LabVIEW bitness. For example, some toolkits may only be compatible with LabVIEW 32-bit, so you would not be able to use them to create a custom device for VeriStand 2021 or later.

If you want to edit an existing custom device, refer to the Readme page for that custom device for additional dependencies that you may need to install.