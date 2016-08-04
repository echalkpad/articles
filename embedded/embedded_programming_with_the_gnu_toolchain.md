# Embedded Programming with the GNU Toolchain

[Original URL](http://www.bravegnu.org/gnu-eprog/)

> The GNU toolchain is increasingly being used for deeply embedded software development. This type of software development is also called standalone C programming and bare metal C programming....

The GNU toolchain is increasingly being used for deeply embedded software development. This type of software development is also called standalone C programming and bare metal C programming. Standalone C programming brings along with it new problems, and dealing with them requires a deeper understanding of the GNU toolchain. The GNU toolchain's manuals provide excellent information on the toolchain, but from the perspective of the toolchain, rather than the perspective of the problem. Well, that is how manuals are supposed to be written anyway. The result is that the answers to common problems are scattered all over, and new users of the GNU toolchain are left baffled.

This tutorial attempts to bridge the gap by explaining the tools from the perspective of the problem. Hopefully, this should enable more people to use the GNU toolchain for their embedded projects.

For the purpose of this tutorial, an ARM based embedded system is emulated using Qemu. With this you can learn the GNU toolchain from the comforts of your desktop, without having to invest on hardware. This tutorial itself does not teach the ARM instruction set. It is supposed to be used with other books and on-line tutorials like:

But for the convenience of the reader, frequently used ARM instructions are listed in the appendix.
