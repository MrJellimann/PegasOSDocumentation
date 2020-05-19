# PegasOS - Hardware

![](/images/4_image1.png "4 - Hardware")

# 4.1 Raspberry Pi Hardware

The Raspberry Pis’ internal hardware has evolved through several generations. All current Raspberry Pis have a Broadcom SoC (System on Chip) that has an ARM integrated CPU (Central Processing chip) and GPU (Graphics Processing Unit). The Raspberry Pi 4 has an 1.5 GHz 64-bit quad-core processor with a Broadcom VideoCORE VI at 500 MHz. The Raspberry Pi 4 is also equipped with on-board 802.11ac Wifi, Bluetooth 5.0, gigabit Ethernet, two USB 2.0 ports, two USB 3.0 ports and dual Micro HDMI ports for dual monitor support.

A system on a chip is essentially all of the parts of a computer in a single chip. This is great for things like the Pi 4 because it uses up much less power than multi-chip designs with similar functionality. The Pi 4 uses a Broadcom BCM 2711 SoC.It uses an ARM 72 core, making it more powerful than its predecessors. Furthermore, it’s GPU is improved to process input and output faster. This SoC is capable of addressing more memory, continues to use heat spreading technology, and a natively attached Ethernet controller.

![Raspberry Pi Diagram](/images/4_image2.png "Raspberry Pi 4")

Credit to: Raspberry Pi Company

## 4.1.1 General-Purpose Input/Output (GPIO)

One feature of the Raspberry Pi is the GPIO (General-Purpose Input/Output) pins along the top edge of the board. Most Raspberry Pi boards have a 40-pin GPIO header except Pi 1 Model B+ (2014). Each GPIO pin on the board is used for a wide range of purposes. Here is a diagram showing how the GPIO pins are assigned.

![GPIO Diagram](/images/4_image3.png "Raspberry Pi GPIO Diagram")

![Pin Diagram](/images/4_image4.png "Raspberry Pi Pin Diagram")

Credit to: Raspberry Pi Company

## 4.1.2 Bootloaders

Before we begin developing the kernel for the OS, it would be best to get a better understanding of how the Raspberry Pi works by learning more about the booting sequence. This way, we will know how to boot up the kernel properly.

Not every iteration of the Raspberry Pi boots up the same way. Before the Pi boots up, power must be applied to the Raspberry Pi. Once the power is delivered, the Pi will begin to boot the GPU to execute the first stage boot loader. This is where we begin to see the differences in the booting sequences between the older Raspberry Pi’s (Versions 1, 2, 3) and the Pi 4.

In the older Raspberry Pi’s, the first-stage bootloader mounts to the SD card and loads the second stage bootloader (bootcode.bin). It then loads it into the SoC’s (system on chip) L2 cache. The second stage loader would then load and execute the `start*.elf` files.

The Pi 4 does a different method, because it loads the code contained in an EEPROM, which is in the SoC, and executes it. The EEPROM is an electrically erasable programmable read-only memory, that is nonvolatile memory integrated into many electronic devices to store relatively small amounts of data. The purpose of this EEPROM boot code is to load the GPU and to execute the next boot stage. It serves the same purpose as the bootcode.bin but instead loads the code in the EEPROM instead of the SD card.

The start.elf files reads the system configurations, loads the kernel image, reads the kernel parameters, and releases the reset signal on the arm to boot up the kernel. It also should be noted that prior to October 2012, the 2nd stage bootloader would load a 3rd stage bootloader called the loader.bin file. The loader.bin file handles the elf files, but has since then been removed in the booting process because the bootcode.bin file has been updated to load the elf files instead.

[Image Goes Here]()

## 4.1.3 EEPROM

The EEPROM is an electrically erasable programmable read-only memory. It is non-volatile memory, which means that it does not need constant power to retain data. EEPROM is commonly used in microcontrollers and smartcards.

The EEPROM was included for many reasons. First, the boot sequence for the Pi 4 is more complicated, so there is more room for error in the read-only memory on the System on a Chip. After further research we noticed that most open-source kernels for the Pi 4 went straight to writing the kernel. We found the EEPROM is constantly being updated, with some current updates bringing down the processor temperature.

