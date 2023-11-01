Package Dependencies and Splitting
This report describes how to add a new package, fix dependency errors, and split a package into multiple sub-packages using BitBake and devtool.

Adding a new package
To add a new package from a GitHub repository, we can use the following command:

devtool add libanswer https://github.com/LetoThe2nd/libanswer # Under build dir

This will create a recipe for the libanswer package and fetch the source code from the GitHub repository. We can then build the package using BitBake:

bitbake libanswer

To test the package, we can run QEMU with an example image that contains the libanswer package:

runqemu qemuarm example-image nographic

The example-image is from the last session. Inside QEMU, we can run the ask command, which is provided by the libanswer package:

ask # Should add new package into image first
    # Actually an error is expected because image was not rebuilt

However, this will result in an error, because we have not added the libanswer package into the image yet. To do that, we need to edit the example-image.bb file under meta-hello/recipes-example/images and append libanswer to the IMAGE_INSTALL variable:

vim example-image.bb # Under meta-hello/recipes-example/images
                     # Make update of IMAGE_INSTALL += “libanswer”
                     
require recipes-core/images/core-image-minimal.bb

DESCRIPTION = "A small image just containing a calculator"

#IMAGE_FEATURES += "ssh-server-dropbear"
IMAGE_INSTALL +="libanswer"

Then, we need to rebuild the image using BitBake:

bitbake example-image

And run QEMU again with the updated image:

runqemu qemuarm example-image nographic

Inside QEMU, we can run the ask command again:

ask    # The expected error appears, a runtime dependency
the answer is
sh: bc: not found

This time, we get a different error, which indicates that there is a runtime dependency on bc, which is not installed in the image.

Fixing dependency errors
To fix the runtime dependency error, we need to edit the recipe for the libanswer package and add bc to the RDEPENDS_${PN} variable, which specifies the runtime dependencies for the package:

devtool edit-recipe libanswer # Under build dir
                              # Right after line of DEPENDS = “boost”
                              # Add RDEPENDS_${PN} = “bc”
                              # Note that DEPENDS are prepared per recipe and RDEPENDS are per package

Then, we need to rebuild the image using BitBake:

bitbake example-image

And run QEMU again with the updated image:

runqemu qemuarm example-image nographic

Inside QEMU, we can run the ask command again:

ask # Should work now
the answer is 42.0

This time, it works as expected.

To create a build dependency error, we can comment out the DEPENDS = “boost” line in the recipe for the libanswer package, which specifies that the package depends on boost at build time:

devtool edit-recipe libanswer # Comment out DEPENDS = “boost”

Then, we can try to clean and rebuild the package using BitBake:

bitbake -c cleansstate libanswer && bitbake libanswer # Raise a dependency error in configure step

This will result in an error during the configure step, because boost is not available.

To fix the error, we can uncomment out the DEPENDS = “boost” line in the recipe for the libanswer package:

devtool edit-recipe libanswer # Uncomment out DEPENDS = “boost”

Then, we can rebuild the package using BitBake:

bitbake libanswer 

This time, it should succeed.

Splitting a package
To split a package into multiple sub-packages, we can use the PACKAGES and FILES variables in the recipe. The PACKAGES variable tells BitBake what sub-packages to create from the recipe. The FILES variable tells BitBake what files to install into each sub-package.

For example, if we want to create a new sub-package called libanswer-example, which contains only the /usr/bin/ask binary file, we can edit the recipe for the libanswer package and add these lines:

devtool edit-recipe libanswer # Right after inherit “cmake”
                              # Add line PACKAGES =+ “${PN}-example”
                              # Right after PACKAGES =+ “${PN}-example”
                              # Add line FILES_${PN}-example = “/usr/bin/ask”

Then, we can rebuild the package using BitBake:

bitbake libanswer 

To check the sub-packages created by the recipe, we can use the -e option of BitBake to print the environment variables:

bitbake -e libanswer | less  # Check variable PACKAGES should contain libanswer-example in addition to canonical packges

We can also check the package format used by the image by looking at the conf/local.conf file:

less conf/local.conf # Check what package format is used e.g. rpm

Then, we can rebuild the image using BitBake:

bitbake example-image

And run QEMU again with the updated image:

runqemu qemuarm example-image nographic

Inside QEMU, we can run the ask command again:

ask # Still work, not as expected
the answer is 42.0

However, this is not what we expected, because we have moved the /usr/bin/ask file to the libanswer-example sub-package, which is not installed in the image. To fix this, we need to edit the example-image.bb file and append libanswer-example to the IMAGE_INSTALL variable:

vim example-image.bb # Under dir meta-live/recipes-example/images
                     # Make an update IMAGE_INSTALL += “libanswer libanswer-example”

Then, we need to rebuild the image using BitBake:

bitbake example-image

And run QEMU again with the updated image:

runqemu qemuarm example-image nographic

Inside QEMU, we can run the ask command again:

ask # Work again
the answer is 42.0

This time, it works as expected.

To show the recipe and the variables for the libanswer package, we can use these commands:

devtool edit-recipe libanswer # Show the recipe
bitbake -e libanswer | less # Check variables FILES_libanswer, FILES_libanswer-dev, FILES_libanswer-example, etc

Why splitting?
The reason for splitting a package into multiple sub-packages is to reduce the size and complexity of the package and to allow more flexibility and control over what files are installed in the image. For example, if we only want to install the library file /usr/lib/libanswer.so.0.0.1, we can install only the libanswer sub-package, without installing any other files related to the package, such as documentation or examples. This can save disk space and memory on the target device.

Splitting a package can be done by using two variables: PACKAGES and FILES. The PACKAGES variable tells BitBake what sub-packages to create from the recipe. The FILES variable tells BitBake what files to install into each sub-package. By default, BitBake creates four canonical sub-packages for each recipe: ${PN}, ${PN}-dev, ${PN}-dbg, and ${PN}-staticdev. The ${PN} sub-package contains the main files for the package. The ${PN}-dev sub-package contains files for development, such as headers and libraries. The ${PN}-dbg sub-package contains files for debugging, such as symbols and source code. The ${PN}-staticdev sub-package contains static libraries for linking. We can also create custom sub-packages by appending them to the PACKAGES variable and specifying their contents using the FILES variable.

Conclusion
This report has shown how to add a new package, fix dependency errors, and split a package into multiple sub-packages using BitBake and devtool. These are useful skills for developing and maintaining software packages for embedded systems using Yocto Project.
