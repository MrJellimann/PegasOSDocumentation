# How To Set Up the Arm Cross-Compiler

At a later date there will also be a YouTube video going through these steps. In the meantime, if there are any particular questions about this process just make an Issue in the PegasOSDocumentation repository (where you are now) and we'll get back to you!

**Navigation**

[Part 1 - Installing the Cross-Compiler for AArch64](#part-1)

[Back to Guides Home](GUIDES_HOME.md)

---

To cross-compile Circle on Linux, do the following:

Here, we will be doing this from Windows 10 running WSL on Debian (the steps are the same for Ubuntu).

## === Part 1 - Installing the Cross-Compiler for AArch64 ===
(#part-1)

### Step 1.
Go to: 
    https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-a/downloads

And download the appropriate (aarch64-none-elf) compiler FOR YOUR CPU: 

    Modern Intel/AMD CPU running Linux -> gcc-arm-9.2-2019.12-x86_64-aarch64-none-elf.tar.xz

If you are compiling this on an ARM cpu (such as a windows tablet), try this one instead: 

    ARM64 CPU running Linux -> gcc-arm-9.2-2019.12-aarch64-aarch64-none-elf.tar.xz

### Step 2.
Extract the contents to a new folder.

### Step 3.
Open your WSL Distro (in my case, Debian).

### Step 4.
Create a directory called /opt

(Note: the directory name doesn't matter, but being consistent does)

### Step 5.
Open the folder in windows with `explorer.exe .`

### Step 6.
Open the folder you extracted the compiler to.

### Step 7.
Copy the folders within the compiler's folder into /opt.

These folders are called 'aarch64-none-elf', 'bin', 'include', 'lib', 'libexec', 'share'

### Step 8.
After the folders have copied over, open the .profile file for your user in your text editor.

Sample: `nano ~/.profile`

### Step 9.
At the bottom of the file, add a line that says:

	export PATH="$HOME/opt/bin:$PATH"

(Note: you may substitute this with a custom directory if you did that for Step 4)

### Step 10.
Restart your computer for the path changes to take effect.

(This is the easiest method. There are other ways to reset your environment variables.)

### Step 11.
Reopen your WSL Distro.

### Step 12.

To confirm the path variable works correctly, type:

	aarch64-none-elf-gcc -v

or

	aarch64-none-elf-gcc --version

### Step 13.
If you see a copyright message, you've done it!

### Notes/Troubleshooting
If the path is correct but it can't find the file, make sure that the binaries have read and execute permissions.

To check what the permissions of your files are, navigate to their directory and enter the following command:

    ls -la

If your binaries do not have `-rwxr-xr-x` permissions, even if the path variables are pointing to the correct location, the user will not be able to access the binaries because they don't have the `x` or execute permissions. The `r` or read permissions are not as important, though still recommended. What's important to look at is the last six characters of the permissions, which dictate the group permissions and other user permissions (other than the file owner, which is the first triplet of characters.

To change the permissions on these files, navigate to your `opt/bin/` directory and enter in the following command:

    chmod -R 755 ./

Now, you **MUST** be careful with this command. The `-R` tag is for *recursive* which means it will do this for every file in the current directory. Depending on where or how you call this command, you may accidentally change the permissions for *every single file on your Linux system*. Navigating to the `opt/bin/` directory (or wherever you put the compiler binaries) will act as the root of the recursion, and it will recursively modify the permissions for everything below it.

## === Part 2 - Compiling the Libraries ===

### Step 14.
For Circle on its own, clone the Circle Repository:

	https://github.com/rsta2/circle

For PegasOS, clone the PegasOS Repository:

    https://github.com/mrjellimann/PegasOS

### Step 15.
Move into the `/circle/lib` directory of the repository

### Step 16.
`make` the `/lib` folder

### Step 17.
Move into the `/circle/lib/usb` directory

### Step 18.
`make` the `/lib/usb` folder

### Step 19.
Move into the `/circle/lib/input` directory

### Step 20.
`make` the `/lib/input` folder

### Step 21.
Move into the `/circle/lib/fs` directory

### Step 22.
`make` the `/lib/fs` folder

### Step 23.
Move into the `/circle/lib/fs/fat` directory

### Step 24.
`make` the `/lib/fs/fat` folder

### Step 25.
Move into the `/circle/lib/sched` directory

### Step 26.
`make` the `/lib/sched` folder

### Step 27 (PegasOS Compile).
Move into the `/lib/pegasos/` directory

### Step 28 (PegasOS Compile).
`make` the `/lib/pegasos` folder

### Step 29.
You've compiled the Circle library! (at least the important stuff)

## === Part 3 - Compiling the Sample Kernel ===

### Step 30 (Circle Compile).
Move into the desired sample folder. For this example, the usbkeyboard folder:

	/circle/sample/08-usbkeyboard

### Step 31 (PegasOS Compile).

### Step 32.
`make` the desired folder

### Step 33.
If you see a 'kernel8-rpi4.img' file, you've done it!

## === Part 4 - Move files onto RPi Micro SD and Boot ===

### Step 31.
Insert your Micro SD card into your Micro SD card reader/adapter and into your PC

### Step 32.
Make sure that your Micro SD card is formatted for FAT file system

### Step 33.
Copy the files from `/circle/boot` onto your Micro SD card

### Step 34.
Copy the kernel image from Step 29 onto your Micro SD card

### Step 35.
Make sure that the 'config.txt' file is present on the Micro SD card for 64-bit boot

### Step 36.
Now remove your Micro SD card and reinsert it into your RPi

### Step 37.
Plug in your Pi and boot

### Step 38.
If you see text about Circle or PegasOS, you've done it!

**Navigation**

[Back to Guides Home](GUIDES_HOME.md)