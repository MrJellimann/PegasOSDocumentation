# 3.0.0 PegasOS First Release

The first public release of PegasOS will be released at the end of the Fall 2020 Semester, which means that the first public release of PegasOS will be available on Wednesday, December 2nd, 2020. The link to this release is here. The original PegasOS Team may or may not continue work on PegasOS past this point, though this is not guaranteed. This point will be discussed further in PegasOS Future.


We will break down the features of this release into four main components: the Command Line Interface or Shell, the File System, the Task System, and the Memory Management System.

## 3.1.0 PegasOS Shell

PegasOS comes with a Command Line Interface, which we will call the Shell from here on. It is made up of two main components: the commands that can be executed on the system, and the user system that allows for users to have their own space on the system.

## 3.1.1 PegasOS Shell Commands

The PegasOS Shell on release contains 24 commands, as given here including the appropriate arguments for each one:


***changedir***

    changedir <path>

        Will change the current directory to the specified parameter given.


***clear***

    clear

        Clears the screen of all previous input/output.


copy
copy <file> <directory>
        Creates an identical copy of the specified file to the specified location given.


createdir
createdir <directoryname>
        Creates a subdirectory within the current directory.


createfile
createfile <filename>
        Creates a file with the given name and extension in the current directory.


currenttasks
cutrenttasks
        Shows a list of all currently active tasks in the scheduler.


delete
delete <file>
        Deletes the specified file given.


deletedir
deletedir <directory>
        Deletes the specified subdirectory given.


displaytasks
displaytasks
        Displays the scheduler demo. To stop displaying tasks enter ‘s’ and return.


dirtext
dirtext <num1,num2,num3>
Will change the directory color to the specified (x,y,z) values between 0-31 for Red, Green, and Blue respectively. E.g. dirtext 9,13,24


echo
echo <text>
        Repeats the inputted text, surrounding it with “Echo” and “Echo!”


head
head <file>
head <file> <n>
Prints out the first ‘n’ given lines of the specified file within the current directory. If no value for ‘n’ is not given, then it will simply print out the first 5 lines.


hello
hello
        The system responds to the user and says hello.


help
help
Lists all available commands in the system. (With a more mature system this will not be feasible).


listdir
listdir 
        Displays the path to the current and the current working directory.


login
login
Will allow the user to login off Boot. Also allows for new users to create a new file with their own password.


memorystats
memorystats
Displays the amount of free space on the low, high, and combined heap and how many memory blocks the system has.


move
move <file> <directory>
        Moves the specified file given to the directory specified.


power
power
        Powers off the system. This will occur immediately after this command is entered.


reboot
reboot
        Reboots the system immediately after this command is entered.


systeminfo
systeminfo
        Displays the information for the separate components of the Pi.


tail
tail <file>
tail <file> <n>
Prints out the last ‘n’ given lines of the specified file within the current directory. If no value for ‘n’ is not given, then it will simply print out the last 5 lines.


usertext
usertext <num1,num2,num3>
Will change the user color to the specified (x,y,z) values between 0-31 for Red, Green, and Blue respectively. E.g. usertext 12,22,30


writeto
writeto <file> <string>
        Will add the given string to the end of the given file.

## 3.1.2 PegasOS Users

A user account is required to use the system. When PegasOS boots up, they will be prompted with the log-in screen. Here they will enter their username and password. Once the password is entered, it will be compared to the password stored in the user file corresponding to the user name. If they match, the user will be granted access. Otherwise, they will have to try again.


        Welcome to PegasOS!
        Please log in to continue…
        Username:
        Password:


If a username is entered for a user that does not exist, they will be prompted with the following text. This prompt will also be displayed if there are no users on the system at all:


        User does not exist. Would you like to create one? [Y]es/[N]o
        Enter desired Username:
        Username was created. Now please enter a password:


If the password was not entered correctly, the user will be able to attempt to enter their password again.


        Password: Password mismatch.
        Re-enter Password:


Once the user has entered their username and password correctly, or created their user account, they will be shown the following prompt and may begin using the system.


        Successfully logged in!
        user@RaspberryPi:SD:$ _

## 3.2.0 PegasOS File System

PegasOS uses a FAT32 system, as provided in Circle. While FAT32 is basic by today’s standards, it is not lacking in any of the typical features one would expect from a modern file system. PegasOS’s file system supports the following features:


Create Files
The user can create files of any type, the type being the file extension. A user may even create a file without an extension. The files can then be opened and written to or read from.


Create Directories
The user may create directories wherever they wish, as long as the device has enough storage.


Move Files
The user may move files between directories, wherever they wish (with the appropriate permissions).


Move Directories
The user can move a directory from one location on the disk to another (i.e. move a subdirectory from one directory to another).


Open Files
        The user can open files for reading or writing purposes.


Navigate Directories
The user may change the active or working directory, so that new commands are begun from the new active or working directory.


Delete Files
        The user can delete files (with the appropriate permissions).


Delete Directories
        The user can delete directories (with the appropriate permissions).


Mount Storage Devices*
        The user can mount different/additional storage devices.


