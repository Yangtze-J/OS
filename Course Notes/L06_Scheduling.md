## Job Schedule

## Context Switching
Scheduling is the procedure that decides which process to run next;
**Context switching is the actual switching procedure, from one process to another.**

![enter image description here](https://lh3.googleusercontent.com/g716q7VDTNPTaS73MTW1S7Uyw8WqSl54Qmy6RzcyVik6RtTaWSJ9qq-LXQj6r1LFniC_CDYBEzQ)

Suppose that **one process gives up running on the CPU**, 
- then its state transfers from Running to interruptible/uninterruptable Wait,
- Now it's time for the scheduler to choose the next process to run.

But **before the scheduler can seize control of the CPU,** a very important step must be taken:
**Backup all current context** of that process **to the kernel space's PCB**:
- current register values
- program counter(which line of code the current program is at)

Say, the scheduler decides to schedule another process in the ready queue,then the scheduler has to **load the context of that process from the main memory to the CPU**.

We call the entire operation: context switching.

### Context switching is expensive
Direct costs in kernel:
- save and restore registers
- switch address space(expensive instructions)

Indirect costs
 - cache, buffer cache, &TLB misses

## Scheduling
Scheduling is required because **the number of computing resource** - the CPU - **is limited.**
### classes of process scheduling
- preemptive scheduling ( 抢占式
- Non-preemptive is out

### scheduling algorithms
Inputs to these algorithms:
- A set of tasks
- For each task:
	- arrival time
	- CPU requirements

|Offline scheduling algorithm|Online scheduling algorithm|
|--|--|
|assumes that you know the sequence of processes that a scheduler will face| have no such assumptions|
|Theoretical baseline|Practical use|

#### algorithm evaluation
- the number of context switches
- individual& average turnaround time : The time between the arrival of the task and its termination 
- individual& average waiting time: The accumulated time that a task has waited for the CPU.

### Different algorithms
- shortest-job-first (SJF)
- Round-robin (RR)
- Priority scheduling with multiple queues

#### Preemptive SJF
Whenever a new process arrives at the system, the scheduler steps in and selects the next task based on their remaining CPU requirements.

#### Round-robin
Round-Robin (RR) scheduling is preemptive.
- Every process is given a quantum, or the amount of time allowed to execute.
- Whenever the quantum of a process is used up (i.e., 0), the process releases the CPU and this is the preemption.
- Then, the scheduler steps in and it chooses the next process which has a non-zero quantum to run.
- If all processes in the system have used up the quantum, they will be re-charged to their initial values.
- Processes are therefore running one-by-one as a circular queue, for the basic version (i.e., no priority)
  - New processes are added to the tail of the ready queue
  - New process arrives won’t trigger a new selection decision

![enter image description here](https://lh3.googleusercontent.com/PNs308y6tbYdPKLHP-OPlmUCErTCbBeWPJ1QOBGMjUqUllznFaMyUZVmK90PzAib69kvYoJsJA5b)


![enter image description here](https://lh3.googleusercontent.com/CixwAHx6PejeffgrqZ43foz_fUl00QYWHAiYqp2TGilYB_ExOALW3UJm7wxdz7zubNot9jru_9-O)

So, the RR algorithm gets all the bad! Why do we still need it?
The responsiveness of the processes is great under the RR algorithm. 
E.g., you won’t feel a job is “frozen” because **every job gets the CPU from time to time**!

#### Priority Scheduling
A task is given a priority (and is usually an integer).
A scheduler selects the next process based on the priority
A higher priority process + RR = priority queue + new process arrival triggers a new selection

|Static priority|Dynamic priority|
|--|--|
|Every task is given a fixed priority|Every task is given an initial priority|
|The priority is fixed throughout the life cycle |The priority is changing|

##### Multiple queue priority scheduling
It is still a priority scheduler.
But, at each priority class, different schedulers may be deployed.
The priority can be a mix of static and dynamic.

**Scheduling for modern servers**
Symmetric multiprocessing (SMP)
- All processes/threads share a common ready queue
- Scheduling:
    - Each processor/core scheduler examines the common ready queue and select a process to execute
- Affinity Scheduling:
  - Soft affinity: attempt to keep the same process/thread on the same core
  - vs. Hard affinity: use pthread_attr_setaffinity_np() to manually bind a thread to a specific core in your pThread program
  - Especially important when we are reaching NUMA world










