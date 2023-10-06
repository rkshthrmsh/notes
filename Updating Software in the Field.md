---
tags:
  - embeddedLinux
  - swupdates
---
## Updating Software in the Field
There are many approaches to software updates. Broadly, they can be characterized as:
- Local updates: Often performed by a technician who carries the update on a portable medium and has physical access to each individual system.
- Remote updates: Update is initiated by the user or technician locally, but is downloaded from a remote server.
- **Over the Air** (**OTA**) updates: The update is pushed and managed entirely remotely without needing any local input.

All embedded Linux devices have the same basic set of components: bootloader, kernel, root filesystem, system applications, and device-specific data. However, each component varies in difficulty of updating:
![[Pasted image 20231003151513.png]]
### [[Basics of Software Updates]]
### [[Types of Software Update Mechanism]]
### Over the Air Updates 
Updating over-the-air (OTA) means having the ability to push software to a device or group of devices via a network, usually without any end user interaction with the device. For this to happen, we need a central server to control the update process and a protocol for downloading the update to the update client. In a typical implementation, the client polls the update server from time to time to check if there are any updates pending.

Open-source projects that can implement OTA are: [[Mender]] in *managed mode*, [[balena]], and Eclipse hawkBit.