*This is technically true, though this is primarily the SD card that the system is contained on.

## 3.3.0 PegasOS Task System

The task system for PegasOS is fairly straightforward. There is a main task within the kernel that starts the tasks, and each task runs through its allotted time as long as it is instantiated and a member of the CTask class. The main task running is the shell. It is made in CKernel::Run() and sets the speed at which tasks have control of the system. Each task is instantiated within CKernel::Run() as well, but the management of the tasks goes to scheduler.cpp.


CTask is the parent class to all tasks in the system. It allows for quite a bit of customization, as you can create tasks with different arguments (LED tasks, screen tasks, etc.). Any task created from CTask gets fed into an array in the scheduler, which gets processed through any function the scheduler may need. Each task also has an initial weight of zero, which is the lowest priority. Tasks that have higher weights will get inserted farther ahead in the array. Currently there is no ceiling limit to the weight of a task.


As of now tasks cannot be removed via the command line, as this proved to be catastrophic to the system (though this could be addressed by another team in the future). However you can view all running tasks in the system and display them in real time. The task demo command displaytasks also showcases four screen tasks running on the system to demonstrate its multi-tasking abilities.


Creating new tasks on the system is as simple as creating a class that is a child of CTask, and instantiating it. This could then be tied to commands on the shell for specific system tasks and/or components.

## 3.4.0 PegasOS Memory Management System

PegasOS essentially uses the memory system given through Circle, which consists of several key features one would want in a memory system for an OS. The memory system has support for paging on a flat memory space through a two-level page table for 32-bit systems and a three-level-max page table for 64-bit system. Page sizes are configurable through the header files for the memory system. One drawback of the system however is that it provides no virtualization for programs - it is essentially a flat memory space of literal addresses within the page. The default page size of Circle (and therefore PegasOS) is 64KB. The memory system also supports dynamic heap allocation on a flat memory space, using heap-low for all Pi models and heap-high for Pi 4 and newer models.

## 3.5.0 First Release Known Issues

* The ‘changedir’ command cannot move up and down levels in one command. For example, if you are in your /desktop directory and wish to move to your /documents directory, you must enter two commands: changedir ../ and changedir documents. However, if you try to enter changedir ../documents you will not move any directories at all. This is due to a parsing issue with the directory string that could not be resolved in time. Interestingly, this issue does not apply to the other directory commands, ‘createdir’ and ‘deletedir’.


* The ‘changedir’ command will not remove instances of ‘./’ in the directory path. If the user enters the command changedir . then their directory path will look like the following:
user@RaspberryPI:SD:/users/user/desktop/./
This does not have any effect on moving up directory levels, for example if the user was in their root directory /users/user/ and ended up with a ‘./’ in their directory path, they would still be able to enter their /desktop and /documents directories correctly. It will however take multiple entries to move down directories with ‘..’ to clear out every occurrence of ‘./’.
        
* When logging in to the system, if you enter a correct username the system will not let you continue until you enter their password correctly. The system will repeatedly prompt you to re-enter their password until it is entered correctly or the machine is rebooted. This is due to the lack of a maximum attempts catch on failed log-ins. To switch accounts or create a new one from this state, you must reboot the machine.


* Backspacing after reaching the beginning of the command input will erase characters from the screen. This does not have an actual effect on the input string for commands, it is simply a visual bug that is partially due to the Screen device’s separation from the command shell.


* Shell commands can only accept two parameters, internally known as _commandParameterOne and _commandParameterTwo. Any delimiters after the second will count towards _commandParameterTwo. For example:
writeto myfile.txt Some text for my file.
Will be a valid command and write ‘Some text for my file.’ to ‘myfile.txt’. However, this:
        move myfile.txt .. otherparam
Will be an invalid command, not because it should have three parameters, but because ‘.. otherparam’ is the second parameter and is not a valid directory to move ‘myfile.txt’ into.
        
* The command can be overflowed and leave the system unresponsive. The typical layout of the input string is as follows:
<command> <param1> <param2>
However, if the <command> section of the input string, i.e. if everything before the first space exceeds the buffer for the command string, the system will freeze and require a reboot.


* Overflowing the string parameter of the ‘writeto’ command can overwrite parts of the file extension for a new file creation, and the string written to the file will not be the entirety of what was entered. Furthermore, the end of the inputted string will have writeto appended to it.
If this is attempted with a pre-existing file, the name and extension of the file will not be affected. However the string written to the file will be the same as in the case above.


* After entering ‘displaytasks’ and running the command, other commands can be run before the cancel command ‘s’ has been entered. This means that the screen tasks in the scheduler demo will continue to print to the screen while the user attempts to use the system. This may be problematic as it will not take long for the prompt about the ‘s’ command to disappear off the screen.

[Back - 2.0.0 PegasOS Cuts and Edits](2_PEGASOS_CUTS_EDITS.md) | [Next - 4.0.0 PegasOS Future](4_PEGASOS_FUTURE.md) | 
[Addendum Design Document Home](ADD_DESIGN_DOCUMENT.md) | [Documentation Home](../README.md)