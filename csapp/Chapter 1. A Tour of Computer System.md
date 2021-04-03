```c
#include <stdio.h>
int main()
{ 
  	printf("hello, world\n");
}
```



## 1.2 Programs Are Translated by Other Programs into Different Forms



```shell
gcc -0 hello hello.c
```

![WeChat7329f7b9d9e270e2fb75ce8859dbd800.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp04n3vqk2j32140jgh4d.jpg)

- Preprocessing phase

  The preprocessor modifies the original C program according to directives tha begin with the # character.

  For example, the #include<stdio.h> command in line 1 of hello.c tells the preprocessor to read the contents of the system header file stdio.h and insert it directly into the program text.

- Compilation phase

  The compiler translates the text file hello.i into the text file hello.s, which contains an assembly-language program.

  Assembly language is useful because it provides a common output language for different compilers for different high-level languages.

  For example, C compilers and Fortran compilers both generate output files in the same assembly language.

- Assembly phase

  The assembler translates hello.s into machine-language instructions, packages them in a form known as relocatable object program, and stores the result in the object file hello.o. 

  The hello.o file is a binary file whose bytes encode manchine language instructions rather than characters.

- Linking phase

  Our hello program calls the printf function, which is part of the standard C library provided by every C compiler. And printf.o must somehow be merged with our hello.o program. The linker handles this merging. The result is the hello file, which is an executable object file that is ready to be loaded into memory and executed by the system.

## 1.4 Processors Read and Interpret instructions Stored in Memory

### 1.4.1 Hardware Organization of a System

![WeChat259b6d94b3e35d09869d54735f4996ba.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp095ytnixj31es10ckbq.jpg)

CPU : Central Processing Unit

ALU: Arithmetic/Logic Unit

PC : Program count

USD: Universal Serial Bus

> CPU instruction

- Load

  Copy a byte or a word from main memory into a register, overwriting the previous contents of the register.

- Store

  Copy a byte or a word from a register to a location in main memory, overwriting the previous contents of that location

- Operate

  Copy the contents of two registers to the ALU, perform an arithmetic operation on the two words, and store the result in a register, overwriting the previous contents of that register.

- Jump

  Extract a word from the instruction itself and copy that word into the program count(PC), overwriting the previous value of the PC

### 1.4.2 Runing the hello Program

When we type './hello' at the keyboard, the shell program reads each one into a register, and then stores it in memory. 

![WeChatee95940b49def841ca58b366b772a627.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0bqsr3l5j320w11g1jx.jpg)

When we hit the enter. The shell then loads the executable hello file by copies the code and data in the hello object file from disk to main memory.

Using a technique known as direct memory access(DMA), the data travels directly from disk to main memory, without passing through the processor.

![WeChat8db23fd0486f9ed1da6ab76044a70250.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0bts7opnj31go11wnmt.jpg)

The processor begins executing the machine-language instructions in the hello program.

These instructions copy the bytes in the "hello,world\n" string from memory to the register file, and from there to the display device, where they are displayed on the screen.

![WeChatabddc685d235759671787e5a50ca07a6.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0bxn503hj325w0y81kx.jpg)

## 1.5 Caches Matter

To deal with the **processor-memory gap**, we use **cache memories** that serve as temporary staging areas for information that the processor is likely to need in the near future.

![WeChat0d30fa8e9d1f32056dede5f459e1fbec.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0ebfubirj326s0n015l.jpg)

Systems may have three levels of cache: L1, L2,L3.

L1 is very fast, but the space is small.

L2 is slower than L1, but the space is larger than L1.

The L1 and L2 caches are implemented with a hardware technology known as **static random access memory (SRAM)**.

The idea behind caching is that a system can get the effect of both a very large memory and a very fast one by exploiting locality, **the tendency for programs to access data and code in localized regions**.

## 1.6 Storage Devices form a Hierarchy

![WeChat4bf265807b72d66d8b78a25362cc691c.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0epa1vpbj321o13kqv5.jpg)

The main idea of a memory hierarchy is that storage at one level serves as a cache for storage at the next lower level. For example, the register file is a cache for the L1 cache.

## 1.7 The Operating System Manages the Hardware

All attampts by an application program to manipulate the hardware must go through the operating system.

> The operating system has two primary purpose:

1. to **protect the hardware from misuse** by runaway application.
2. to **provide applications with simple and uniform mechanisms for manipulating** complicated and often wildly different low-level **hardware devices**.

![WeChat7485d63a958c03161b04752ac0de791f.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0evxf8soj31s009otir.jpg)

The operation system achieves both goals via the fundamental abstraction : processes, vitual memory and files.

- files are abstractions for I/O devices.
- virtual memory is an abstraction for both the main memory and disk I/O devices.
- processes are abstractions for the processor, main memory and I/O devices.

![WeChate706bb38453b6ce06fb84c04c83a748c.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp0ewewu40j31p00fsajr.jpg)

### 1.7.1 Processes

A process is the operating system's abstraction for a running program.

When we ask system to run  the hello program, the shell carry out our request by invoking a special function known as a **system call** that passes control to the operating system.

