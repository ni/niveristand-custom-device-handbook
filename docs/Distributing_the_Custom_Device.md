## Distributing the Custom Device

After building, debugging, validating, and benchmarking the custom device, you need to package the device for others to use.

As with most applications, you can streamline distribution in two ways.

* With an Installer—For more information, refer to the *LabVIEW Help* topic [(Windows) Installer Properties](https://zone.ni.com/reference/en-XX/help/371361R-01/lvdialog/installer_tab_windows/).

* With a Package for Distribution—For more information, refer to the *LabVIEW Help* topic [Creating Packages for Distribution](https://zone.ni.com/reference/en-XX/help/371361R-01/lvhowto/creating_packages/).

NI recommends distributing the custom device by copying the necessary files into a simple folder hierarchy. The top-level folder should contain the following items.

1. Build folder - Contains files for operator to copy to the `<Common Data>\Custom Devices\` VeriStand directory. This will add the custom device to the system definition.
2. Source folder - Contains the LabVIEW Project used to create the custom device and any supporting libraries and dependencies required to build the custom device. For example, the AES-201 custom device would ship with the LabVIEW API and hardware DLL.
3. Readme file - Contains instructions for installing, licensing, using, and modifying the custom device. The file should also contain contact information if you plan to support the device or a disclaimer if you do not plan to support the device. The Readme file is a good place to store any benchmarking information.

**Note:** Do not include the *Custom Device API.lvlib* files with the distribution. You do not want to replace the library on the operator’s machine or change the library linking on your machine.

The following image demonstrates the distribution folder hierarchy for the AES-201 custom device.

![](images/AES-201_Distribution_and_Folder_Hierarchy.JPG)
