**What is a process**
Process is a program in execution. It contains every accounting information of tha running program, e.g.,
Current program counter
Accumulated running time
The list of files that are currently opened by that program
The page table

Process creation – **fork()** system call 
To create a process, we use the system call fork().
>Both the parent and the child execute the same program.
The child process starts its execution at the location that fork() is returned, not from the beginning of the program.
