# Layers and Recipes in Yocto
## Introduction
This will explain how to modify cross building, create and edit recipes, and build images using Yocto. Yocto uses layers and recipes to organize and customize the components of an embedded Linux system.

## Modifying Cross Building
Cross building is the process of compiling code for a different architecture than the one running the compiler. Yocto uses a toolchain to cross build the packages for the target system. The toolchain consists of a cross compiler, a linker, libraries, and other tools.

To modify cross building, we need to edit the recipe that specifies the toolchain configuration. A recipe is a file that describes how to build a package, including its dependencies, configuration options, patches, and installation steps. Yocto uses BitBake as the build engine to parse and execute the recipes.

In this example, we will use `ipk` packages instead of `rpm` packages for cross building. ipk is a lightweight package format that is compatible with opkg, a package manager for embedded systems. To use ipg packages, we need to edit the recipe that defines the package class variable. This variable determines which packaging backend to use for the target system.

The recipe file is located in `meta/classes/package.bbclass`. We need to change the line that says `PACKAGE_CLASSES ?= "package_rpm"` to `PACKAGE_CLASSES ?= "package_ipk"`. This will tell BitBake to use ipg as the packaging format for cross building.

## Creating a Layer
A layer is a collection of recipes and configuration files that define a specific functionality or customization for an embedded Linux system. Layers can be stacked on top of each other to create a complete system image. Yocto provides a set of core layers that contain the essential components for an embedded Linux system, such as the kernel, bootloader, libraries, and utilities.

To create a custom layer, we can use the `bitbake-layers` command. This command provides various options to manage and manipulate layers. For example, we can use the `create-layer` option to generate a template layer with the necessary files and directories.

In this example, we will create a layer called `meta-hello` that contains a simple hello world application. To create the layer, we need to run the following commands in the Yocto build directory:
```
$ mkdir meta-hello
$ bitbake-layers create-layer meta-hello
```
This will create a directory called `meta-hello` with the following structure:
```
meta-hello/
├── conf
│   └── layer.conf
└── README
```
The `conf/layer.conf` file contains the metadata for the layer, such as its name, priority, and dependencies. The README file contains some basic information about the layer and how to use it.

To add the layer to the build system, we need to edit the `conf/bblayers.conf` file in the build directory. This file lists the layers that are included in the build configuration. We need to add the path to our `meta-hello` layer to the BBLAYERS variable. 

For example:
```
BBLAYERS ?= " \
  /path/to/poky/meta \
  /path/to/poky/meta-poky \
  /path/to/poky/meta-yocto-bsp \
  /path/to/meta-hello \
  "
```
## Writing a Recipe
A recipe is a file that describes how to build a package, including its dependencies, configuration options, patches, and installation steps. Recipes are written in BitBake syntax, which is similar to Makefile syntax. A recipe file has a .bb extension and is usually named after the package it builds.

To write a recipe, we need to specify some mandatory variables, such as:

- `DESCRIPTION`: A brief summary of what the package does.-
- `LICENSE`: The license under which the package is distributed.
- `LIC_FILES_CHKSUM`: A checksum of the license file(s) in the source code.
- `SRC_URI`: The location of the source code or other files needed to build the package.
- `S`: The directory where BitBake will unpack and work on the source code.
- `do_compile`: A task that defines how to compile the source code.
- `do_install`: A task that defines how to install the compiled binaries and other files into the target system.

There are also many optional variables that can be used to customize various aspects of the package building process, such as:

- `DEPENDS`: A list of packages that are required to build the package.
- `RDEPENDS`: A list of packages that are required to run the package on the target system.
- `PV`: The version of the package.
- `PR`: The revision of the recipe.
- `BBCLASSEXTEND`: A list of variants that can be used to extend the recipe for different architectures or configurations.

In this example, we will write a recipe for a simple hello world application that uses `CMake` as the build system. CMake is a cross-platform tool that can generate makefiles or other build files from a configuration file called `CMakeLists.txt`. To simplify the recipe writing process, we can use the `cmake` class that provides some common functionality for CMake-based packages.

The recipe file is located in `meta-hello/recipes-hello/hello/hello_0.1.bb` and has the following content:
```
DESCRIPTION = "A simple hello world application"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://LICENSE;md5=302d50a6369f5f22efdb674db908167a"

SRC_URI = "git://github.com/user/hello.git;protocol=https"
S = "${WORKDIR}/git"

inherit cmake

do_install_append() {
    install -d ${D}${bindir}
    install -m 0755 ${B}/hello ${D}${bindir}
}

FILES_${PN} += "${bindir}/hello"

The recipe does the following:
```
- It sets the description, license, and license checksum for the package.
- It fetches the source code from a git repository using the SRC_URI variable.
- It sets the source directory to the git subdirectory in the work directory using the S variable.
- It inherits the cmake class, which defines the do_configure and do_compile tasks to run CMake and make commands respectively.
- It appends a custom installation step to the do_install task, which copies the hello binary to the bindir directory in the target system.
- It adds the hello binary to the list of files that belong to the package using the FILES_${PN} variable.
  
## Building the Image

To add our `hello world` application to the image, we need to create a custom image recipe that inherits from `core-image-minimal.bb` and adds our package to the list of installed packages. The image recipe file is located in `meta-hello/recipes-hello/images/example-image.bb` and has the following content:
```
require core-image-minimal.bb
```
```
DESCRIPTION = "A small image just containing a calculator"

IMAGE_FEATURES += "dev-pkgs"    # Pre setup group of settings & packages  "ssh-server-dropbear" installing features (dev-pkgs/tool chain/ openssh)
IMAGE_INSTALL += "bc hello"   # Add packages not recipes 
```
The recipe does the following:

- It requires (includes) the content of `core-image-minimal.bb`, which defines the base image configuration.
- It sets a custom description for the image.
- It adds some image features, such as development packages and tools, using the `IMAGE_FEATURES` variable.
- It adds our hello world package and another package called bc (a calculator program) to the list of
