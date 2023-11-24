---
tags:
  - embeddedLinux
---
### `systemd`
`systemd` defines itself as a system and service manager. It is state of the art and common on desktop and server Linux distributions and is becoming popular on embedded Linux systems too. Some of its advantages are:
- The configuration is simpler and more logical (once you understand it). Rather than the sometimes convoluted shell scripts of [[Starting Up—The init Program#System V `init`|System V init]], `systemd` has unit configuration files that are written in a well-defined format.
- There are explicit dependencies between services, rather than a two-digit code that merely sets the sequence in which the scripts are run.
- It is easy to set the permissions and resource limits for each service, which is important for security.
- It can monitor services and restart them if needed.
- Services are started in parallel, potentially reducing boot time.
#### Units, Services, and Targets
#### Unit
A unit is a configuration file that describes a target, a service, and several other things. Units are text files that contain properties and values. They are found in three different places:
- `/etc/systemd/system`: Local configuration
- `/run/systemd/system`: Runtime configuration
- `/lib/systemd/system`: Distribution-wide configuration

When looking for a unit, `systemd` searches the directories in that order, stopping as soon as it finds a match, and allowing you to override the behavior of a distribution-wide unit by placing a unit of the same name in `/etc/systemd/system`. You can disable a unit completely by creating a local file that is empty or linked to `/dev/null`.

All unit files begin with a section marked `[Unit]`, which contains basic information and dependencies. 
```
[Unit]
Description=D-Bus System Message Bus
Documentation=man:dbus-daemon(1)
Requires=dbus.socket
```
In addition to the description and a reference to the documentation, there is a dependency. Dependencies in the Unit section are expressed through the following keywords:
- `Requires`: A list of units that this unit depends on that are started when this unit is started.
- `Wants`: A weaker form of Requires; the units listed are started but the current unit is not stopped if any of them fail.
- `Conflicts`: A negative dependency; the units listed are stopped when this one is started and, conversely, if one of them is started, this one is stopped.
These three keywords define **outgoing dependencies**. They are used mostly to create dependencies between targets. There is another set of dependencies called **incoming dependencies**, which are used to create links between services and targets. In other words, outgoing dependencies are used to create the list of targets that need to be started as the system goes from one state to another, and incoming dependencies are used to determine the services that should be started or stopped in any particular state. Incoming dependencies are created by the `WantedBy` keyword.

The `Before` and `After` keywords determine the order in which the dependencies are started. In the absence of the `Before` or `After` directive, the units will be started or stopped in parallel with no particular ordering.
##### Service
A service is a daemon that can be started and stopped, very much like a [[Starting Up—The init Program#System V `init`|System V init]] service. Service files are a type of unit files have a name ending with `.service`. A service unit has a `[Service]` section that describes how it should be run.
```
[Service]
ExecStart=/usr/sbin/lighttpd
ExecReload=/bin/kill -HUP $MAINPID
```
##### Target
A target is a group of services, similar to, but more general than, a System V init runlevel. There is a default target that is the group of services that are started at boot time.

A target is another type of unit that groups services (or other types of units). A target is a metaservice in that respect and also serves as a synchronization point. A target only has dependencies. Targets have names ending in `.target`.

A target can have a directory named `<target_name>.target.wants`, which can contain links to services. This is exactly the same as adding the dependent unit to the `[Wants]` list in the target.
#### System Boot with `systemd`
`systemd` is run by the kernel as a result of `/sbin/init` being symbolically linked to `/lib/systemd/systemd`. It runs the default target, `default.target`, which is always a link to the desired target, such as `multi-user.target` for a text login or `graphical.target` for a graphical environment. The default target may be overridden by passing `system.unit=<new target>` on the kernel command line. 

`systemctl` can be used to get more information:
```
$ systemctl get-default
multi-user.target

# Print text graph of dependencies:
$ systemctl list-dependencies

# List services and their current state:
$ systemctl list-units --type service

# List targets and their current state:
$ systemctl list-units --tupe target
```
#### Adding Custom Service
An example of adding a custom service called `simpleserver` is as follows:
```
[Unit]
Description=Simple Server

[Service]
Type=forking
ExecStart=/usr/bin/simpleserver

[Install]
WatedBy=multi-user.target
```
The `[Install]` section creates an incoming dependency on `multi-user.target` so that our server is started when the system goes into the multi-user mode.

Once the unit is saved in `/etc/systemd/system/simpleserver.service`, you can start and stop it using the `systemctl start simpleserver` and `sytemctl stop simpleserver` commands. To make it persistent, you need to add a permanent dependency to a target. This is the purpose of the `[Install]` section in the unit; it says that when this service is enabled, it will become dependent on `multi-user.target`, and so will be started at boot time. You enable it using `systemctl enable`, like this:
```
# start
$ systemctl start simpleserver

# stop
$ systemctl stop simpleserver

# status
$ systemctl status simpleserver

# persist
$ systemctl enable simpleserver
```
#### Adding a Watchdog
`systemd` has a useful feature that distributes the watchdog between multiple services. It can be configured to expect a regular `keepalive` call from a service and take action if it is not received, creating a per-service software watchdog. For this to work, you have to add code to the daemon to send the `keepalive` messages. It needs to check for a non-zero value in the `WATCHDOG_USEC` environment variable, and then call `sd_notify(false, "WATCHDOG=1")` within this time.

To enable the watchdog in the service unit, add something like this to the `[Service]` section:
```
WatchdogSec=30s
Restart=on-watchdog
StartLimitInterval=5min
StartLimitBurst=4
StartLimitAction=reboot-force
```
In this example, the service expects a `keepalive` call every 30 seconds. If it fails to be delivered, the service will be restarted, but if it is restarted more than four times in 5 minutes, `systemd` will force an immediate reboot.

A watchdog like this takes care of individual services, but what if `systemd` itself fails, the kernel crashes, or the hardware locks up? In those cases, we need to tell systemd to use the watchdog driver: just add `RuntimeWatchdogSec=NN` to `/etc/systemd/ system.conf.systemd`, which will reset the watchdog within that period, and so the system will reset if `systemd` fails for some reason.