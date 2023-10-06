---
tags:
  - embeddedLinux
---
## The Yocto Project
The Yocto Project is a general build system for building complex embedded devices. Every component is generated as a binary package, by default, using the Red Hat Package Manager (RPM) format, and then the packages are combined to make the filesystem image. In addition, one can install a package manager in the filesystem image, which allows updating packages at runtime. In effect, the Yocto Project can be used to create a custom Linux distribution.

The build process is structured around groups of recipes that are written using a combination of Python and shell script. A task scheduler called **BitBake** uses the recipes to produce the components of embedded Linux.

The Yocto Project collects several components, the most important of which are:
- OE-Core: Core metadata, share with OpenEmbedded.
- BitBake: Task scheduler, shared with OpenEmbedded and other projects.
- Poky: Reference distribution.
- Documentation
- Toaster: A web-based interface to BitBake and its metadata.

The project provides a stable base which can be used as is, or extended using **meta layers**.

### Installing and Configuring
Yocto Project can be cloned from its git repository by choosing the branch with the required code name. For example, for *Dunfell*:
```
git clone -b dunfell git://git.yoctoproject.org/poky.git
```

To start a build project:
```
source poky/oe-init-build-env <optional build name>
```
This creates a working directory named `build/`, by default, or with the supplied optional build name. This file must be sourced each time before working on the build project. Initially, the build directory contains only one subdirectory named `conf/`, containing the following files:
- `local.conf`: Contains specification of the device to build and the build environment.
- `bblayers.conf`: Paths of meta layers to use.
### Building
Building is done by using BitBake and providing it with the type of root filesystem to generate. Some common options are:
- `core-image-minimal`: Small console based system that is useful for tests and as a base for custom images.
- `core-image-minimal-initramfs`: Similar to `core-image-minimal`, but built as a ramdisk.
- `core-image-x11`: Basic image with support for graphics through an X11 server and `xterminal` terminal app.
- `core-image-full-cmdline`: Console based system offering a standard CLI experience and full support for the target hardware.

An example build command:
```
bitbake core-image-minimal
```
Once the build is complete, several new folders will appear in the build directory. Some of these include:
- `downloads/`: Contains all the source downloaded for the build.
- `tmp/`: Contains all the build artifacts.
	- `work/`: Build directory and staging area for the root filesystem.
	- `deploy/`: Contains final binaries to be deployed on the target.
		- `deploy/images/<machine name>/`: Contains the bootloader, kernel, and root filesystem that are ready to be run on the target.
		- `deploy/rpm`: Contains RPM packages that make up the images.
		- `deploy/licenses`: Contains license files extracted from each package.

### Layers
The metadata for the Yocto Project is structured into layers, with each layer having a name beginning with `meta`. The core layers of the Yocto Project are:
- `meta`: The OpenEmbedded core which also contains some changes for Poky.
- `meta-poky`: Metadata specific to the Poky distribution.
- `meta-yocto-bsp`: BSP packages for the machines that the Yocto Project supports.
The list of layers in which BitBake searches for recipes is stored in `<build directory>/conf/bblayers.conf`. By default, this layer includes all three layers mentioned above.

In this structure, it is very easy to extend the Yocto Project by adding new layers. Adding a layer is as simple as copying the new `meta` directory to a suitable location and adding it to the `bblayers.conf` file. Each meta layer has to have at least one configuration file, named `conf/layer.conf`, a `README` file, and a license.
```
source poky/oe-init-build-env <build>
bitbake-layers create-layer <layer>

# Rename layer to meta-layer
mv <layer> meta-<layer>

# Add layer to build configuration
bitbake-layers add-layer meta-<layer>

# Confirm layer structure
bitbake-layers show-layers
```