The EEPROM is not easily editable. As it stands, there is some sort of bootloader inside of the EEPROM, and is not something you can overwrite without flashing the EEPROM. This is a bad idea for multiple reasons, one being that the EEPROM has only so many times it can be overwritten, and also flashing the EEPROM would need some additional hardware.

However, there is also a workaround for the EEPROM. In the case the EEPROM gets corrupted, Raspberry Pi has a recovery.bin file you can use on it to reset the EEPROM. Updates can be made to the bootloader using that. However, it is easier said than done, because although the EEPROM has no write restrictions it requires very specific formatting to get anything to work properly.

Since the goal of this project is to install an OS out of the box of any Pi 4, we have decided to keep the current code instead of EEPROM. This will reduce the amount of hardware we will need for the project, which brings down our costs. Furthermore, since the EEPROM is constantly being updated, any rewrites we would make would just be overridden the next time someone updates it.

This is not an end-all to any sort of customization, however. The EEPROM still has a readily available config file. From here, we will do most of our low-level edits to it to prevent any previously stated roadblocks.

[Image Goes Here]()

## 4.1.4 Basic Kernel Test

With this in mind, to test out our theory about the booting sequence we tried loading a basic Kernel onto it. This presented some challenges. First off, many setups we were using were configured to take the Linux kernel specifically. Since we are writing our own kernel, we could not just load a Linux-based kernel. As such, we found a cross compiler from the ARM company that did not specifically need a Linux kernel. We tried to get it to load to the screen but then found nothing would show first. After that, we realized that we needed a frame buffer.

A frame buffer is a portion of memory that contains the frames that are sent to the screen. To print something to the screen, we would need to convert the I/O from the chip to something that can be readily read by the screen. However, for a computer to find a frame buffer, it first needs a mailbox peripheral to find it from.

To test our kernel without programming a framebuffer, we bought a USB-to-TTL cable that will send input into the serial console. Following the picture below, the black lead needs to attach to the GND pin, the white lead to the TXD pin, and green lead to the RXD pin.

[Image Goes Here]()

Credit to: Adafruit

After finishing connecting the serial cable to the Raspberry Pi, plug in the USB to your computer. Your computer will need a serial console, so I would recommend using a program called PuTTY. Change your connection type to serial and put COM3 as your serial line and 115200 for your speed. Now you need to flash your SD Card for the Raspberry Pi. To do this, download any Raspberry Pi operating system and flash onto the SD card. Once that is done, replace the OS’s .img file and put your compiled .img file onto the SD card. All that’s left is to insert your SD card in the Raspberry Pi and power it on. You should be getting a “Hello World” on your serial terminal.

## 4.1.5 Drivers

Our PegasOS project will require some basic drivers in order for our operating system to function properly. A keyboard, mouse, and sound output driver are essential components for this project. Our keyboard drivers will enable the user to type inputs into their keyboard. The OS also needs a mouse driver for the user to click on the screen. Finally, we will need sound output to play the go knights scream at the startup. We originally thought of making the drivers ourselves since it will give us a learning experience on how to make drivers and give us a better understanding of our Operating System. Unfortunately, there is not a lot of information on how to make USB drivers for the Raspberry Pi. For the sake of our time and sanity, we will be using an open-source repository for our USB drivers. This will prevent us from having to reinvent the wheel and also gives us more time to work on more important matters with the hardware.

https://github.com/rsta2/uspi

This URL link is the USB driver repository we will be using for our operating system. It’s called USPi and it’s a bare metal USB driver for the Raspberry Pi in C. It was originally ported from the Circle USB library which was written in C++. The functional drivers support USB keyboard, mice, MIDI instruments, gamepads, USB flash drives, and on-board Ethernet controllers. This repository also comes with an environment library that provides all required functions to get USPi running. In order to use this repository, we will have to make some changes in the configuration file in `include/uspios.h`

## 4.1.6 I/O

