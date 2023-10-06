---
tags:
  - embeddedLinux
  - yocto
---
### `devtool`
Creating recipes from scratch, as shown in [[The Yocto Project#Example]], can become tedious process as any project grows. A better way of working with package recipes is `devtool`, which is the cornerstone of Yocto's extensible SDK. By using `devtool`, one can create their own custom layer, and keep adding recipes to it. The recipes added could be new, patched from an existing recipe source, or an upgraded version of an upstream recipe source.

The basic workflow with `devtool` is:
```
# Create a build
source poky/oe-init-build-env <build>

# Set machine in conf/local.conf
MACHINE ?= "<machine>"

# Create custom layer which is where all recipes will be added to
bitbake-layers create-layer ../meta-<layer>

# Add new layer
bitbake-layers add-layer ../meta-<layer>
bitbake-layers show-layers # To confirm

# Build a deployable image which will be used for validation
devtool build-image <image_type> # Ex.: core-image-full-cmdline

########################
# Creating a new recipe
devtool add <recipe_url>

# Modifying an existing recipe
devtool modify <recipe>

# Upgrading an existing recipe
devtool upgrade <recipe> --version <new version>
########################

# View (and edit) recipe
devtool edit-recipe <recipe>

# Build recipe
devtool build bubblewrap

# Deploy to target
devtool deploy-target <recipe> <target>

# Once satisfied, undeploy target
devtool undeploy-target <recipe> <target>

# Merge work back into the layer:
devtool finsih -f <recipe> ../meta-<layer>

# Delete leftover sources from workspace
rm -rf workspace/sources/<recipe>
```