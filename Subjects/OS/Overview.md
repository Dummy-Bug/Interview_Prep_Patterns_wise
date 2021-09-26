# Introduction to OS

[Notes](https://drive.google.com/file/d/1FAxjhyIlsGGouIyCPyR3xqKVgU7mhEmQ/view)

<br>

## @ Know Your OS
An operating system is a piece of software that manages all the resources of a computer system, both hardware and software, and provides an environment in which the user can execute his/her programs in a convenient and efficient manner.

<br>

### 1. RAM (Random Access Memory)
1. The information stored in this type of memory is lost when the power supply to the PC or laptop is switched off. 
2. The information stored in RAM can be checked with the help of BIOS. 
3. It is generally known as the main memory, or volatile memory.
4. Much faster to read from and write as compared to ROM.
5. RAM chips are widely used for starting and loading the operating system and applications.

<br>

**Types of RAM**

1. DRAM : Dynamic RAM must be continuously refreshed, otherwise all contents are lost.
2. SRAM : Static RAM is faster, needs less power but is more expensive. However, it does need to be refreshed like DRAM.
3. SDRAM: Synchronous Dynamic RAM can run at very high clock speeds.

<br>

### 2. ROM (Read Only Memory)
1. It is a permanent type of memory. Its content are not lost when the power supply is switched off. 
2. The computer manufacturer decides the information of ROM, and it is permanently stored at the time of manufacturing, which cannot be overwritten by the user.
3. A ROM, non-volatile memory stores only several megabytes (MB) of data, up to 4 MB or more per chip.
4. ROM stores the instructions for the computer to start up when it is turned on again.

Note: The processor can’t directly access the information that is stored in the ROM. In order to access ROM information first, the information is transferred into the RAM, and then it can be executed by the processor.

<br>

**Types of ROM**

1. MROM : Mask ROM can be programmed only by an integrated circuit manufacturer.
2. PROM : Programmable Read-Only memory is written or programmed using a particular device.
3. EPROM : Erasable Programmable Read-only memory. We can erase instructions by exposing memory to ultraviolet light.
4. EEPROM : Electrically Erasable Programmable Read-Only Memory stores and deletes instructions on a special circuit.

<br>

[RAM Vs ROM](https://www.guru99.com/difference-between-rom-ram.html)

<br>

### 3. Virtualization
1. Virtualization provides multiple small virtual severs within the framework of large physical environment.
2. VM allocates resources to each virtual server along with its own OS, drivers, binaries, and applications.
3. These VMs are isolated from each other.
4. **HyperVisor** is a software(or hardware) that provides a virtual layer that seprates the physical server from virtual machines and provide isolated environment to all VMs.
5. VMs can update and modify applications within their own space without affecting other VMs.
6. If one VM becomes corrupted, it will not affect other VMs and host server.

Virtualization softwares: Vsphere by Vmware, Xen by Citrix, Hyper-V by microsoft, Sparc by Oracle

<br>

### 4. Containerization
1. This allows applications to run in Isolation while they are using same system resources and same OS.
2. It is lightweight and faster then Virtualization.
3. A **Container Engine** can easily manage large numbers of containers so we can create, add and remove containers as needed.
4. Efficient hardware utilization

Containerization softwares: Docker, Kubernetes by Google

<br>

### 5. Virtualization Vs Containerization
**1. Speed**

The container starts immediately since OS is already up and running, while VM needs to start the entire OS which includes full Boot Process.

<br>

**2. Security and Isolation**

Each VM can incorporate its own security protocols since they are running in fully isolated enviroment. Containers only isolate data and applications hence they provide less secure environment.

<br>

**3. Portablility**

Containers are easier to transfer, whereas VMs need to transfer whole copy of OS, kernel, system libs etc. along with data and apps.

<br>

**4. What to Choose ?**

If business needs is to run multiple applications that require full functionality of a dedicated OS, then VM will be best option. Whereas, if most of the applications have same OS requirements, then Containers will be much more practical solution.

Containers are suitable for short-term applications, while VMs are suited for apps that need to used for an extended period of time.

<br>

### 5. BIOS 
BIOS (Basic Input Output System) is a program that's stored in non-volatile memory such as ROM or flash memory. BIOS is responsible for waking up your computer’s hardware components, ensures they’re functioning properly, and then runs the bootloader. Settings like hardware configuration, system time, and boot order are located here.

The BIOS goes through a **POST** (Power On Self Test), before booting your operating system. It checks to ensure your hardware configuration is valid and working properly. After the POST finishes, BIOS looks for a **Master Boot Record** (MBR), to launch the bootloader.

<br>

### 6. UEFI
The traditional BIOS still has serious limitations. It can only boot from drives of 2.1 TB or less, due to the way the BIOS’s Master Boot Record system works. It has trouble initializing multiple hardware devices at once, which leads to a slower boot process. In 2007, Intel, AMD, Microsoft, and PC manufacturers agreed on a new *Unified Extensible Firmware Interface* (UEFI) specification. 

Advancements in UEFI:
1. The UEFI firmware can boot from drives of 2.2 TB or larger upto 9.4 zettabytes.
2. Boot process is faster.
3. It supports Secure Boot, which ensures no malware has tampered with the boot process.

<br>

### 7. HardDisk Partitions
Hard disks, USB drives, SD cards — anything with storage space must be partitioned. An unpartitioned drive can’t be used until it contains at least one partition, but a drive can contain multiple partitions.

**Partitions are necessary** because you can’t just start writing files to a blank drive. You must first create at least one container with a file system. After creating a partition, the partition is formatted with a file system, like the NTFS file system on Windows drives, FAT32 file system for removable drives, HFS+ file system on Mac computers, or the ext4 file system on Linux. Files are then written to that file system on the partition.

**Fun Fact:** If you have multiple partitions in USB, multiple different drives would appear when you plugged your USB drive into your computer.

<br>

### 8. System Reserved Partition
Windows computers come with a separate recovery partition where the files you need to restore your Windows operating system to its factory default settings are stored. When you restore Windows, the files from this partition are copied to the main partition. The recovery partition is normally hidden so you can’t access it from Windows and mess it up. If the recovery files were stored on the main system partition, it would be easier for them to be deleted, infected, or corrupted.

<br>

### 9. Bootable and System Partitions
The **Boot partition** is a primary partition that contains the bootloader, a piece of software responsible for booting the operating system. Whereas, the **System partition** is the disk partition that contains the operating system folder, known as the system root.

In Linux, a single partition can be both a boot and a system partition if both /boot/ and the root directory are in the same partition.

<br>

### 10. MBR and GPT
MBR and GPT are two different ways of storing the partitioning information on a drive. This information includes where partitions begin and end on the physical disk, so your operating system knows which sectors belong to each partition and which partition is bootable.

<br>

**MBR (Master Boot Record)**

MBR is a special boot sector located at the beginning of a drive. This sector contains a boot loader for the installed operating system and information about the drive’s logical partitions. The boot loader is a small bit of code that generally loads the larger boot loader from another partition on a drive.

MBR only works with disks up to 2 TB in size. MBR also only supports up to four primary partition (if you want more, you have to make one of your primary partitions an “extended partition” and create logical partitions inside it).

On an MBR disk, the partitioning and boot data is stored in one place. If this data is overwritten or corrupted, you’re in trouble!

<br>

**GPT (GUID Partition Table)**

It’s a new standard that’s gradually replacing MBR. It’s called GUID Partition Table because every partition on your drive has a “globally unique identifier”.

GPT based drives can be much larger, with size limits dependent on OS and its file systems. GPT also allows for a nearly unlimited number of partitions, and you don’t have to create an extended partition to make them work.

GPT stores multiple copies of this data across the disk, so it’s much more robust and can recover if the data is corrupted.

<br>

### 11. Monolithic Vs Microkernel
A kernel is that part of the operating system which interacts directly with the hardware and performs the most crucial tasks. 

<br>

**Monolithic Kernel**

1. Monolithic kernel is a single large process running entirely in a single address space. It is a single static binary file. 
2. All kernel services exist and execute in the kernel address space.
3. The kernel can invoke functions directly.
4. If a service crashes, the whole system crashes.
5. Bigger and Faster then Microkernels.
6. Examples of monolithic kernel based OSs: Unix, Linux.

<br>

**MicroKernel**

1. In Microkernels, the kernel is broken down into separate processes, known as servers. Some of the servers run in kernel space and some run in user-space. 
2. All servers are kept separate and run in different address spaces. 
3. Servers invoke "services" from each other by sending messages via IPC (Interprocess Communication). 
4. This separation has the advantage that if one server fails, other servers can still work efficiently. 
5. More Crash Resistent and Portable then Monolithic kernels.
6. Examples of microkernel based OSs: Mac OS X.

<br>

### 12. Why Windows is a monolithic operating system ?
Because the kernel mode protected memory space is shared by the operating system and device driver code. Therefore no protection between OS and drivers. But it does have some attributes of a microkernel OS such as:
1. Kernel-mode components don’t reach into one another’s data structures.
2. Use formal interfaces to pass parameters and access and/or modify data structures

Hence Windows follow **Hybrid architecture**.

**Why not pure microkernel?**

Separate address spaces would mean context switching to call basic OS services. Most other commercial OSs (Unix, Linux, VMS etc.) have the same design.

<br>

### 13. What happens when we turn on computer?
When the computer is turned on or restarted, it first performs the power-on-self-test, also known as POST. It ensures whether your hardware configuration is valid and working properly. If the POST is successful and no issues are found, BIOS looks for a Master Boot Record (MBR), to launch the bootloader. The bootloader will load the operating system for the computer into memory. The computer will then be able to quickly access, load, and run the operating system. 

Modern system use two stage boot loading. In the first step a tiny program is loaded from a sector of Hard disk. This tiny program in turn load a program from some where in the disk, which is called bootloader. And finally bootloader loads the OS.

<br>

### 14. Where is the bootloader stored ?
Bootloader is neither stored in ROM, nor in RAM. It is actually stored on **Hard disk** (or other Boot device, such as bootable CDROM, USB drives etc), precisely speaking the first sector of the hard disk, which is of size 512 bytes and often referred to as the boot-sector.

<br>

### 15. Difference between 32-bit and 64-bit OS
These Numbers represents the no. of bits used to represent the adresses. Or Numbers of bits that a Address register can hold. 

```
A 32-bit system can access 2^32 memory addresses, i.e 4 GB of RAM.

A 64-bit system can access 2^64 memory addresses, i.e actually 18-Quintillion bytes of RAM.
```

**Why 64-bit processors are fast ?**

64-bit processors can come in dual-core, quad-core, six-core, and eight-core versions for home computing. Multiple cores allow for an increased number of calculations per second that can be performed, which can increase the processing power and help make a computer run faster. Using 64-bit one can do a lot in multi-tasking, user can easily switch between various applications without any windows hanging problems. 

Note: A computer with a 64-bit processor can have a 64-bit or 32-bit version of an OS installed. However, with a 32-bit OS, the 64-bit processor would not run at its full capability.

<br>

### 16. Shell
A shell, also known as a command interpreter, is that part of the operating system that receives commands from the users and gets them executed.

<br>

### 17. System Call
A system call is a mechanism using which a user program can request a service from the kernel. User programs typically do not have permission to perform operations like accessing I/O devices and communicating other programs. A user program invokes system calls when it requires such services. System calls provide an interface between a program and the operating system.

Eg: fork, exec, getpid, getppid, wait, exit.

<br>

### 18. User Mode Vs Kernel Mode
1. In it’s life span a process executes in user mode and kernel mode. The User mode is normal mode where the process has limited access. While the Kernel mode is the privileged mode where the process has unrestricted access to system resources like hardware, memory, etc.

2. Any crash in kernel mode brings down the whole system. But any crash in user mode brings down the faulty process only.

3. System Calls are the only way through which a process can go into kernel mode from user mode. 

[Explaination](https://www.geeksforgeeks.org/user-mode-and-kernel-mode-switching/)

<br>

### 19. Device drivers
The role of drivers is to provide an abstraction to hardware resources, so applications can use it through the OS API instead of having to know specific details. It also allows for sharing the same piece of hardware among many applications simultaneously.

[Explaination](https://www.geeksforgeeks.org/device-driver-and-its-purpose/)

<br>