#### GPIO Registers

| Address   | Field Name | Description                          | Size | Read/Write |
| --------- | ---------- | ------------------------------------ | ---- | ---------- |
| 0xXX20000 | GPFSEL0    | GPIO Function Select 0               | 32   | R/W        |
| 0xXX20004 | GPFSEL1    | GPIO Function Select 1               | 32   | R/W        |
| 0xXX20008 | GPFSEL2    | GPIO Function Select 2               | 32   | R/W        |
| 0xXX2000C | GPFSEL3    | GPIO Function Select 3               | 32   | R/W        |
| 0xXX20010 | GPFSEL4    | GPIO Function Select 4               | 32   | R/W        |
| 0xXX20014 | GPFSEL5    | GPIO Function Select 5               | 32   | R/W        |
| 0xXX2001C | GPSET0     | GPIO Pin Output Set 0                | 32   | W          |
| 0xXX20020 | GPSET1     | GPIO Pin Output Set 1                | 32   | W          |
| 0xXX20028 | GPCLR0     | GPIO Pin Output Clear 0              | 32   | W          |
| 0xXX20034 | GPCLR1     | GPIO Pin Output Clear 1              | 32   | W          |
| 0xXX20038 | GPLEV0     | GPIO Pin Level 0                     | 32   | R          |
| 0xXX20040 | GPLEV1     | GPIO Pin Level 1                     | 32   | R          |
| 0xXX20044 | GPEDS0     | GPIO Pin Event Detect Status 0       | 32   | R/W        |
| 0xXX20064 | GPEDS1     | GPIO Pin Event Detect Status 1       | 32   | R/W        |
| 0xXX20068 | GPREN0     | GPIO Pin Rising Edge Detect Enable 0 | 32   | R/W        |
| 0xXX200?? | GPREN1     | GPIO Pin Rising Edge Detect Enable 1 | 32   | R/W        |
| 0xXX20098 | GPPUDCLK0  | GPIO Pin Pull-up/down Enable Clock 0 | 32   | R/W        |
| 0xXX2009C | GPPUDCLK1  | GPIO Pin Pull-up/down Enable Clock 1 | 32   | R/W        |

For more information about each register, please see the BCM ARM Peripheral Manual.

#### UART Address Map

| Address Offset | Register Name | Description                       | Size |
| -------------- | ------------- | --------------------------------- | ---- |
| 0x0            | DR            | Data Register                     | 32   |
| 0x4            | RSRECR        | Flag Register                     | 32   |
| 0x18           | FR            | Flag Register                     | 32   |
| 0x20           | ILPR          | No Use                            | 32   |
| 0x24           | IBRD          | Integer Baud Rate                 | 32   |
| 0x28           | FBRD          | Fractional Baud Rate Divisor      | 32   |
| 0x2c           | LCRH          | Line Control Register             | 32   |
| 0x30           | CR            | Control Register                  | 32   |
| 0x34           | IFLS          | Interrupt Mask Set Clear Register | 32   |
| 0x38           | IMSC          | Raw Interrupt Status Register     | 32   |
| 0x3c           | RIS           | Interrupt Clear Register          | 32   |
| 0x40           | MIS           | DMA Control Register              | 32   |
| 0x44           | ICR           | Test Control Register             | 32   |
| 0x48           | DMACR         | DMA Control Register              | 32   |
| 0x80           | ITCR          | DMA                               | 32   |
| 0x84           | ITIP          | Integration Test Input Register   | 32   |
| 0x88           | ITOP          | Integration Test Output Register  | 32   |
| 0x8c           | TDR           | Test Data Register                | 32   |

For more information about each register, please see the BCM ARM Peripheral Manual.

There are a couple of pressing matters to consider with the I/O. These include the Page manager, the TLB, and how ARM registers are set up. Without these components, we cannot hope to get even the simplest of algorithms written for our OS, as they are crucial to interact with the hardware.

## 4.1.7 Framebuffer

