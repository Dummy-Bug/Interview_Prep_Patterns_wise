# OS Quick Notes

[Notes](https://drive.google.com/file/d/1FAxjhyIlsGGouIyCPyR3xqKVgU7mhEmQ/view)

<br>

## @ Basics of OS
An operating system is a piece of software that manages all the resources of a computer system, both hardware and software, and provides an environment in which the user can execute his/her programs in a convenient and efficient manner.

<br>

#### 1. Kernel 
A kernel is that part of the operating system which interacts directly with the hardware and performs the most crucial tasks. 

<br>

#### 2. Shell
A shell, also known as a command interpreter, is that part of the operating system that receives commands from the users and gets them executed.

<br>

#### 3. System Call
A system call is a mechanism using which a user program can request a service from the kernel. User programs typically do not have permission to perform operations like accessing I/O devices and communicating other programs. A user program invokes system calls when it requires such services. System calls provide an interface between a program and the operating system.

Eg: fork, exec, getpid, getppid, wait, exit.

<br>

#### 4. User Mode Vs Kernel Mode
1. In it’s life span a process executes in user mode and kernel mode. The User mode is normal mode where the process has limited access. While the Kernel mode is the privileged mode where the process has unrestricted access to system resources like hardware, memory, etc.

2. Any crash in kernel mode brings down the whole system. But any crash in user mode brings down the faulty process only.

3. The kernel provides System Call Interface (SCI), which are the entry points for kernel. System Calls are the only way through which a process can go into kernel mode from user mode. Kernel space switching is achieved by Software Interrupt, which changes the processor mode and jump the CPU execution into interrupt handler, which executes corresponding System Call routine.

[Explaination](https://www.geeksforgeeks.org/user-mode-and-kernel-mode-switching/)

<br>

#### 5. MicroKernel
1. In microkernel, user services and kernel services are kept in separate address space.
2. If a service crashes, it does not effect on working of microkernel.
3. Smaller in size and slow execution.
4. The advantage to a microkernel is that any failed service can be easily restarted, for instance, there is no kernel halt if the root file system throws an abort.
5. Eg: QNX, Symbian, Mac OS X

<br>

#### 6. Monolithic Kernel
1. In monolithic kernel, both user services and kernel services are kept in the same address space.
2. If a service crashes, the whole system crashes. 
3. Larger then microkernel and fast execution.
4. Linux, Microsoft Windows (95,98), Solaris

[Monolithic Vs Microkernel Explained](https://techdifferences.com/difference-between-microkernel-and-monolithic-kernel.html)

<br>

#### 7. Booting
Booting is the process of starting the computer and loading the kernel. When a computer is turned on, the power-on self-tests (POST) are performed. Then the bootstrap loader, which resides in the ROM, is executed. Then bootstrap loader loads the kernel.

<br>

#### 8. Power on self test (POST) 
It is a set of routines performed by firmware or software immediately after a computer is powered on, to determine if the hardware is working as expected. Roles of POST are:
1. Initialize BIOS.
2. Identify, organize, and select which devices are available for booting.
3. Verify CPU registers.
4. Verify some basic components like DMA, timer, interrupt controller.

<br>

#### 9. Device drivers
The role of drivers is to provide an abstraction of the hardware so applications can use it through the OS API instead of having to know specific details. It also allows for sharing the same piece of hardware among many applications simultaneously.

[Explaination](https://www.geeksforgeeks.org/device-driver-and-its-purpose/)

<br>

#### 10. Virtual Device Driver (VDD)
virtual device drivers manages the virtual devices. They controls/manages the data flow from different application used by different users to the same hardware.

<br>



## @ Process Scheduling and Management

Note: Kernel is the first process to be created, is the ancestor of all other processes and is at the root of the process tree.

<br>

#### 1. Zombie process
A zombie process is a process that has terminated but its PCB still exists because its parent has not yet accepted its return value.

<br>

#### 2. Difference between process and thread
1. processes are typically independent while threads exist as parts of a process
2. processes carry considerably more state information than threads, whereas multiple threads within a process share process state as well as memory and other resources
3. processes have separate address spaces, whereas threads share their address space
4. processes interact only through system-provided inter-process communication mechanisms
5. context switching between threads in the same process is typically faster than context switching between processes

<br>

#### 3. Dispatcher
A dispatcher is the module of the operating system that gives control of the CPU to the process selected by the CPU scheduler. Dispatch latency is the time taken to stop a process and start another. Dispatch latency is a pure overhead.

<br>

#### 4. Priority inversion
A low-priority process gets the priority of a high-priority process waiting for it.
