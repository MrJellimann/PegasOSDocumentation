# PegasOS - Technical Content

## 3.1.1 Project Goal

Our goal for this project is to create an operating system that can utilize the ARM 64-bit architecture that is native to the chip, since 32-bit systems are legacy support with the instruction set itself. Furthermore, we do not want this to be a fork of another operating system like many Linux distributions out there, but rather its own system. We want to create an operating system that has the essential programs that a user would need, plus some fun UCF-themed applications.

## 3.1.2 Objectives

1. To create a functioning operating system, called PegasOS, that uses purpose-built components by the PegasOS team.
2. To learn more about the design and process that goes into making systems software, and in particular operating systems.
3. To create a stable system that can be expanded upon, whether by individual contributors or future CECS Senior Design teams.
4. To fully release all code, documents, and related software components upon Senior Design’s completion, so that the operating system can be accessed, modified, and used by anyone who wishes to do so.

## 3.1.3 Specifications and Requirements

Must-Haves
1. PegasOS shall have a custom kernel, so called The Kingdom, that is not based on an existing kernel.
2. PegasOS shall have an attached shell terminal to the kernel, so called The Royal Messenger, from which the user can issue commands to interact with the operating system.
3. PegasOS shall have at least 20 built-in shell commands, so called The Decrees, which allow the user to interact with the various managers and systems within the kernel and operating system.
4. PegasOS shall have driver support for the components built into the board, so called Tim.
5. PegasOS shall have at least 5 pre-built programs that allow the user to have a degree of control within the shell terminal.
6. PegasOS shall have a File Manager, so named The Armory, from which the user can change directories, create and destroy directories, create and destroy files, copy and move files, read and write to files, and it shall manage the partitions of the disks so that files and directories are preserved between startups.
7. PegasOS shall have a Scheduler, so named The Warden, which will manage the processes that are instantiated within the operating system’s run, so that they all get execution time on the CPU.
8. PegasOS shall have an Input/Output Manager, so named The Gatekeeper, which shall handle all input from physical controllers, and disperse output to the appropriate devices that request it.
9. PegasOS shall have an Interrupt Handler, so named The Executioner, which shall handle the system’s interrupts, which includes the minimum of: Input/Output Interrupts, Timeout Interrupts, Insert Interrupts, Suspend Interrupts, ProcessMurder Interrupts.
10. PegasOS shall have a Page Manager, so named The Scribe, which shall transfer memory pages between Swap, RAM, DRAM, Hard Disk, Solid State, and other such storage devices.

Like-to-Haves

1. PegasOS may have an animated booting screen, so called The Gate, which includes a Knight of some description, that is not alike to the UCF mascot Knightro unless explicit permission is granted.
2. PegasOS may have a usable set of compilers, so called The Dragons.
3. PegasOS may have a set of non-mandatory programs included that will elevate the user’s experience with the operating system.
4. PegasOS may have a customizable shell terminal screen, with a dedicated section for terminal feed, past successful commands, and the current directory and file path, so called The Record.

# 3.2 Program and System Ideas/Decisions

Since this is a student project, we wanted to challenge ourselves but also not overwhelm our team. For this project, we need an operating system that has the essential components to any operating system while also having additional programs that the user can use. The following programs were agreed upon by the team as either required programs or ‘like-to-have’ programs, in a brainstorming session during a physical meeting. This list is neither comprehensive nor final.

## 3.2.1 Group Ideas

**File Manager**

This program allows for a user to manage files and folders. The user can perform the most basic operation on files such as creating, opening, renaming, moving, copying, deleting, and searching for files. They are also able to modify attributes, properties, and file permissions.

**Calculator**

A simple calculator that allows for basic operations such as addition, subtraction, multiplication, and division, with a programmer-friendly syntax.

3.2.2 Christopher’s Ideas

**GoKnightScreamer**

A simple program that reacts to the user typing “Go Knights” into the shell, printing out a banner with the words ‘Charge On!’ and potentially a sound effect to go with it

**ZorkClone**

One of the earliest interactive video games created by Tim Anderson, Marc Blank, David Lebling and Bruce Daniels in the late 1970s.

**RoguePort**

A custom port of some description of the classic game Rogue, a terminal-based randomly-generated turn-based hack-and-slash game.

## 3.2.3 Jacob’s Ideas

**Parking Lot Simulator**

A simulation of the average parking situation on campus at UCF, presented in a charming console format

**Hangman**

Another pen and paper game where one player tries to guess the word or phrase by suggesting a letter given a certain amount of guesses.

## 3.2.4 Giancarlo’s Ideas

**TicTacToe**

A pen and paper game where players take turns marking down X’s or O’s in a 3x3 grid. First player to get 3 marks in a vertical, horizontal, or diagonal row wins.

