---
tags:
  - embeddedLinux
  - yocto
---
### Building Custom Linux Distros
A unique feature of Yocto is the ability to build custom Linux distros. A **distro layer** is a powerful abstraction that can be shared across multiple projects targeting different hardware. Creating a distro helps alter policies such as C library implementations and choice of package managers. Building a distro is a three step process:
1. Create a new distro layer. A new distro is created as a Yocto layer. 
```
# Create a build
source poky/oe-init-build-env <build>

# Create a new layer for the distro
bitbake-layers create-layer ../meta-<distro>

# Add layer to build
bitbake-layers add-layer ../meta-<distro>
```

2. Create a distro configuration file. For this, create. distro configuration file in the `meta-<distro>` directory at `conf/distro` with the name `<distro>.conf`. Set the required variables and any optional ones as necessary:
```
# Mandatory:
DISTRO_NAME =
DISTRO_VERSION =

# Optional
DISTRO_FEATURES = # Add softwaresupport for these features.
DISTRO_EXTRA_RDEPENDS = # Add these packages to all images.
DISTRO_EXTRA_RRECOMMENDS = # Add these packages if they exist.
TCLIBC = # Select this version of the C standard library.
```
Once this is done, use the `conf/local.conf` file to define any more variables.

3. Add recipes to the distro. This can be done using [[devtool]] or manually