![WeChatbb2c7410609230915d29b24988d914f1.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp281so14ij31co0ig188.jpg)

> context switching 上下文切换

a context switch is the process of storing the state of a process or thread, so that it can be restored and resume execution at a later point. This allows multiple processes to share a single CPU, and is an essential feature of a multitasking operating system.

> Memory interleaving 交叉编址

Memory interleaving is less or more an Abstraction technique. Though it is abit different from Abstraction.

It is a Technique which divides memory into a number of modules such that Successive words in the address space are placed in the Different module.



![WeChat6cbc237976adad0163d231ddd697b646.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp22vzc5orj31so18khdt.jpg)

Here, Most significant bit(MSB) provides the Address of the Module & least significant bit (LSB) provides the address of the data in the module.

![WeChatd26159bf496e39bcef29efd66c033b26.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp22wumn8mj31sw174kjl.jpg)

Least Significant Bit (LSB) provides the Address of the Module & Most significant bit (MSB) provides the address of the data in the module.

> Why we use Memory Interleaving?

Whenever, Processor request Data from the main memory. A block of Data is transferred to the cache and then to processor. So whenever a cache miss occurs the data is to be fetched from main memory. But main memory is relatively slower than the cache. So to improve the access time of the main memory interleaving is used.

We can access all four Module at the same time thus achieving **Parallelism**.



### 1.7.2 Threads

A process can actually consist of multiple execution units, called threads. 

> Feature

- running in the context of the process
- share the same code and global data



### 1.7.3 Virtual Memory

Virtual memory is an abstraction  that provides each process with the illusion that it has exclusive use of the main memory.

Each process has the same uniform view of memory, which is known as its **virtual address space**.

In Linux, the **topmost region** of the address sapce is reserved for code and data **in the operating system** that is common to all processes. The **lower region** of the address space holds the code and data **defined by the user's process**.

> virtual address space

![WeChat11b1b4d2a1a442f603066f8df9939b8b.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp28y70pp7j31xk124x1c.jpg)

- Program code and data

  The code and data areas are initialized directly from the contents of an executable object file.

- Heap

  The heap expands and contracts dynamically at run time as a result of calls to C standard livrary routines such as malloc and free.

- Shared libraries

- Stack

- Kernel virtual memory

  The kernel is the part of the operating system that is always resident in memory.The top region of the address space is reserved for the kernel. Application programs are not allowed to read or write the contents of this area or to directly call functions defined in the kernel code.

> basic idea of virtual memory

To **store the contents of a process's virtual memory on disk**, then **use the main memory as a cache for the disk**.

### 1.7.4 Files

A file is a sequence of bytes, nothing more and nothing less.

Every I/O device, including disks, keyboards, displays, and even networks is modeled as a file. All input and output in the system is performed by reading and writing files, using a small set of **system calls** known as **Unix I/O**.

## 1.8 Systems Communicate with Other Systems Using Networks

Network can be viewed as just another I/O device.

When the system copies a sequence of bytes from main memory to the network adapter, the data flows across the network to another machine, instead of, to a local disk drive. Similarly, the system can read data sent from other machines and copy this data to its main memory.

![WeChate339100ce15b71ab019aba11fc66447f.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp29xgt7arj320412sx1r.jpg)

![WeChat40d44c3e56297be376c673265b82918a.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp2a5pws99j31zo0kchad.jpg)

##  1.9 Important Themes

### 1.9.1 Concurrency and Parallelism

> Thread-Level Concurrency

- uniprocessor system 单处理器系统

- multiprocessor system 多处理器系统

- multi-core processor 多核处理器

- hyperthreading 单核多线程

  also called simultaneous multi-threading.



![WeChatdf28e693ab3a6c8cb2058b4e96fff540.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp2cj7bncij31ig0i47jb.jpg)

![WeChat38e4f4837f9d2f9ca6fb4035947731d4.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp2cqu477yj31ws16gtta.jpg)

> Instruction-Level Parallelism

At a much lower level of abstraction, modern processors can execute multiple instructions at one time, a property known as **instruction-level parallelism**.

**A superscalar processor** is a CPU that implements a form of parallelism called instruction-level parallelism within a single processor.

> Single-Instruction, Multiple-Data (SIMD) Parallelism

Many modern processors have special hardware that allows a single instruction to cause multiple operations to be performed in parallel, a model known as single-instruction, multiple-data, or "SIMD" parallelism.

### 1.9.2 The Importance of Abstractions in Computer Systems

![WeChat90fc8c1dfd77581912c27a4322729173.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp2eig0snjj31ys0jsh6s.jpg)



## 1.10 Summary

- Processors read and interpret binary instructions that are stored in main memory.
- Storage devices that are higher in the hierarchy serve as caches for devices that are lower in the hierarchy.
- The operating system kernel serves as an intermediary between the application and the hardware. It provides three fundamental abstractions:
  1. File abstractions for I/O devices
  2. Virtual memory is an abstraction for oth memory and disks.
  3. Processes are abstraction for the processor, main memory, and I/O devices.
- From the viewpoint of a particular system, the network is just another I/O device.

