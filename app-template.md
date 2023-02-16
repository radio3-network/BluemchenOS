App Template
============

This document is a work in progress intended to define the standard format for BlümchenOS apps.

These apps have the following characteristics:
+ self-contained, can include their own source code on the app folder
+ shareable, can be shared throught internet or directly by users
+ upgradeable, using SemVer to assure automatic dependency upgrades


### Summary

ESP32 is traditionally used for single-purpose applications that are uploaded by a computer inside a firmware that is not intended to change.
At the moment of this document exists no common notion of launching apps from ESP32, much less apps with a standardized GUI system.
The goal for an app template is to enable ESP32 microcontrollers to install/launch/upgrade and remove apps from within the operating system.

### File format

Apps consist of folders that are expected to contains files (binary/text/configuration) with specific names.
These files include for example the icon to be displayed, the launch script, the user-modified configuration and so forth.
Over the next sections are detailed each of these requirements.


### Software considerations
Given the limitations of hardware, there is not (yet) a multi-user platform with different access/admin rights.
Each app runs with full administrative rights.

These apps are NOT limited to launch an executable with a binary file. Apps can launch scripts or any other formats supported by the operating system. See the details on the related sections below such as the config.json file.

English is used as the base language for documentation and source code. Translations for apps into other languages is possible throught the configuration file.


### Hardware considerations
Apps are expected to be stored inside removable storage cards like MicroSD and similar.
You can expect full access to the file system and resources such as pins albeit with limitations to ensure proper interaction with other apps.
The filesystem within these cards is typically FAT32 or ExFAT, can change but the files should be reachable to the operating system.


### Names for files and folders

The names selected for each file and folder should be as short as possible, to permit better compatibility of the app when being shared/accessed across different file systems. We recommend the names to not be bigger that 30 characters and to avoid unsupported characters. For example, if the filename can exist on Linux and Windows operating systems, then it can typically be used too inside Blümchen.


### App versions

Apps follow the semantic versioning (SemVer) specification defined at https://semver.org/

In summary an app should follow a X.Y.Z versioning where:
+ X represents a change that breaks API compatibility
+ Y is a minor upgrade that adds new features on the API
+ Z a release with modifications/patches that DO NOT change the API

Each app can simultaneously be a dependency for other apps to function properly. Therefore it is critical that all app authors understand SemVer and follow the specification mentioned here. SemVer enables upgrades to happen without breaking functionality on apps.

For example, when there are only minor or patch releases then these dependencies can be upgraded automatically.

The first version released to a public app repository should use 1.0.0 as version number.


## File/Folder structure summary

Below is an example of a simple app prepared for ESP32 with a single icon for the launcher.

```
.
./config.json
./bin/
./bin/binESP32
./setup/
./setup/install.sh
./setup/upgrade.sh
./setup/uninstall.sh
./setup/schedule.sh
./media/
./media/iconlauncher-64px.png
./data/
./translate/
./translate/en_US.json
./versions/
./versions/1.0.0/
./share/
./source/
./temp/
```

### Base folder

This is the root folder where all files related to this app.
For compatibility purposes it should follow these rules:
+ 8 characters as maximum name length
+ ASCII alpha-numeric characters plus "." "_" "-"
+ only lower case characters
+ must start with a letter or number
+ permanent name, should never be changed
+ must not clash with names for other existing apps

For the purpose of describing the location of files inside this base folder we use the common Linux way of documenting relative file paths.

This folder name becames the Unique Identification (UID) of the app across the multiple repositories where it will be listed and available.


### Bin folder [./bin]

A folder for placing the binaries of the app. This folder is placed on the base folder, for example: "./bin" and contains the binaries for different architectures where the app is expected to run. These are usually pre-compiled binaries for ESP32, ARM and similar machines. Other binaries from third-party tools might also be included inside this folder.


### Bin files

Bin files are the compiled binaries for the app itself. They start with the name "bin" and then include the platform specific information. In some architectures such as x86 there is a distinction between binaries made for different operating systems such as Linux, Windows or OSX.

Examples:
+ binESP32
+ binESP2866
+ bin64-win.exe
+ bin64-linux
+ bin64-osx

The config.json defines the type of launch for the app but it is an optional value. When there is a binary with the exact name for the architecture where the app is running then that binary will be launched regardless of the value inside the config.json file.


### Setup folder [./setup]

This folder is located on the base folder with the name "setup" and contains the scripts for installing, upgrading and removing the app from disk.

Each of these scripts follow specific names:
+ install.sh
+ upgrade.sh
+ uninstall.sh
+ schedule.sh

Scripts are files with extension .sh similar to Linux environments that will run specific actions from the command line. A template for each of these files will be included on the app template demonstration.