**Texteditor**

A text editor is a computer program that allows the user to read, edit, and save plain text files. The user can create text files to create text files or other programming language file types. It should be noted that this program is not a word processor that can create rich text format and is only a plain text editor.

## 3.2.5 Jacqueline’s Ideas

**Users & WhoAmI**

A system within the operating system that allows for user accounts that separate data, files, and programs with different permissions for system access.

**KnightQuestions**

Ask the shell any questions, and it will try to answer them.

**Tell Me a Story**

The user asks the operating system for a story and it chooses a set of small short stories randomly.

## 3.2.6 Kenny’s Ideas

**Display Settings**

A PegasOS feature that will allow the user to change the display settings. The user can modify settings such as resolution, text font, and text size. The user can also modify the terminal to have add or remove the file path window and previous command lists window.

# 3.3 Initial Research and Design

## 3.3.1 Design Overview

[Picture goes here]()

## 3.3.2 Cross Compiler

There are two main methods of building the kernel for our Raspberry Pis. We can either build the program locally on our Raspberry Pis or build using a cross-compiler. We opted to use the cross compiler since it will be much quicker to compile. The only downside to this is that it will require more time to set up. So why do we need a cross compiler? If you use the compiler that comes with your system, it wouldn’t know it’s compiling for something on a different platform. Otherwise a lot of unexpected things can happen because the compiler assumes your building code on your host operating system.

There are two ways that we can set up the cross-compiler. We can build the cross-compiler ourselves. That way we can use the latest GCC compiler on the system. We can also download prebuilt cross-compilers to skip setting up the build for the cross-compiler. It should be noted that this might not use the GCC compiler on the system. In this document, we will be going over both the setup.

## 3.3.3 Setting Up the Build for the Cross-Compiler

To begin building our cross-compiler we will need to know what platform we want to build on, otherwise, we will run into problems. Our platform will be the aarch64 platform since we want to utilize the 64-bit architecture. To prepare our cross-compiler we will need several dependencies to download to compile our compiler.

**GCC Compiler**

The GNU Compiler Collection is a compiler system produced by the GNU Project that supports various programming languages

**Binutils**

Also known as GNU Binary Utilities is a set of programming tools for managing binary programs, object files, libraries, profile data, and assembly source code.

**Bison**

A general-purpose parser generator that converts an annotated context-free grammar into a deterministic left-right parser or generalized left-right parser employing parser tables

**Flex**

A fast lexical analysis generator that is used for generating scanners, programs that recognize lexical patterns in text.

**GMP**

A free library for arbitrary precision arithmetic, operating on signed integers, rational numbers, and floating-point numbers.

**MPC**

Another library for the arithmetic of complex numbers with arbitrarily high precision.

**MPFR**

C library for multiple-precision floating-point computations with correct rounding


After downloading all the dependencies that are required, we will need to find a place to put the cross-compiler. It is generally recommended to install the cross-compiler into $HOME/opt/cross if you want to install it locally onto your computer. If you want to install it globally, a good directory would be /usr/local/cross/. It should be noted that this should work for Windows Subsystem for Linux.

## 3.3.4 Setting Up the Prebuilt Cross-Compiler

ARM has a developer section on their website where you can download the GNU Toolchain for your cross-compiler. For our purposes, we want to download the GNU-A Toolchain since our Raspberry Pi uses the Cortex-A processor. The specific GNU Toolchain we used for this project is the AArch64 ELF bare-metal target (aarch64-none-elf). Our reason for choosing this version is that our OS won’t be built on top of Linux, so we need to use the bare-metal target.

After downloading the prebuilt cross-compiler, create a directory to put your new cross-compiler into. It’s generally recommended not to install it into system directories. A good place to put your new cross compiler is in $HOME/opt/cross if you want for just your system. After creating the directory move files from the tar file into the destination you chose to put your cross-compiler.

Now you need to edit the path environment variable. For Linux, all you need to do is edit the \~/.profile found in the home directory. You can do this by using nano to edit the file. Scroll down to the bottom and paste this line of text export PATH=”$HOME/opt/cross/bin:$PATH”. Restart the computer and your system should be able to compile with ARM.

# 3.4 Build, Prototype, Test, and Release Plans

This section details our various testing methods and plans for testing the validity of the system. This is broken up into various sections in the order that the testing will be taking place.

## 3.4.1 Equipment

Testing equipment for PegasOS will consist of the following:

- Raspberry Pi 4
- 3.5A USB-C Power Adapter
- Micro HDMI to HDMI Cable
- USB Micro SD Card Reader
- Raspberry Pi 4 Heat Sinks
- HDMI-Compatible Display
- USB-to-TTL Cable