To display on the screen without any special hardware, we need a framebuffer. A framebuffer is a portion of RAM that uses a bitmap to feed to the video display. It is a piece of data that both the GPU and CPU use. Most modern video cards have some sort of device to convert bitmap to video signal, but there is a portion of code that needs to be written to feed the data to it. The data is written as RGB, and then is fed to the GPU.

To initialize the framebuffer, you need to print a fully black screen. Then, you need to write pixels individually to the screen. Each character has a different bitmap. After this data is written in a bitmap, it is then fed to a mailbox peripheral to be fed to the hardware.

## 4.1.8 Mailbox Peripheral

The mailbox peripheral is how the ARM GPU talks to the Video GPU. It is the closest to bare metal we will get in terms of the display. The mailbox has three registers: read, write, and status. The Read register is at offset 0x00 from the mailbox base address and it facilitates reading messages from the GPU. It’s made up of 4 lower bits that say which channel the message is from and 28 high bits are our data. The Status register is at offset 0x18 from the mailbox base address and it tells you whether the read register is empty or tells you whether the write register is full. It has two bits: bit 30 tells you if the read register is empty and bit 31 tells you if the write register is full. The Write register is at 0x20 from the mailbox base address and it works similar to the Read register. It has 4 low bits that tell which channel and the 28 high bits are our data.

The mailbox has 8 channels. A channel is a number that tells you and the GPU what information is being sent through the mailbox. Means. For this project, we will be only needing channel 1, the framebuffer channel, and channel 8, the property channel (ARM -> VideoCore).

## 4.1.9 Page Table

The page table maps virtual memory to physical memory. The processes in memory use virtual memory while the hardware uses physical, and the page table is what reconciles the two. The page table first checks the TLB to see if the memory has been recently used and stored in the cache. If it is not there, a TLB miss occurs and the mapping is looked up in the page table. This is the tricky part, because there are a couple different page tables you could use.

[Image Goes Here]()

Source: https://en.wikipedia.org/wiki/Page_table

Before discussing the different implementations of page tables, it should be important to talk about page sizes. The default size for a page table is 4KB, but can be increased or decreased depending on how process space is needed. Having a large page size, like 2MB or 1GB, can result in a number of problems like having too much internal fragmentation(i.e unused space) and can lead to less flexibility for placing data near processors accessing them the most. But having very small page sizes, like 512 B, can also lead to some major issues including high overhead for bookkeeping translation.

There are several ways of implementing a page table, each having different tradeoffs between one another. One type of page table is a linear page table. This page table is linearly allocated for each application starting from a specific address, thus it being impractical to use. Another type of page table we could have used is an inverted page table. One page table is shared among all the processes and we can use a hash function to speed up the lookup process. One benefit of this is just managing a one-page table for potential low-storage overhead. However, the spatial locality is not considered and no physical pages are shared between applications. Our last option is to use a multi-level page table, which is the implementation we choose to use. It has the benefits of being able to grow dynamically similar to a tree, each application has its own page table, and the page table entries are dynamically allocated with paging.

ARM naturally supports multi-level page tables. This means that the page table is split into levels that point to lower ones. Only the lowest level have frames. This also means that accessing one piece of memory takes several different level accesses to reach. This may seem counterintuitive, but having multi-level paging means that the page table can be split into many parts and page itself. This is especially useful if the entirety of the page table cannot fit in one part of memory. In general, 64-bit systems use 4-page tables.

[Image Goes Here]()

Source: https://www.geeksforgeeks.org/multilevel-paging-in-operating-system/

## 4.1.10 Translation Lookaside Buffer (TLB)

Although using a page table can help store vast amounts of memory addresses, the one downside to using this method is slower access to memory. One solution to this problem would be using a translation lookaside buffer(TLB). The TLB is a memory cache that reduces the amount of time it takes to access a memory location. It stores the most recent translations from virtual memory to physical memory. Each entry will consist of two parts: a page number and a frame number. When the CPU initially looks for an address, it will go to the TLB first. If the data is already there, it will have a TLB hit and bring the data back in. If not, it will have a TLB miss and go into the page table as an interrupt, look up the data, and put it into the TLB. It will then go through the TLB and have a TLB hit.

