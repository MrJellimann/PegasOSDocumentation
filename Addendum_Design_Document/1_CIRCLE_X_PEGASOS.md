# 1.0.0 Circle x PegasOS

As was alluded to in the Overview, the adoption of Circle for the PegasOS project resulted in a number of changes that affected the structure of the entire project, forcing us to rebase the project’s code from C to C++. Most, if not all, of the original code was scrapped in the process. The specific nature of the changes will be discussed later in this section, but the beginning of this section will be used to describe what Circle is, what Circle does for PegasOS, and a brief overview of its features.

## 1.1.0 Circle

> “Circle is a C++ bare metal programming environment for the Raspberry Pi. It should be usable on all existing models (tested on model A+, B, B+, on Raspberry Pi 2, 3, 4 and on Raspberry Pi Zero). It provides several ready-tested C++ classes and add-on libraries, which can be used to control different hardware features of the Raspberry Pi. Together with Circle there are delivered several sample programs, which demonstrate the use of its classes. Circle can be used to create 32-bit or 64-bit bare metal applications.”
> - Rene Stange, Circle GitHub


Most of the discussion about Circle will revolve around Release 42 (aka Step 42) which is the release of Circle that PegasOS is being developed with. Since the adoption of Circle by PegasOS, Circle has moved on to Release 43.1.


As the author stated, Circle is a bare metal programming environment on the Raspberry Pi, though this really sells it short. Circle’s programming environment contains a variety of components that make up a basic real-time system on the Pi, that is not reliant on another kernel or other drivers. This is quite significant, as it is not beholden to the Linux drivers or kernel, though this means that working with the Circle environment means doing it the “Circle way”.


Circle contains its own kernel, and the Circle system can have a scheduler component, memory management, file system, and any combination of hardware drivers for both USB and on-board hardware. Some of these systems have limitations that we will discuss shortly.

## 1.1.1 Circle File Systems

Circle contains support for the FAT-family of file systems, from FAT12 all the way up to exFAT in an addon. We have attempted to get the exFAT addon to work with PegasOS, however this has proven to be extremely difficult and has not cooperated well so far. As such we are using Circle’s implementation of FAT32, which means it comes with all of the standard operations you come to expect from a file system, with the traditional memory limits of FAT32.

## 1.1.2 Circle Memory Management

Circle contains definitions and infrastructure for memory paging like in a real operating system, however after confirming with the author we know that Circle does not contain true virtualization of the addressable space. Instead, because the ARM CPU requires a memory management unit to be instantiated and running on the chip in order to continue booting, Circle fulfils the requirement but then allows the system and its tasks to use a 1-to-1 mapping of the given page, and allocates the page accordingly.


As far as we can tell, a single page is allocated(?) for the memory management system. For a traditional or even a modern OS this would be incredibly small, however Circle is using 64KB pages which makes sense given the original intent is to provide an environment for bare metal and by extension real-time applications. These are not applications that are typically bogged down with the frequent swapping in and out of hard disk space that OS’s must put up with, and these applications do not typically have entire programs or at the very least not large programs that require multiple pages of memory to run. This is something that we are obviously addressing in PegasOS, as we need a proper page-swapping system in place with ideally typical OS page sizes of around 4KB to make the OS more standardized.


On that note however, Circle does include different functionality for its memory system based on whether you are building for a 32-bit application or a 64-bit application. 64-bit applications unlock the 3rd level of the page tables, whereas 32-bit applications may only utilize up to the 2nd level of page tables.

## 1.1.3 Circle Scheduler

Circle’s scheduler is a simple First-In-First-Out Queue that uses the Circle CTask class to execute tasks sent to the scheduler for execution. The CTask class is quite powerful, in that any class can be run as a task on the system if it is a child of the CTask class. As such, CTask dictates how tasks are actually run on the system itself.


Since the scheduler has no weighting system or any real methods to prevent tasks from being starved in extreme conditions, the scheduler is lacking ‘modern sensibility’ though as we said for the Memory Management system, this sophistication is not really required for Circle’s intended purposes.


This is one of the parts of Circle that we are changing drastically for PegasOS, as for PegasOS to be closer to a true OS we need to have scheduling that can handle a greater variety of tasks and task volume.

## 1.1.4 Circle USB Drivers

