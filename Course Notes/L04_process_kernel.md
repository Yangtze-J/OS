## Process    part.2

#### When invoking a system call 
 - Memory view
	 -  When running a program code of a user process, since the code is in user-space memory, so the program counter is pointing to that region.
	 - When the process is calling the system call “getpid()”, the CPU switches from the user-space to the kernel-space, and reads the PID of the process from the kernel.
	 - When the CPU has finished executing the “getpid()” system call, it switches back to the user-space memory, and continues running that program code.
 - CPU view
 
![ ](https://lh3.googleusercontent.com/acgrdPLd4ZvygOSKG14FZiYbLpOvJrYHB-ER7BUYe1hkGb_cj2Ku2CEnrVhPuSRoJgeUkPzE_hI)

#### User time  and  System time
![enter image description here](https://lh3.googleusercontent.com/fKZw7N6BV6s2NGVrpNXWNq2phMVBnQBrdo2qw1hU2SK8S6uCV1GheIe26PE1HDKsJnDoHD1J2nk)
User time – CPU time spent on codes in user-space memory.
Sys time   – CPU time spent on codes in kernel-space memory.

The user time and the sys time together define the performance of an application.

Function calls cause overhead
 - Stack pushing

Sys calls may cause even more
 - Sys call is from another “process” (the kernel)
 - Switching to another “process”     ->   context switch

Working of system calls  fork()
-
Programmer view of fork()
![enter image description here](https://lh3.googleusercontent.com/cOMQ7Ho_jXG1onGws3SYfC8uPt-au2fH4cCsZEJzGdFp0zkgR6N2BW3XxS5W7cc6RpMIWsnBFMA)
1. fork() inside the kernel
 A process invokes fork(), and then its properties(PID, running time, array of opened files) are copied;
 > Inside kernel, processes are arranged as a doubly linked list, called the task list.
2. fork() in action – kernel-space update
- A new node is introduced with a new PID.
- There are changes among the duplication for the new process: new PID, running time( reset to 0 ), and pointer to the parent.
- The original process adds a child.
3. fork() in action – user-space update
- local and global variables, dynamically-allocated memory and code with constants are copied from parent to child process.
4. fork() in action – finish
- Parent process is ready to return from fork()
	- its return value is the PID of its child 
- Child process is ready to return from fork()
	- its return value is 0
5. fork() in action – array of opened files?

| Array index | Description |
|--|--|
| 0 | Standard Input Stream; FILE *stdin; |
|1|Standard Output Stream; FILE *stdout;|
|2|Standard Error Stream; FILE *stderr;|
|3 or beyond|Storing the files you opened, e.g., fopen(), open(), etc.|

That's why **a parent process shares the same terminal output stream as the child process.**

### What if two processes, sharing the same opened file, write to that file together?

Working of system calls     - exec*()
-
exec*() that you’ve learnt...
![enter image description here](https://lh3.googleusercontent.com/XMKmhPcCfFpjVq_WiVJ_2w_CttinwjJAluxbmdSh1fcUKSlB2AO_bE2V7CIuZW0-BPmDBDx6D9o)
The process returns to user-space but is executing another program.
**exec\*() in action**
![enter image description here](https://lh3.googleusercontent.com/Nhk3OZsCfbqOFJNKgtRGbnVFBnJMDC8Zg3IYxDpGs3P_iaao-meCkwrBTpGcauTMG2nEH-Fqpwc)

Working of system calls  - wait() + exit();
-
![enter image description here](https://lh3.googleusercontent.com/oFcK56R8ItuyFyCgyzDldQ2CPaC_LZ3jprGfcGXszQqn0Klx5Xf2ueolmUGdPmscDmwh_izct34)
**exit() (kernel-view)**
When the child calls exit(),
- the kernel frees all the allocated memory.
- the list of opened files are all closed.
- Then, the kernel frees everything on the user-space memory.
- However, process ID stills in the kernel’s process table.
  - This entry is still needed to allow the process that started the process (now zombie) to read its exit status.
  - The status of the child is now called zombie (“terminated).
 - Last but not least, the kernel notifies the parent of the child process about the termination of its child. 
   - The kernel sends a **SIGCHLD** signal to the parent.

**What the kernel does for exit()**
1. Clean up most of the allocated kernel-space memory (e.g., process’s running time info).
2. Clean up the exit process’s user-space memory.
3. Notify the parent with SIGCHLD.

wait() kernel’s view
-
By default, every process does not respond to the SIGCHLD signal 
- i.e. ,the parent ignores his child unless he is waiting for the child)

**But if a process has called wait()**
- The **kernel will register** a signal handling routine for that process

**When SIGCHLD comes, the corresponding signal handling routine is invoked**
- Note: the parent is still inside the wait() system call
- And the **signal handler**(default registered by the kernel):
  - **Accept and remove** the SIGCHLD signal;
  - **Destroy the child process** in the kernel-space (remove it from process table, task-list, etc.)
 
**Finally, the kernel**
- deregisters the signal handling routine for the parent
- returns the PID of the terminated child as the return value of wait()
- The parent is ignoring SIGCHLD again.

 wait() and exit() – short summary
 -
1. exit() system call turns a process into a zombie when...
 - The process calls exit().
 - The process returns from main().
 - The process terminates abnormally.
   - The kernel knows that the process is terminated abnormally. Hence, the kernel invokes exit() for it.

2. wait() & waitpid() are to reap zombie child processes.
- It is a must that you should never leave any zombies in the system.
- wait() & waitpid() pause the caller until
  - A child terminates/stops, OR
  - The caller receives a signal (i.e., the signal interrupted the wait())
- Linux will label zombie processes as “\<defunct>”.


### The first process
The kernel, while it is booting up, creates the first process – init.
- the init process has PID = 1
- Its first task is to create more processes using fork() and exec\*()

However, termination can happen, at any time and in any place. Once it happens, one orphan will be created( a process with no parent node).
- This is no good because an orphan turns the hierarchy from a tree into a forest.
- In linux, the “init” process will become the step-mother of all orphans(**Re-parenting**)
- In windows, it remains a forest-like process hierarchy.

**What had happened during re-parenting?**
- One parent process calls exit()
- for its children, the exit() system call changes parent pointer to the "init process"
- The init process accepts its new child by adding the new child into the list of children.
>The re-parenting operation enables something called background jobs in Linux. 

Process lifecycle
-
![enter image description here](https://lh3.googleusercontent.com/13p1-atEmlhILaFSMzNVSGcmyheyJ9QlNdXPabyqxeko6nEwTDvKP3MU1btEDN4wWUgokzqQQq4)
|State| Description |
|--|--|
|Start |This is the initial state when a process is first started/created.|
| Ready  | The process is waiting to be assigned to a processor. Ready processes are waiting to have the processor allocated to them by the operating system so that they can run. Process may come into this state after Start state or while running it by but interrupted by the scheduler to assign CPU to some other process.  |
|Running|Once the process has been assigned to a processor by the OS scheduler, the process state is set to running and the processor executes its instructions.|
|Waiting|Process moves into the waiting state if it needs to wait for a resource, such as waiting for user input, or waiting for a file to become available.|
|Terminated or Exit|Once the process finishes its execution, or it is terminated by the operating system, it is moved to the terminated state where it waits to be removed from main memory.|
