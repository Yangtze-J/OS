## Lab 1 : Introduction to Linux Shell
**Linux Architecture**
A Linux Operating System has primarily three components:

 1. **Kernel:** At the core is the Linux kernel, which mediates access to the underlying hardware resources such as memory, the CPU, and peripherals.
 2. **Shell:** The shell provides user access to the kernel. The shell provides command interpretation and the means to load user applications and execute them.
 3. **Applications:** These make up the bulk of the GNU/Linux operating system. These applications provide the useful functions for the operating system, such as windowing systems, web browsers, and, of course, programming and development tools.

**Type of shell in-use**
There are two major types of shells in Linux:
**Bourne shell** (i.e. sh, ksh, bash)
**C shell**(i.e. csh,tcsh).

**Basic Bash Commands**
```
	echo

	// display a line of text:	
	echo "hello  world" 

	// display a environment variable:	
	echo $HOME
```
**Basic operations on files**
```
	rm
	// rm-remove files or directories
	// common option:   -r   -f
```
**Showing file content**
cat/more/less/head/tail
```
	cat: cat concatenates files and prints all content to the terminal at once.
```
``more``and ``less`` are two other commands that do a similar job as cat once, **more** and **less** divide and print one screen at a time.

``head``prints a certain numbers of lines, 10 by default, from the beginning of a file.
```
	head -n 2 filename.txt   // prints the first 2 lines of the file.
```
``tail`` is almost the same as head, except that it counts lines from the end of a file.

**Searching file content**
```
	grep:
	grep [OPTIONS] PATTERN [FILE...]
	grep searches the named input FILEs for lines containing a match to the given PATTERN.
```

**Add extra content at the end of the file**
```
	echo "contents" >> filename
```

**Basic operations on processes**
``ps``	displays information about a selection of the active processes.
To see every process on the system using BSD syntax:	``ps aux``

``kill``	sends a signal to a process, affecting its behavior or killing it.

**Useful Operators for Bash Commands**
Pipe Operator ( | )
By putting ``|`` between 2 commands, the output of the first command is piped to the second command as its input.
```
	ls | grep Do
```

Output Redirect Operator ( > )
Adding a ``>`` to the end of a command, followed by a file name, redirects the standard output stream (stdout).
```
	ls > ls.out
	cat ls.out
```
**File permission**
The most commonly way to view the permissions of a file is 
```
	ls -l
``` 

Changing Permissions
To change the file or the directory permissions, you use the chmod(change mode) command.
There are two ways to use ``chmod``	— **the symbolic mode** and **the absolute mode**.

**the symbolic mode**:
Chmodoperator & Description

``+``	Adds the designated permission(s) to a file or directory.
``-``  Removes the designated permission(s) from a file or directory.
``=``  Sets the designated permission(s).
```
	//remove the execute permission of the group of other users
	chmod o-x testfile
	
	//add write and execute
	chmod o+wx testfile
	
	//change the permission of group members
	chmod g=rx testfile
```
**the absolute mode**
``chmod``	with Absolute Permissions
![enter image description here](https://lh3.googleusercontent.com/VXsUmb_wXjgPQ8-tJvknvTqjjsf-8kImi1t8euUwwqXrE8ql0p6V1EwYl3_kfFNhU_tMIIEXIkY)

**Elements in Bash Scripts**
Bash Script
To create the Bash *script*, you may gather a list of commands, put it into a file.
Alternatively, we may put a shebang, ``#!/bin/bash``, at the beginning of a script. This shebang tells the OS to use /bin/bash to parse and run the script.

**Create a shell script**
To create a shell script:
1.Use a text editor such as vim. Write required Linux commands and logic in the file.
2.Save and close the file (exit from vim).
3.Make the script executable.
4.Script can be run directly.
```
	vim hello.sh
	ls -l hello.sh
	chmod u+x hello.sh
	
	./hello.sh
```

**Shell Variables**
You can use variables as in shell. There are no data types. A variable in bash can contain a number, a character, a string of characters..

The name of a variable can contain only letters (a to z or A to Z), numbers ( 0 to 9) or the underscore character \_ .

Variables are defined as follows:
``variable_name = variable_value``
For example,
``NAME = "Operating_Systems"``

To access the value stored in a variable, prefix its name with the dollar sign (``$``).
```
	#!/bin/bash

	NAME = "Operating_Systems"
	echo $NAME
```

