Circle includes a large number of USB drivers for various devices, ensuring that Circle has great compatibility with many generic USB device types. Some of these types include:


* USB Bluetooth
* USB Ethernet
* USB Gamepads
* USB Mass Storage Devices
* USB Midi Devices
* USB Mouse
* USB Printer


Using these drivers is fairly straightforward; include the desired USB device header in the kernel and begin using the functions of the device driver.

## 1.1.5 Other Circle Features and Components

Due to the nature of Circle being a standalone bare-metal environment, some of the benefits of things like the Standard Library are harder to come by without the appropriate system calls that simply do not exist on a bare-metal system. As such, Circle actually includes definitions for additional data types, as well as a number of string manipulation functions that we’ve come to expect to be able to use like strcpy() or  strcmp() for example. Circle also has its own implementation of malloc() which allows for the typical dynamic memory allocation seen in C/C++. Circle also supports variable arguments for function calls, spinlocking cores, and much more.


Circle also includes additional drivers and system components in its included /addon directory.

## 1.2.0 Circle in PegasOS

Circle provided the groundwork for us to be able to work on the system at large, instead of spending a long amount of time on developing our own hardware drivers for the given devices. While Circle does include a basic environment to get code running on the Pi, the environment itself is as minimal as possible to ensure that it contributes as little overhead as possible. Circle even includes a template for a blank kernel so that the developer(s) building off of Circle can take exactly what they want for their needs. Our needs are much larger in scope than Circle anticipates, as such we have replaced components where necessary and extended others to increase their capability.

## 1.2.1 PegasOS Usage of Circle

PegasOS only takes a few things of importance from Circle, apart from the drivers needed to run the hardware on the system. These are the major components, as small functions and other pieces of code or structure are taken from classes, components, or paradigms from within Circle.


The first of these is the *kernel structure*. Using the structure of the sample kernels that Circle has given us, we have based our kernel design around the main functionality that Circle expects from its kernels, and have extended the capabilities and functionality of the kernel from there.


The second being the *Circle Scheduler*. Circle’s scheduler on its own is rather basic - it operates on a simple queue that switches tasks every few seconds to the next one. We have taken this scheduler as the base and extended it with weights to allow certain tasks to have priority over others. Current tasks can also be viewed via command line.


The third being Circle’s *Memory Management Unit*. Circle’s MMU is quite bare-bones on its own - its purpose within Circle is quite literally to appease the ARM CPU at the heart of the Pi, and from there Circle effectively uses a one-to-one memory mapping onto the actual memory space. PegasOS’s modifications allow for this MMU to use actual virtualization on this memory, so that multiple programs can run on the system and have access to their own space in memory - much like any other modern OS.


The fourth being Circle’s implementation of *FAT32*. Circle includes an implementation of FAT32 that works on the SD card that Circle is on, and allows for real-time access of files as one would expect from FAT32. PegasOS leaves the implementation of Circle’s FAT32 basically unchanged, however the user is able to interact with FAT32 through the shell or CLI.


The fifth, and perhaps one of the most important was the USB keyboard driver. Without that, it would be very hard to use anything on the system, for obvious reasons! As such we’ve allowed the USB keyboard drivers to act exactly how they would expect to within Circle’s environment, taking care not to edit the driver code itself nor modify any internal settings to the driver (that cannot obviously be reverted or damage the system’s integrity at run time).

## 1.2.2 PegasOS Original Components

The major component that was built from scratch was PegasOS’s shell, or its Command Line Interface (CLI). This allows the user to interact with the system through a number of built-in commands, of which are discussed in [Section 3.1.0](3_PEGASOS_FIRST_RELEASE.md) of this document. This is also the primary method in which the user is able to interact with data on the system through the file system, FAT32.


PegasOS’s shell connects with Circle through a number of internal classes and functions from within the kernel and its dependencies to ensure that writing to the screen is done as intended, and that there is minimal delay between writing and reading input from the user’s keyboard. PegasOS also makes occasional use of Circle’s logger for some shell commands.

[Back - 0.1.1 Preface, 0.1.2 Overview](0_PREFACE_OVERVIEW.md) | [Next - 2.0.0 PegasOS Cuts and Edits](2_PEGASOS_CUTS_EDITS.md) | 
[Design Document Home](ADD_DESIGN_DOCUMENT.md) | [Documentation Home](../README.md)