App Template
============

This document is a work in progress intended to define the standard format for BlümchenOS apps.

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
./media/
./media/iconlauncher-64px.png
./data/
./translate/
./translate/en_US.json
./temp/
```

### Base folder

This is the root folder where all files related to this app.
For compatibility purposes it should follow these rules:
+ 8 characters in length
+ alpha-numeric characters plus "." "_" "-"
+ must start with a letter or number
+ permanent name, should never be changed
+ must not clash with names for other existing apps

For the purpose of describing the location of files inside this base folder we use the common Linux way of documenting relative file paths.


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


### Setup folder [./setup]

This folder is located on the base folder with the name "setup" and contains the scripts for installing, upgrading and removing the app from disk.

Each of these scripts follow specific names:
+ install.sh
+ upgrade.sh
+ uninstall.sh

Scripts are files with extension .sh similar to Linux environments that will run specific actions from the command line. A template for each of these files will be included on the app template demonstration.


### Media folder [./media]

A folder for placing icons or sound files. Can be used by the app for any artwork resources that are used.
This folder is written in lowercase characters. For example: "./media"


### Data folder [./data]

Location for storing permanent data related to the app. For example databases, or any other user-specific data that has been generated by the app while being used. This is different from the temporary folder where all files are expected to be deleted on routine basis after being used once. The name for this folder is "data".


### Translate folder [./translate]

Each app can have translations for different languages. The base language for an app is US english by default, but when different languages are available they will be used. Language files are JSON files with the extension ".json".

Filename for each translation file follows the IANA/Unicode language-tag-extension definitions that can be found inside the ./ ain/ folder of the core.zip file that can be downloaded at https://www.iana.org/assignments/language-tag-extensions-registry/language-tag-extensions-registry

It is mandatory to include a "en_US.json" inside the app to permit a common language available as reference for other translations.


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
+ "license" to report the applicable license for the app
+ "copyright" to list the copyright author
+ "execute", for the the command to be run when launched