Although the TLB is not absolutely required, not having one means you are almost working with bare metal, which is very insecure and can lead to some unfortunate errors. Also, having a TLB speeds up the lookup process if you already have certain commonly used addresses stay inside the cache.

[Image Goes Here]()

Silberschatz, Galvin, Gagne, Abraham, Peter B. , Greg (2009). Operating Systems Concepts. United States of America: John Wiley & Sons. INC. ISBN 978-0-470-12872-5.

## 4.1.11 Page Coloring

Page coloring is a way to make virtual memory look contiguous. It is in the same vein of thinking as a multi-level page table- meaning that it “colors” pages that look contiguous in virtual memory and maps them to physical memory non-contiguously. Although it is not required to have a functioning page table, it is very closely related to multi-level page tables and would be a great way to bring the performance of PegasOS to a more efficient and deterministic state.

For our project, we decided to keep this out of scope so as to not overwhelm the team. Our operating system will be very bare-bones, and as such will not have super complex memory to manage. However, this will be a get thing to add as a possible stretch goal, or for a future team.

Because we are not implementing page coloring, our process will be a little more streamlined. The addresses will not have a separate virtual and physical address, and will instead be the same on both fronts. It is important to note that this is very secure to program, but due to the scope of our project we are limiting functionality on this feature.

[Image Goes Here]()

Source: https://en.wikipedia.org/wiki/Cache_coloring

## 4.1.12 ARM Registers and General Research

Reference for this section: https://developer.arm.com/docs/den0024/a/armv8-registers

[Image Goes Here]()

There are 31 x64 bit general purpose registers accessible at all exception levels. Each register has a 32-bit form for backward compatibility. There are also several special registers:

[Image Goes Here]()

**Zero Register**

A zero register is a register that is a hard wired 0. This is a very common register component because 0 is a very useful constant.

**Program Counter**

The program counter houses the current instruction. This is not easily accessed and really should not be directly accessed.

**StackPointer**

The stack pointer points to the current point on the stack.

**Program Status Register**

This register contains information on the current state of the program
being executed and the state of the processors.

**Exception Link Register**

This contains the link to return to once a function call completes. There is a dedicated SP with every exception level, but it does not hold the return state.

**Saved Process Status Register**

Holds the value of the process right before it was interrupted.

## 4.1.13 ARMv8 Processor States

| Name  | Description                                    |
| ----- | ---------------------------------------------- |
| N     | Negative Condition                             |
| Z     | Zero Condition                                 |
| C     | Carry Condition                                |
| V     | Overflow Condition                             |
| D     | Debug Mask bit                                 |
| A     | SError Mask bit                                |
| I     | IRQ Mask bit                                   |
| F     | FIQ Mask bit                                   |
| SS    | Software Step bit                              |
| IL    | Illegal Execution State bit                    |
| EL(2) | Exception Level                                |
| nRW   | Execution State, 0=64, 1=32                    |
| SP    | Stack Pointer Selector, 0 = SP_EL0, 1 = SP_ELn |

Unlike ARMv7, ARMv8 does not have a current program status register. The components of the old register are not accessible under these different processor states. N, Z, C, and V can be accessed from EL0. All others are undefined at EL0, but can be accessed from EL1 and up.

## 4.1.14 System Registers

The System Control Register (SCTLR): Controls standard memory, system facilities, and provides status for functions in the core.

[Image Goes Here]()

## 4.1.15 Endianness

In computer systems, there are two ways of reading a string of bits: little and big-endian. In little-endian, the byte is read from the least significant bit first. In big-endian, the byte is read from the most significant bit first. There are different advantages and disadvantages to both ways, but it is very important to understand which way a system reads them in order to write and read code and bits. In ARMv8, data can be set to read in both little and big-endian, but instruction fetches are strictly little-endian. Endianness is configured in the last step of setting up the MMU.

## 4.1.16 Exception Handling