BitBake processes metadata of several different types:
- **Recipes**: Files ending in `.bb` These contain information about building a unit of software, including how to get a copy of the source code, dependencies of components, build and installation process. A recipe is a collection of tasks written in a combination of Python and shell script. These tasks are usually named as `do_fetch`, `do_unpack`, `do_patch`, `do_configure`, `do_compile`, `do_install`. The default task is `do_build`, which performs all the subtasks required to build the recipe. Tasks in a recipe can be listed with:
```
bitbake -c listtasks <recipe>  
```
- **Append**: Files ending in `.bbappend`. These override or extend some details of a recipe. They simply append their instructions to the end of recipe file of the same root name.
- **Include**: Files ending in `.inc`. Contain information that is common to several recipes. These maybe added using the `includ` or `require` keywords. The latter is a strict requirement and leads to errors if the file does not exist.
- **Classes**: Files ending in `.bbclass`. Contain common build information on things like building a kernel, or an autotools project. The classes can be inherited, using the `inherit`, keyword and extended in recipes. The class `classes/base.bbclass` is implicitly inherited in all recipes.
- **Configuration**: Files ending in `.conf`. They define various configuration variables that govern the project's build process.
#### Example
To create a recipe for a simple `helloworld` program in `meta-nova`, the directory would look like:
```
meta-nova/recipes-local/helloworld
├── files
│   └── helloworld.c
└── helloworld_1.0.bb
```
The `helloworld_1.0.bb` contains:
```
DESCRIPTION = "A friendly program that prints Hello World!"
PRIORITY = "optional" SECTION = "examples"
LICENSE = "GPLv2"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/GPL-2.0;md5=80 1f80980d171dd6425610833a22dbe6"

SRC_URI = "file://helloworld.c"

S = "${WORKDIR}"

do_compile() {
	${CC} ${CFLAGS} ${LDFLAGS} helloworld.c -o helloworld
}

do_install() {
	install -d ${D}${bindir} # ${D} expands to the staging area
	install -m 0755 hellowordl ${D}${bindir} # default /usr/bin
}
```

The recipe is compiled by:
```
bitbake helloworld
```
Which generates a working directory in `tmp/work/<toolchain>/helloworld` and an RPM package in `/tmp/deploy/rpm/<arch>/`.

##### Including in List of Packages: Option 1
Finally, to include it in the list of packages to be installed, modify `conf/local.conf` to add:
```
IMAGE_INSTALL_append = " helloworld" # Note the space after opening quote
```
Now, the package is added to any image that is built with BitBake.

##### Including in List of Packages: Option 2
Another option, which is more shareable is to write an **image recipe**. An image recipe contains instructions about how to create the image files for a target, including the bootloader, kernel, and root filesystem images. This is usually stored in an `images` directory within the layer's directory. The result of the example just shown abouve can be achieved by adding the following `nova-image.bb` recipe to `meta-nova/recipes-local/images`:
```
# Start from a minimal image
require recipes-core/images/core-image-minimal.bb
IMAGE_INSTALL += "helloworld"
```
#### Commercial Licenses
By default, commercial licenses are disabled. However, they can be enabled by specifying it in the recipe:
```
LICENSE_FLAGS = "commercial"
```
And in the `conf/local.conf` file:
```
LICENSE_FLAGS_WHITELIST = "commercial"
```
### Creating an SDK
Creating a standalone toolchain that other developers can install is quite helpful. This avoids the need for everyone in the team to install the Yocto Project. An SDK containing development libraries and header files can be created using:
```
bitbake -c popoulate_sdk <image>
```
The output is a self-installing shell script in `/tmp/deploy/sdk`:
```
poky-<c_library>-<host_machine>-<target_image><target_machine>toolchain-<version>.sh
```

Installing the toolchain can be done by just running this script, which defaults to the `/opt/poky` install directory. To make use of the toolchain, the environment and setup script needs to be sourced:
```
source /opt/poky/<version>/environment-setup-<arch>
```
This loads the C compiler, C++ compiler, C preprocessor, Assembler, and the Linker to the `CC`, `CXX`, `CPP`, `AS`, and `LD` environment variables, which should be used directly for tasks.

