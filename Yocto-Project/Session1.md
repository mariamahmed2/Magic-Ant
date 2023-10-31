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
