---
tags:
  - embeddedLinux
---
## Starting Up—The init Program
There are many possible implementations of `init`. The three main ones are BusyBox `init`, System V `init`, and `systemd`.

The kernel bootstrap code seeks to find a root filesystem, either [[Root Filesystem#`initramfs`|initramfs]] or a filesystem specified by `root=` on the kernel command line, and then executes a program that, by default, is `/init` for `initramfs` and `/sbin/init` for a regular filesystem. The init program has root privilege, and since it is the first process to run, it has a process ID (PID) of `1`. If, for some reason, `init` cannot be started, the kernel will panic.

The job of the init program is to take control of the boot process in user space and set it running.
- During boot, after the kernel transfers control, the init program starts other daemon programs and configures system parameters and other things needed to get the system into a working state.
- Optionally, it launches a login daemon, such as getty, on terminals that allow a login shell. 
- It adopts processes that become orphaned as a result of their immediate parent terminating and there being no other processes in the thread group.
- It responds to any of the init's immediate children terminating by catching the SIGCHLD signal and collecting the return value to prevent them from becoming zombie processes.
- Optionally, it restarts those daemons that have terminated.
- It handles the system shutdown.

The three `init` programs that you are most likely to encounter in embedded devices are BusyBox `init`, System V `init`, and `systemd`. [[Build Systems#Buildroot|Buildroot]] has options to build all three with BusyBox `init` as the default. [[The Yocto Project]] allows you to easily choose between System V `init` and `systemd` with System V init as the default.
![[Pasted image 20231012142621.png]]
### BusyBox `init`
BusyBox has a minimal init program that uses a configuration file, `/etc/inittab`, to define rules to start programs at bootup and to stop them at shutdown. Usually, the actual work is done by shell scripts, which, by convention, are placed in the `/etc/init.d` directory. init begins by reading `/etc/inittab`. This contains a list of programs to run, one per line, with this format:
```
<id>::<action>:<program>
```

The role of these parameters is as follows:
- `id`: This is the controlling terminal for the command.
- `action`: This includes the conditions to run this command, as shown in the following list.
	- `sysinit`: Runs the program when init starts before any of the other types of actions.
	- `respawn`: Runs the program and restarts it if it terminates. It is typically used to run a program as a daemon.
	- `askfirst`: This is the same as respawn, but it prints the message Please press Enter to activate this console to the console, and it runs the program after Enter has been pressed. It is used to start an interactive shell on a terminal without prompting for a username or password.
	- `once`: Runs the program once but does not attempt to restart it if it terminates.
	- `wait`: Runs the program and waits for it to complete.
	- `restart`: Runs the program when init receives the SIGHUP signal, indicating that it should reload the inittab file.
	- `ctrlaltdel`: Runs the program when init receives the SIGINT signal, usually as a result of pressing Ctrl + Alt + Del on the console.
	- `shutdown`: Runs the program when init shuts down.
- `program`: This is the program to run.

[[Build Systems#Buildroot|Buildroot]] has been making effective use of BusyBox init for many years. Buildroot has two scripts in `/etc/init.d/` named `rcS` and `rcK`. The first one runs at bootup and iterates over all the scripts in `/etc/init.d/` with names that begin with a capital `S` followed by two digits, and runs them in numerical order. These are the start scripts. The `rcK` script is run at shutdown and iterates over all the scripts beginning with a capital `K` followed by two digits and runs them in numerical order. These are the kill scripts. With this in place, it becomes easy for Buildroot packages to supply their own start and kill scripts, using the two-digit number to impose the order in which they should be run, and so the system becomes extensible.
### System V `init`
Until recently, it was the init daemon for almost all desktop and server distributions and a fair number of embedded systems as well. However, in recent years, it has been replaced by [[systemd]]

The [[#BusyBox `init`]] daemon is just a trimmed-down version of System V init. Compared to BusyBox init, System V init has two advantages: 
- Firstly, the boot scripts are written in a well-known, modular format, making it easy to add new packages at build time or runtime.
- Secondly, it has the concept of **runlevels**, which allow a collection of programs to be started or stopped in one go when switching from one runlevel to another. There are eight runlevels, numbered from 0 to 6, plus S:
	- `S`: Runs startup tasks 
	- `0`: Halts the system
	- `1` to `5`: Available for general use
	- `6`: Reboots the system

The init program starts the default runlevel given by the initdefault line in /etc/ inittab as follows:
```
id:3:initdefault:
```
The runlevel can be change at runtime using the `telinit [runlevel]` command, which sends a message to `init`. You can find the current runlevel and the previous one using the `runlevel` command.

Each runlevel has a number of scripts that stop things, called kill scripts, and another group that starts things, the start scripts. When entering a new runlevel, `init` first runs the kill scripts in the new level, and then the start scripts in the new level. Daemons that are currently running and that have neither a start script nor a kill script in the new runlevel are sent a `SIGTERM` signal. In other words, the default action on the switching runlevel is to terminate daemons unless told to do otherwise.

In truth, runlevels are not used that much in embedded Linux: most devices simply boot to the default runlevel and stay there. However, Runlevels are a simple and convenient way to switch between modes, for example, from production to maintenance mode.
#### `inittab`
The init program begins by reading `/etc/inttab`, which contains entries that define what happens at each runlevel. The format of each line in `inittab` is as follows:
```
id:runlevels:action:process
```
- `id`: A unique identifier of up to four characters.
- `runlevels`: The runlevels for which this entry should be executed. This was left blank in the [[#BusyBox `init`]] inittab.
- `action`: Action keywords similar to [[#BusyBox `init`]] but without `askfirst`.
- `process`: The command to run.
#### `init.d` Scripts
Each component that needs to respond to a runlevel change has a script in /etc/ init.d to perform the change. The script should expect two parameters: `start` and `stop`. The runlevel-handling script, `/etc/init.d/rc`, takes the runlevel it is switching to as a parameter. For each runlevel, there is a directory named `rc<runlevel>.d`:
```
$ ls -d /etc/rc*
/etc/rc0.d /etc/rc2.d /etc/rc4.d /etc/rc6.d 
/etc/rc1.d /etc/rc3.d /etc/rc5.d /etc/rcS.d 
```
Within each `rc` directory are kill scripts and start scripts starting with `K` and `S`, respectively. The `rc` script runs all the scripts beginning with a `K` first, adding the `stop` parameter, and then runs those beginning with an `S`, adding the `start` parameter.
#### Adding a New Daemon
`start-stop-daemon` is a helper function that makes it easier to manipulate background processes. It starts the daemon with the `-S` parameter, making sure that there is never more than one instance running at any one time. To stop a daemon, you use the `-K` parameter, which causes it to send a signal, `SIGTERM` by default, to indicate to the daemon that it is time to terminate.

To make a custom daemon operational, copy the daemon script to the target directory called `/etc/init.d/<script>` and make it executable. Then, add links from each of the runlevels that you want to run this program from. For example:
```
$ cd /etc/init.d/rc<runlevel>.d
$ ln -s ../init.d/<script> S99<script>
```
If there is something that needs to be done, add kill links to levels `0` and `6`:
```
$ cd /etc/init.d/rc0.d
$ ln -s ../init.d/<script> K01<script>
$ cd /etc/init.d/rc6.d
$ ln -s ../init.d/<script> K01<script>
```
#### Starting and Stopping Services
You can interact with the scripts in `/etc/init.d` by calling them directly. All scripts implement `start` and `stop`, and they should also implement `help`. Some implement `status` as well, which will tell you whether the service is running or not.
### [[systemd]]