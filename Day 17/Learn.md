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