The schedule script is intended for cases where the app needs to run at specific intervals of time, for example to check for new messages. The syntax and method for using this mechanism will later be documented.


### Media folder [./media]

A folder for placing icons or sound files. Can be used by the app for any artwork resources that are used.
This folder is written in lowercase characters. For example: "./media"


### Data folder [./data]

Location for storing permanent data related to the app. For example databases, or any other user-specific data that has been generated by the app while being used. This is different from the temporary folder where all files are expected to be deleted on routine basis after being used once. The name for this folder is "data".


### Translate folder [./translate]

Each app can have translations for different languages. The base language for an app is US english by default, but when different languages are available they will be used. Language files are JSON files with the extension ".json".

Filename for each translation file follows the IANA/Unicode language-tag-extension definitions that can be found inside the ./ ain/ folder of the core.zip file that can be downloaded at https://www.iana.org/assignments/language-tag-extensions-registry/language-tag-extensions-registry

It is mandatory to include a "en_US.json" inside the app to permit a common language available as reference for other translations.


### Versions folder [./versions/]

Since each app can be a dependency to other apps, some apps will primarily become library apps to be used as support for others. When an app requires a specific version of a dependency and another app requires another specific version of the dependency there is a clash of incompatibility. To address this topic when it occurs it is possible to include a folder with multiple versions of the same app/dependency.

For example:
+ 1.0.0
+ 1.0.1
+ 1.0.2
+ 2.0.4

These folders contain a copy of the app for a specific version. On the base folder is always placed the most recent version of the app. When an app specifies a dependency then the operating system will install the specified version and place a copy on the versions folder to assure that it will remain always available.

Limitations of this method:
+ ./data/ and ./temp/ folder are version specific, there is no shared data between versions
+ ./data/ is duplicated across each version and might use cause impact on disk storage

Dependencies should have these limitations in mind when being designed by authors to reduce duplication and waste of disk space. In the case of data that can be safely shared/used across different versions, please use the ./share folder for that purpose.


### Share folder [./share]

This is an **optional** folder for sharing data across multiple versions of the same app/dependency.


### Source folder [./source]

The source code for the app. This is an optional folder but critical to assure a longer life for the app since it will not depend on the availability of an external server on the internet. It will be self-contained, capable of compiling itself for newer/different machines in the future. It includes all the source code and instructions required for compiling the app.


### Temp folder [./temp]

A folder for placing temporary files that are meant to be deleted from time to time. For example, cache files or files/databases downloaded temporarily from the network. This folder is optional, only required if needed for the app operation and can be created/deleted on runtime by the app.


### Launcher icons [./media/iconlauncher-XXpx.png]
Icon files are used on the launcher application with the following characteristics:
+ PNG format
+ square shape (equal dimension on each side)
+ placed inside the folder ./media/
+ transparency is supported

Diferent screen sizes can use launcher icons in different sizes to improve the visual aspect.
Their size is specified on the file name.

Launcher icons files follow the name convention "iconlauncher-XXpx.png" where "XX" represents the pixel length. 

For example "iconlauncher-64px.png" would mean an icon image with 64 pixels of dimension.

When the app has special icon support for black&white screens such as ePaper then it is possible to include special icons for these devices.
On that situation add the suffix "_bw" on the image. For example: "iconlauncher-XX_bw.png".


### Config.json file [./config.json]

The main config file for the app that permits customization for the specific hardware where the app is running.
Settings on the config file can be made both by the app or by external apps, these files follow these requirements:
+ JSON format
+ placed inside the base folder
+ filename "config.json"

Inside the config.json are expected the following data fields
+ "version" for version number
+ "title" to specify the app title (up to 30 ASCII characters)
+ "description", for a one line description (up to 100 characters)
+ "repository" for the location of the official git repository
+ "license" to report the applicable license for the app (SPDX.org format)
+ "copyright" to list the copyright authors
+ "dependencies" a list of dependencies and supported versions
+ "execute", for the the command to be run when launched

Some of the fields can have multiple values. Each value on those fields should be separated using the JSON syntax. An example would be "dependencies": ["acme-1.0.0", "example-1.x.x", "test-5.0.2"].

The list of fields where multiple values are supported:
+ repository
+ license
+ copyright
+ dependencies


### Dependencies

Each app can list inside the "config.json" the dependencies necessary for running.
A dependency is another app that needs to be installed or available on the system.

Each dependency is named using the UID (unique identification) of the app and the version range that is supported.

Examples:
+ "acme-1.0.0" to require a dependency called "acme" with exact version 1.0.0
+ "acme-1.x.x" to require a dependency with the most recent releases of 1.