A more basic toolchain with just C and C++ cross compilers, the C library, and header files can be obtained with:
```
bitbake meta-toolchain
```
### Building on Top of an Existing BSP
In Yocto, a Board Support Package (BSP) layer adds support for a particular hardware device or a family of devices. The support usually includes a bootloader, device tree blobs, additional kernel drivers, and, optionally, user space software and peripheral firmware. By convention, the BSP names start with a `meta-` prefix followed by the machine's name.

To build an existing BSP, for example, on the Raspberry Pi using the dunfell code base, the following repositories need to be cloned at the same level as (parallel to) the Yocto Project (`poky`).
```
git clone -b dunfell git://git.openembedded.org/meta-openembedded
git clone -b dunfell git://git.yoctoproject.org/meta-raspberry
```

Once this is done, create a new build project as per usual:
```
source poky/oe-init-build-env build-rpi
```
Then, add the following layers to the image (order matters):
```
bitbake-layers add-layer ../meta-openembedded/meta-oe
bitbake-layers add-layer ../meta-openembedded/meta-python
bitbake-layers add-layer ../meta-openembedded/meta-networking
bitbake-layers add-layer ../meta-openembedded/meta-multimedia
bitbake-layers add-layer ../meta-raspberrypi
```
Next, add the relevant machine to the `conf/local.conf` file. Here's an example:
```
MACHINE = "raspberrypi-64"
```
Add an extra features that maybe necessary in `conf/local.conf`:
```
EXTRA_IMAGE_FEATURES ?= "debug-tweaks ssh-server-openssh"
```
Finally, build the image. Here's an example:
```
bitbake rpi-test-image
```
### Adding a Custom Layer
A custom layer can be created by:
```
# First creating a new build
source poky/oe-init-build-env <build>

# Creating a new layer
bitbake-layers create-layer ../meta-<layer>
```
This creates a file tree that looks like:
```
├── conf
│   └── layer.conf
├── COPYING.MIT
├── README
└── recipes-example
	└── example
		└── example_0.1.bb
```
Rename the example directories and recipes to suit the name of the layer.

To add a custom layer to the build:
```
bitbake-layers add-layer <layer>
```
And then modify the `conf/local.conf` file:
```
CORE_IMAGE_EXTRA_INSTALL += "<layer>"
# OR 
IMAGE_INSTALL_append = " layer"
```
### [[devtool]]
### [[Building Custom Linux Distros]]
### Runtime Package Management
Runtime package management refers to the process of fetching packets from a remote server and installing them on a target device. This is especially useful when working with frequent package updates, because it minimizes the number of times the target would need to be disrupted with repeated reboots.

Yocto supports different package formats (`rpm` and `ipk`) and different package managers (`dnf` and `opkg`). To select a package manager for the [[Building Custom Linux Distros|distro]], set the `PACKAGE_CLASSES` variable in the `conf/distro/<distro>.conf` file.
```
PACKAGE_CLASS ?= "package_ipk" # or "package_rpm"
```
And comment out the same variable in the build's `conf/local.conf` file. Enabling runtime package management also requires adding extra image features.
```
EXTRA_IMAGE_FEATURES ?= "package-management"
```
Finally, set the distro in `conf/local.conf`:
```
DISTRO = "<distro>"
```
#### Provisioning a Remote Package Server
The target device requires a remote package server in order to download the packages from. This can be done by:
```
# First adding a package to the build directory
# More packages can be added later
bitbake curl

# Populating the package index
bitbake package-index

# Starting the HTTP package server where package installer files exist
cd tmp-glibc/deploy/ipk
sudo python3 -m http.server --bing <host ip address> 80
```

On the target's end, edit the configuration of the package manager (usually located in `/etc/<package manager>`) to reflect the `<host ip address>`, and update the package manager.

As the project grows and new packages are added, the package manager can be used to find and upgrade all modified packages from the package server.
## [[Yocto Under the Hood]]