The SD Card, Power Adapter, HDMI Cable, and Raspberry Pi itself are the bare minimum to get the system up and running. For our initial testing, we need the USB-to-TTL Cable to get input readings from the GPIO pins to test our framebuffer. More information about this testing can be found in our Hardware section.

Once our framebuffer is working, and output is being printed to the screen, we can make use of the HDMI cable to test future iterations of the operating system on a traditional display.

## 3.4.2 Prototypes

As we develop the code base of PegasOS, first we will need to develop compartmentalized prototypes of various components within the system, before we integrate the prototype component into the main system. These prototypes will undergo unit tests of their own, before their integration into the rest of PegasOS, where it will undergo further unit tests to ensure that its integration was a success and has not affected the usage or operation of other aspects of the system.

## 3.4.3 Building

Building PegasOS will require the usage of the cross-compiler (as detailed previously), as building on the Raspberry Pi will take far too long to be feasible over multiple tests in a short timeframe, even with five separate units across the members of the team. Therefore, building PegasOS will involve compiling the system into ARM binaries that can be bundled and mounted onto the SD Card, which can then be inserted into the Raspberry Pi and run on the hardware.

If a user would like to build the system themselves, they will need to set up their own cross-compiler to target ARMv8 or aarch64. For references on how to do this, please visit our Cross-Compiler section for more information. If a user would like to create their own .iso file for the system, they are free to do so.

## 3.4.4 Testing

To test various aspects of the operating system, we will be designing unit tests for each major component of the system. This includes but is not limited to:

- The Kernel Scheduler
- The Kernel Input/Output Manager
- The Kernel Memory Manager
- The File System
- The PegasOS Shell
- The Kernel’s System Calls
- The System’s Program Execution

These unit tests will be recording empirical data, such as execution time from process invocation to scheduler insertion, computation time for scheduling algorithms, average CPU burst time, average Page Swaps per minute, average number of processes, and so on. Using these tests, we can determine if components or subsystems of the system are underperforming and evaluate methods to boost the performance of these components and subsystems. Using these tests, we can also create logs of system performance within the system itself, such that if the user wishes to know how the system is performing, the system can report its findings to the user.

## 3.4.5 Shipping/Release

Once the first official release of PegasOS is ready for public usage, we will release the source code as well as a mountable .img or .iso file to use in installing the operating system. The source code will be available for those who wish to inspect the code, compile the code themselves into an .img or .iso file for mounting on a Micro SD Card. For the convenience of other users, we will also include links to pre-compiled .img and .iso files for use in mounting PegasOS onto Micro SD Cards for installation.

At the current time of writing, there are no plans for a physical release of the operating system on Disks, Flash Drives, SD Cards, Micro SD Cards, or other media storage devices. As we are, at time of writing, not making a profit from this operating system, we cannot supply the funds necessary for a wide release of physical media containing the operating system. We therefore ask that the user take it upon themselves to mount, burn, or otherwise copy the files necessary to use the system onto a media storage device that they own.

The source code will be available on the project’s public GitHub repository, from which it can be cloned, downloaded, and forked. We will also provide links to downloads of the .img and .iso files that we have pre-built. These links will most likely be on the GitHub repository as well, and if they are hosted elsewhere we will include links to the new download host(s).

## 3.4.6 Contributions

As the project matures and is released to the public for usage, there will be individuals that will wish to contribute to the project. One of the goals for this project is to be a community resource into creating operating systems for the Raspberry Pi, and for these reasons we welcome all suggestions and contributions to the system that are beneficial to its development. If an individual or group would like to contribute to the project, they may ask for permission to become a contributor on the project GitHub. When asking to become a contributor to the project, please state your reasoning for why you wish to do so, and what aspects of the system you would like to work on. Upon being accepted as a contributor to the project, you may then make pull requests which can then be reviewed by members of the team for merging into the master branch.

## 3.4.7 Source Code Modification

As per the provisions granted under the GNU General Public License (version 3 and later), any individual or entity is allowed to download and modify the source code of PegasOS for their own personal usage. If they would like to submit their modifications as an official contribution to PegasOS, they may do so through the public GitHub page where they can apply to become a contributor to the project and have their submission reviewed by the team.

If a user, individual, or group would like to fork the project and make their own modifications to their fork, they are free to do so under the provisions of the GNU General Public License, versions 3 and later. They may also use their fork of the project as a reference in applying to become an official contributor to the project.

[Back - Legal, Ethical, and Technical](2_LEGAL_ETHICAL_TECHNICAL.md) | [Next - Hardware](4_HARDWARE.md) | 
[Design Document Home](DESIGN_DOCUMENT.md) | [Documentation Home](../README.md)