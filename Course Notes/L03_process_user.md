## Process    part.1
**What is a process**

Process is a program in execution. It contains every accounting information of tha running program, e.g.,
 - Current program counter
 - Accumulated running time
 - The list of files that are currently opened by that program
 - The page table

Process creation – **fork()** system call 
-
To create a process, we use the system call fork().

 - Both the parent and the child execute the same program.
 - The **child process starts** its execution **at the location that fork() is returned**, **not from the beginning** of the program.

```
// fork_example_2.c
#include<stdio.h>
#include<unistd.h>

int main(void)
{
    int result;
    printf("before fork ...\n");
    result = fork();
    printf("result = %d.\n", result);
    if(result == 0) 
    {
        printf("I'm the child.\n");
        printf("My PID is %d\n", getpid());
    }
    else {
	printf("I'm the parent.\n");
	printf("My PID is %d\n", getpid());
    }
    printf("program terminated.\n");

    return 0;
}

$ ./fork_example_2
before fork ...
result = 16162.
I'm the parent.
My PID is 16161
program terminated.
result = 0.
I'm the child.
My PID is 16162
program terminated.
```
**For the parent, the return value of fork() is the PID of the created child.
For a  child, the return value of fork() is 0.**

#### Process creation – fork() system call
Fork() behaves like "cell division"
It creates the child process by **cloning from the parent process, including all user-space data**, e.g.,
|  Cloned items|Descriptions  |
|--|--|
| Program counter[CPU register] | That’s why they both execute from the same line of code after fork() returns. |
|Program code[File and Memory]|They are sharing yje same piece of code.
|Memory |Including local variables, global variables, and dynamically allocated memory.
|Open files[Kernel's internal]|If the parent has opened a file “A”, then the child will also have file “A” opened automatically.

However, fork() does not clone the following:
- Return value of *fork()*
- PID
- Parent process
- Running time
- File blocks

Process execution
----
How can we execute other programs?
The **exec\*()** system call family.

```
#include<stdio.h>
#include<unistd.h>
int main(void) 
{
	printf("before execl ...\n");
	execl("/bin/ls", "/bin/ls", NULL);
	printf("after execl ...\n");
	return 0;
}

$ ./exec_example
before execl ...
exec_example
exec_example.c
$ _                     
```
 The shell prompt appears again, which means that:
```
int main(void) 
{
	printf("before execl ...\n");
	execl("/bin/ls", "/bin/ls", NULL);
	
	//The code is not reached below and The process is terminated.
	printf("after execl ...\n");    
	return 0;

}
```
**The exec system call family is not simply a function that “invokes” a command.**

 1. Originally, the process is executing the program “exec_example”.
 2. execl() changes the execution from “exec_example” to “/bin/ls”。
```
/* The program “ls” */
int main(int argc, char ** argv)
{
	......
	exit(0);
}

// The “return” or the “exit()” statement in “/bin/ls” will terminate the process
// the process cannot go back to the old program.
```

**exec**
The process is changing the code that is executing and never returns to the original code.

The process that calls an exec\* system call will **replace user-space info, e.g., Program code, Memory, Register value.**

But, the kernel-space info of that process is preserved, including: PID, Process relationship, etc.

fork()+ exec*() = ?
-  
```
int system_ver_3150(const char *cmd_str) 
{
	if(cmd_str == -1)
		return -1;
	if(fork() == 0) 
	{
		execl("/bin/sh", "/bin/sh", "-c", cmd_str, NULL);
		fprintf(stderr, "%s: command not found\n", cmd_str);
		exit(-1);
	}
	return 0;
}
	
int main(void) 
{
	printf("before...\n");
	system_ver_3150("/bin/ls");
	printf("\nafter...\n");
	return 0;
}
```
There might have two execution results.
| the expected sequential order | the abnormal order |
|--|--|
|  $ ./system_implement_2|  $ ./system_implement_2|
|  before...| before... |
|system_implement_2   |after...|
|System_implement_2.c|system_implement_2   |
|after...|System_implement_2.c|
|$ _|$ _|

It is very weird to allow different execution orders.
But...we can’t control the OS scheduler.
Then, our problem becomes...
- How to suspend the execution of the parent process?
- How to wake the parent up after the child is terminated?

**fork()+ exec\*() + wait() = system()**
```
int system_ver_3150(const char *cmd_str) 
{
	if(cmd_str == -1)
		return -1;
	if(fork() == 0) 
	{
		execl("/bin/sh", "/bin/sh", "-c", cmd_str, NULL);
		fprintf(stderr, "%s: command not found\n", cmd_str);
		exit(-1);
	}
	wait(NULL);
	return 0;
}
```

**wait() – user-space**
wait() system call:
 - suspend the calling process to waiting state and return (wakes up) when:
	1. one of its child processes changes from running to terminated.
	2. Or a signal is received.
 - return immediately (i.e., does nothing) if:
	 - it has no children, or a child terminates before the parent calls wait for.

### Summary
A new process is created by fork().

A process is a program being brought by exec to the memory.
- has state (initial state= ready)
- waiting for the OS to schedule the CPU to run it

A process  can execute more than one program by keeping on calling the exec system call family.
