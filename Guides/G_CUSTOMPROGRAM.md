# How To Compile a Custom Program with PegasOS

While PegasOS in its current state (at time of writing) does not support the launching/executing of binary files, it is still possible to get a custom program working on PegasOS and usable within the PegasOS system.

**Navigation**

[Part 1 - Locating and Placing Header/Source Files Correctly](#part-1)

[Part 2 - Adding a Shell Command For Your Program](#part-2)

[Part 3 - Re-Compiling PegasOS](#part-3)

[Part 4 - Testing](#part-4)

[Back to Guides Home](GUIDES_HOME.md)

---

# Part 1
## Locating and Placing Header/Source Files Correctly

There are two main directories that you need to pay attention to:

    <project root>/include/pegasos/

and

    <project root>/lib/pegasos/

Header files should be placed in the first directory. After the header files are placed there, that's all there is to it! You should be able to access them from anywhere in PegasOS or Circle through normal `#include` headers.

    #include <pegasos/myprogram.h>

Source code files should be placed in the second directory. After the source code files are placed there, you must then edit the `Makefile` included there. There is a line for OBJ files that should look something like:

    OBJS = shell.o something.o

Simply add your program name followed by the *.o* extension and it will be included in the compile like so:

    OBJS = shell.o something.o myprogram.o

# Part 2
## Adding a Shell Command For Your Program

Once your code is in the right place, now its time to access it while the system is running.

In order to do that, you must make a command for your program within the PegasOS shell. At the time of writing, the shell uses a fairly straight forward string comparison to know which command to call - simply add a string for your program and call the appropriate functions afterwards. To see examples of this in action, look at the existing shell command code in `shell.cpp`.

# Part 3
## Re-Compiling PegasOS

Once your code is in the right place and your command has been added, it's time to recompile!

Using our `make` command in the repository's root should take care of this for you. If this fails, observe any compiler warnings or errors for steps to fix your code. Otherwise, you may need to try manually compiling each section to create the *.a* libraries needed for the creation of the kernel image in the final step of the `make`. For more information about this, please see our [Compilation Guide](G_CROSSCOMPILER.md).

If PegasOS successfully compiles, you should see several printouts regarding a 6-digit kernel number and *rm* calls to clean up *.o* files after the compile. Then you're ready to copy over the new kernel image and (optionally) the corresponding *.elf* file onto your SD card.

# Part 4
## Testing

Now it's as simple as putting the SD card back into your Pi, booting, logging in, and trying out the command!

If something doesn't work, try to isolate the problem as best you can before recompiling again. The better you can pinpoint what's wrong, the faster you can fix it and see your program in action.

---

**Navigation**

[Part 1 - Locating and Placing Header/Source Files Correctly](#part-1)

[Part 2 - Adding a Shell Command For Your Program](#part-2)

[Part 3 - Re-Compiling PegasOS](#part-3)

[Part 4 - Testing](#part-4)

[Back to Guides Home](GUIDES_HOME.md)