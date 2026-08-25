## Operating System

OS -> software that manages hardware and provides services to applications
CPU -> manages CPU time
RAM -> manages memory
storage -> manages files/directories
I/O -> manages devices like keyboard, mouse, USB
processes -> manages running programs
security -> controls access
application -> OS -> hardware
kernel -> core part of the OS
handles CPU, memory, processes, filesystems and devices

## Kernel vs User Space

The main idea is that **programs should not be allowed to directly access everything on the computer**.

so the system is div into 2 spaces :
### User space

**User space** is where normal applications run.
These programs have **limited access** to the system.

Examples:
		Chrome
		Firefox
		VLC
		Terminal
		Python program
		Text editor

### Kernel space

it is the space where the kernel runs 
The kernel has much higher privileges and can access system resources such as:
										CPU
										RAM
										Devices
										Disk
										Network
For example, when a program wants to read a file:

```
Application
    ↓
Kernel
    ↓
File system
    ↓
Disk
```

### Why ?

If every program had unrestricted access to memory and hardware, a buggy or malicious program could:

- modify another program's memory
- access protected data
- interfere with hardware
- crash the entire system

User space -> limited privileges
Kernel space -> high privileges


### Kernel vs User Space

user space -> where normal applications run
examples -> Chrome, terminal, text editor, Python programs

kernel space -> where the kernel runs
has high privileges and access to CPU, RAM, devices, filesystem

user space -> limited access
kernel space -> privileged access

application -> system call -> kernel -> hardware

separation is mainly for security and system stability

### System calls

system call -> way for a program to request a service from the kernel

==**A system call is not just a normal function call. It uses a special CPU mechanism to safely transition from user mode into kernel mode.**==


user program -> system call -> kernel -> resource

examples:
read() -> read data
write() -> write data
open() -> open file
close() -> close file
fork() -> create process
exec() -> run program
wait() -> wait for process

normal function -> stays in user space
system call -> user space -> kernel space

### Types of OS

batch OS -> jobs executed in batches, little user interaction
example -> IBM OS/360

time-sharing OS -> CPU time shared between multiple processes/users
example -> UNIX, Linux, Windows, macOS

real-time OS -> predictable response within a required time
example -> FreeRTOS, VxWorks, QNX

distributed OS -> multiple computers work together as one coordinated system
example -> Amoeba, Plan 9

network OS -> provides/manages network resources and services
example -> Windows Server, Novell NetWare

embedded OS -> designed for a specific device/purpose used in tv, embedded systems such as washing machines , routers, car systems and other iot devices too
example -> Embedded Linux, Zephyr, FreeRTOS

### Processes and Threads

program -> a file containing instructions
process -> a program that is currently running
thread  -> a unit of execution inside a process

#### Program
the OS loads the program into memory and starts executing it.

Now it becomes a **process**.

#### Thread

A **thread** is a unit of execution inside a process.

A process can have **one or multiple threads**.
Process
├── Thread 1
├── Thread 2
└── Thread 3

# Processes and Threads

program -> file containing instructions
process -> program that is currently running
thread -> unit of execution inside a process

process -> own virtual address space
thread -> shares process memory/resources

process
├── thread 1
├── thread 2
└── thread 3

threads share:
code
data
heap
resources

each thread has its own:
stack
registers
program counter

process -> generally heavier
thread -> generally lighter

concurrency -> tasks make progress during overlapping periods
parallelism -> tasks execute at the same time on different CPU cores