Exceptions are ARM's version of interrupts. There are two types of interrupts: IRQ (interrupt request) and FIQ (fast interrupt request). IRQ’s are just regular interrupts in the system. FIQ’s however, have higher priority over other interrupts, and cannot be masked by interrupts. FIQ’s main functionality is to bring events such as keyboard or mouse inputs to the forefront. Context saves are not required, and only one FIQ at a time is supported.

Both IRQ’s and FIQ’s are asynchronous. Some synchronous exceptions include: instruction aborts from Memory Management Unit, data aborts, and debug exceptions. (more can be found in the ARMv8 manual). For synchronous exceptions, the following registers are used:

ESR_ELn gives information on the cause of an exception.
FAR_ELn holds the virtual address where it faulted.
ELR_ELn holds the address for aborting data access.

## 4.1.17 Multi-Core Processors

The Pi 4 has four cores. Each core can be used to run multiple things at once and scheduled and managed through complex algorithms. ARM v8 offers a lot of support for these kinds of processes.

For our sake, we are designing our system to run on only one core for now. In the future, it would be wise to run the system using all cores, but as of right now we have several more pressing features to finish. As of right now however, we are considering multi-core as a possible stretch goal to improve performance.

## 4.1.18 Caches

[Image Goes Here]()

The cache is a vital part of an OS because it is very quick-access memory. In ARM systems, caches are usually two or more levels. A cache is required to hold three things: address, data information, and status information.

[Image Goes Here]()

## 4.1.19 Cache Controller

The cache controller is a piece of hardware that manages cache memory. It is largely invisible to the program, but it automatically caches things from main memory. When the cache finds a piece of data inside of it, it is called a cache look-up. When a cache does not find it, it is a cache miss and goes to external memory (L2) to find it. The core however, does not need to wait for the new data to be stored in the cache to use it. This is because the cache controller passes the critical line to the core. This allows the core to read the data from memory while the cache is saving it in the background.

## 4.1.20 Cache Policies

Cache policies let us detail what kinds of data gets stored in the cache. The allocation policy are as following:

**Write Allocation**

Data is loaded into the cache on a write- miss, followed by a write-hit.

**Read Allocation**
Data is loaded on a read-miss.

The update policies are as followed:

**Write-Back (WB)**
Write only occurs on the cache and it is marked as dirty. External memory only updated when evicted or cleaned.

**Write-Through (WT)**
Write updates on both the cache and external memory.

Specific Arm instructions regarding policies are as followed:

PRFM `<prfop>`, addr : prefetches memory for the cache. <Prfop> is broken down into type (PLD for prefetch for load, PST for prefetch for store), target (L1, L2, or L3), and policy (KEEP for retaining in the cache, STRM for single-use memory that is only used once).

## 4.1.21 Cache Maintenance

Cache maintenance is required to remove stale data or update MMU changes. Here is a list of ARM commands to maintain the cache:

| Cache | Operation | Description                                                     |
| ----- | --------- | --------------------------------------------------------------- |
|       | CISW      | Clean and invalidate by Set/Way                                 |
|       | CIVAC     | Clean and Invalidate by Virtual Address to Point of Concurrency |
|       | CSW       | Clean by Set/Way                                                |
| DC    | CVAC      | Clean by Virtual Address to Point of Coherency                  |
|       | CVAU      | Clean by Virtual Address to Point of Unification                |
|       | ISW       | Invalidate by Set/Way                                           |
|       | IVAC      | Invalidate by Virtual Address, to Point of Coherency            |
| DC    | ZVA       | Cache Zero by Virtual Address                                   |
|       | IALLUIS   | Invalidate all, to Point of Unification, Inner Shareable        |
| IC    | IALLU     | Invalidate all, to Point of Unification, Inner Shareable        |
|       | IVAU      | Invalidate by Virtual Address to Point of Unification           |

## 4.1.22 Memory Management Unit

The MMU is an actual physical piece of hardware. It is vital, because it translates virtual addresses to physical addresses. It also checks access permissions and gives us control over cacheable memory.

[Image Goes Here]()

Source:
https://en.wikipedia.org/wiki/Memory_management_unit#cite_note-TanenMOS-1

