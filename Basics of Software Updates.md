---
tags:
  - embeddedLinux
  - swupdates
---
### Basics of Software Updates
At the very least, the software update mechanism must be:
- Robust, so that an update does not render the device unstable
- Fail-safe, so that there is a fallback mode if all else fails
- Secure, to prevent the device from being hijacked by people installing unauthorized updates.
#### Making Updates Robust
When it comes to devices that are deployed in the field, often including remote sites, the key requirement for the update is **atomicity**, i.e., there should be no stage at which part of the system is updated but no other parts. There must be a single, uninterruptible change to the system that switches to the new version of the software. To achieve atomicity, the update must be installed alongside the running system, and then a switch is thrown to move from the old to the new. There are two different ways to approach atomicity:
- Having two copies of the root filesystem and other major components, wherein one is live and the other receives updates. This approach includes [[Types of Software Update Mechanism#Symmetric Image Update|Symmetric Image Update]] and [[Types of Software Update Mechanism#Asymmetric Image Update|Asymmetric Image Update]].
- Having two or more copies of the root filesystem in different subdirectories, and selecting one of them at boot time. This is known as [[Types of Software Update Mechanism#Atomic File Updates|Atomic File Update]].
#### Making Updates Fail-Safe
One mode of update failure is when the update was installed correctly but contains code that stops the system from booting. Ideally, this should be detected by the system and it should revert itself to a previous working image. Several failure modes can lead to this:
- The first is a kernel panic, caused, for example, by a bug in a kernel device driver, or being unable to run the init program. A sensible place to start is by configuring the kernel to reboot a number of seconds after a panic. This can be done either by setting `CONFIG_PANIC_TIMEOUT` for the kernel or by setting the kernel command line to `panic`. Moreover, enabling panic on Oops in the kernel configuration could also serve useful. This is done by setting `CONFIG_PANIC_ON_OOPS=y` or, on the kernel command line, `oops=panic`.
- A second failure mode occurs when the kernel launches `init` successfully but for some reason, the main application fails to run. For this, a watchdog is helpful. A **watchdog** is a hardware or software timer that restarts the system if the timer is not reset before it expires.

Both failures result in **boot loops**: either a kernel panic or a watchdog timeout causes the system to reboot. If the problem is persistent, the system will reboot continually. To break out of the boot loop, we need some code in the bootloader to detect the case and to revert to the previous, known good, version. A typical approach is to use a **boot count** that is incremented by the bootloader on each boot, and that is reset to zero in user space once the system is up and running. If the system enters a boot loop, the counter is not reset and so continues to increase. Then, the bootloader is configured to take remedial action if the counter exceeds a threshold. 

In [[U-Boot]], this is handled by three variables: 
- `bootcount`: This variable is incremented each time the processor boots. 
- `bootlimit`: If `bootcount` exceeds the `bootlimit`, U-Boot runs the commands in `altbootcmd` instead of `bootcmd`. 
- `altbootcmd`: This contains the alternative boot commands, for example, to roll back to a previous version of the software or to start the recovery-mode operating system.

For this to work, there must be a way for a user space program to reset the boot count. We can do that using U-Boot utilities that allow the U-Boot environment to be accessed at runtime: 
- `fw_printenv`: Prints the value of a U-Boot variable 
- `fw_setenv`: Sets the value of a U-Boot variable These two commands need to know where the U-Boot environment block is stored, for which there is a configuration file in `/etc/fw_env.config`.

Incrementing the boot count on each boot and then resetting it when the application begins to run leads to unnecessary writes to the environment block, wearing out the flash memory and slowing down system initialization. To prevent having to do this on all reboots, U-Boot has a further variable named `upgrade_available`. If upgrade_available is 0, then bootcount is not incremented. `upgrade_available` is set to 1 after an update has been installed so that the boot count protection is in use only when it is needed.
#### Making Updates Secure
The biggest vulnerability is that of a fake remote update. To prevent this, we need to authenticate the update server before starting the download. We also need a secure transfer channel, such as HTTPS, to guard against tampering with the download stream.

One way to detect a bogus update is to use a secure boot protocol in the bootloader. If the kernel image is signed at the factory with a digital key, the bootloader can check the key before it loads the kernel and refuses to load it if the keys do not match. So long as the keys are kept private by the manufacturer, it will not be possible to load a kernel that is not authorized.