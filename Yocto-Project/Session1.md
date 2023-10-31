# Yocto Project
## Introduction
The Yocto Project is an open-source collaboration project that provides templates, tools and methods to help you create custom Linux-based systems for embedded products regardless of the hardware architecture. It is not an embedded Linux distribution, but a set of tools that allow you to create your own distribution.

## Pokey
Pokey is a reference distribution that is included in the Yocto Project. It takes tools from the Yocto Project, an embedded Linux kernel, a small set of user-space applications and libraries, and some proprietary source code to create a minimal image that can run on various hardware platforms.

### How to use Yocto Project and Pokey
To use the Yocto Project and Pokey, you need to set up a build directory where all the configuration files, source code and output files will be stored. You can do this by following these steps:

1. Source the `oe-init-build-env` script into the current shell session. This script sets up the required environment variables and creates a build directory.
2. Edit the `conf/local.conf` file in the build directory. This file contains various settings that affect the build process, such as the machine type, the package format, the image features, etc.
3. Change the value of `MACHINE` from `qemux86-64` to `qemuarm` to specify that you want to build an image for an ARM-based device.
4. Change the value of `PACKAGE_CLASSES` from `package_rpm` to `package_ipk` to specify that you want to use the IPK package format instead of the RPM package format.

### Building and running an image
Building an image using the Yocto Project and Pokey can take a lot of processor power and time, depending on your hardware and network speed. To build an image, you can use the `bitbake` command with the name of the image recipe as an argument. For example, to build a minimal image, you can use this command:

```
bitbake core-image-minimal
```

This command will download all the required source code, apply patches, compile and install the packages, and create an image file in the `build/tmp/deploy/images/qemuarm` directory.

To run the image on a QEMU emulator, you can use the `runqemu` command with some options. For example, to run the minimal image on a QEMU ARM device without graphical output and with user-mode networking, you can use this command:

```
runqemu qemuarm nographic slirp
```
This command will launch QEMU with the appropriate parameters and boot the image. You can log in as root without a password. You can use some commands to check the system information, such as `uname -a`. To shut down the system, you can use `poweroff`.

## Bitbake
Bitbake is a tool that allows users to interact with the build process of embedded Linux systems. It provides an interface for creating, configuring, and compiling different components of the system, such as the kernel, the bootloader, the root filesystem, and the applications. Bitbake is based on the concept of recipes, which are files that describe how to build a specific package or component. Recipes can include information such as dependencies, configuration options, patches, and installation instructions.

### Bitbake without Metadata
Bitbake itself does not include any metadata, which are the layers that contain the recipes and other files needed to build a system. To use Bitbake, users need to obtain the metadata from a source that provides them. One such source is Poky, which is a reference distribution of the Yocto Project. Poky includes five layers of metadata:

- meta: This layer contains the core recipes and classes that are essential for building any system with Bitbake.
- meta-poky: This layer contains the configuration files and branding for the Poky distribution.
- meta-yocto-bsp: This layer contains the recipes and configuration files for the supported boards and emulators in the Yocto Project.
- meta-oe: This layer contains the recipes for various open source packages and applications that are not part of the core layer.
- meta-networking: This layer contains the recipes for networking-related packages and applications.
  
### Bitbake vs Buildroot
Bitbake and Buildroot are both tools that can be used to build embedded Linux systems, but they have some differences in their approach and features. Some of the main differences are:

- Bitbake is more flexible and modular than Buildroot, as it allows users to customize and extend their system by adding or removing layers of metadata. Buildroot, on the other hand, has a fixed set of packages and configuration options that are defined in its source code.
- Bitbake supports incremental builds, which means that it only rebuilds the components that have changed since the last build. - Buildroot does not support incremental builds, which means that it rebuilds everything from scratch every time.
- Bitbake supports cross-compilation, which means that it can build a system for a different architecture than the one it is running on. Buildroot also supports cross-compilation, but it requires users to install a toolchain for the target architecture before building.
- Bitbake supports shared state cache, which means that it can reuse the output of previous builds to speed up the current build. - Buildroot does not support shared state cache, which means that it always starts from scratch.
- Bitbake supports package management, which means that it can create and install packages for the target system using various formats, such as ipk, rpm, or deb. Buildroot does not support package management, which means that it only creates a single image file for the target system.
  
### How to Add Something to the Build
One of the ways to add something to the build using Bitbake is to create a custom image recipe. An image recipe is a file that defines what components and packages are included in the final image file for the target system. To create a custom image recipe, users can follow these steps:

= Create a new file in the `meta-custom/recipes-core/images` directory (or any other directory under a custom layer), with a name like custom-image.bb.
- Inherit from an existing image recipe class, such as `core-image-minimal` or `core-image-full-cmdline`, by adding a line like inherit `core-image-minimal` at the beginning of the file.
- Add any extra packages or components that are needed for the custom image by using variables like `IMAGE_INSTALL` or `CORE_IMAGE_EXTRA_INSTALL`. For example, to add the bc package (a calculator utility), users can add a line like `CORE_IMAGE_EXTRA_INSTALL += "bc"` in the file.
- Run Bitbake with the name of the custom image recipe as an argument, such as bitbake custom-image. This will create an image file in the `tmp/deploy/images` directory (or any other directory specified by `DEPLOY_DIR_IMAGE` variable).
