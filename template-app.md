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
Apps have full access to the


### Hardware considerations
Apps are expected to be stored inside removable storage cards like MicroSD and similar.
You can expect full access to the file system and resources such as pins albeit with limitations to ensure proper interaction with other apps.
The filesystem within these cards is typically FAT32 or ExFAT, can change but the files should be reachable to the operating system.


### Base folder
This is the root folder where all files related to this app 