What does all of that mean? Let us break it down.

## 4.1.23 Virtual to Physical

The actual hardware reads physical addresses. At first glance, it may seem easier for us to read physical addresses as well. The problem that arises if you do that is that you end up wasting a lot of space. For example, if you have 32-bit addresses that you want to store in 4G, you would actually need, in the worst case, 16G to hold 4G worth of physical addresses. As such, it is vital to translate addresses to virtual addresses, and the MMU facilitates this process.

## 4.1.24 Access Permissions

Every program cannot have full reign over all the memory. For instance, a text editor should not be able to access personal information in another window. This is where access permissions come in. If a program tries to access somewhere it is not allowed then the MMU aborts. This, however, will not be fatal. For example, this feature is used to map virtual machines to registers on the hardware they are actually on. When it makes access, it is denied but then given the right address. This can also be used to extend RAM when it runs out for a program because the nature of virtual addresses allows this.

## 4.1.25 Cacheable Memory

The MMU lets you decide whether or not you want to cache a certain memory. For example, you would not want to cache a peripheral, because if you cache it’s value and use that instead of the actual value your program will probably crash or receive several errors (ex: caching a timer). A general rule of thumb is to only make things cacheable if they can only be written to through the cache. That way, you never run into a problem of reading the wrong value.

## 4.1.26 Translating a Virtual Address to a Physical Address

In AArch64, bits 63-47 must be either all 1s or all 0s. This is the base address and will trigger an error if not done correctly. Bits 41-29 map to a page table entry, which maps to the actual physical address. If the address is valid, it will allow memory access. Bits 28-0 give an offset within the section to combine with the physical address.

[Image Goes Here]()

## 4.1.27 Configuring the MMU

The MMU in the Pi 4 is very similar to the Pi 3, except for a bigger address space. The memory layout begins a bit odd, the first gigabyte of physical RAM you have $gpu_mem and $arm_mem that is used by firmware.

Gpu_mem can be configured from config.txt. Basically, this is splitting the memory between the GPU and the CPU. The minimum value is 16 but the default is 64. Since the GPU has its own MMU, gou_mem can be adjusted to lower values if more space is needed in the CPU.

The rest of the MMU needs to be set up by level. EL0 is where the user interface should go. The kernel usually runs on EL1 or EL2. For a small system like ours, it would make more sense to keep the kernel in EL1. EL3 is also available, but is usually set aside for trusted firmware.

# 4.2 I/O Manager

[Image Goes Here]()

https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/13_IOSystems.html

A simple way of setting up an I/O Manager is to have registers that the CPU checks every instruction. This is broken down into four registers:

**Data-In:** Read for input

**Data-Out:** Write for output

**Status:** Read for status of devices (idle, ready, busy, error, transaction complete)

**Control:** has bits to issue commands or change settings (parity checking, word length, etc)

Another way is through memory-mapped I/O. This means portioning out some address space to map to the device. Communication occurs from reading/writing from those areas. This is mostly useful for devices with tons of data to process (ex. A graphics card) and not for low input devices. With this method, however, you will also need to establish access permissions to prevent processes from overwriting it on accident.

In general, there seem to be these two lines of thinking: constantly check registers or set the drivers up like a process. For our case, it would be more organized to encapsulate all the drivers as processes in an I/O Manager that sends interrupts to the kernel. Any driver, such as a keyboard or a mouse, would send their interrupts to the I/O Manager (as FIQ’s), which would organize and send those values to the kernel.

Encapsulating the I/O Manager is better than the registered approach for a couple of reasons: for one, it allows for us to add and remove drivers without having to interact directly with the kernel. Second, it allows the kernel to operate without stopping every instruction to check a register. Third, it creates a single interface that we can update and use to communicate with possible deprecated drivers.

[Image Goes Here]()

[Back - Technical Content](3_TECHNICAL_CONTENT.md) | [Next - System](5_SYSTEM.md) | 
[Design Document Home](DESIGN_DOCUMENT.md) | [Documentation Home](../README.md)