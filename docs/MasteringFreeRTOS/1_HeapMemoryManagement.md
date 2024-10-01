---
layout: default
title: 1_HeapMemoryManagement
parent: Topic_FreeRTOS
nav_order: 7
---

# Heap Management Overview
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 1. Introduction
In this chapter, we will explore what 'Heap' means in terms of C programming and Microprocessor. Then, we will examine several features that FreeRTOS provides. In modern operating systems, numerous applications run concurrently, and each application should allocate a suitable Heap region owned by itself to manipulate data dynamically. Otherwise, the system will crash by competing with several application running at the same time. As a quick aside, in Over-The-Air (OTA) systems, when deleting an old application and installing a new one on the operating system, managing the Heap (RAM) region reserved for them is crucial to prevent system malfunctions. Therefore, understanding the Heap will help you become a competent C programmer.

Heap memory is a scheme for accessing RAM in a CPU or Microprocessor dynamically. Here, I want to emphasize the terms <b>'Dynamic'</b> and <b>'RAM'</b>. Unlike the 'Stack', which is allocated statically as a memory region in RAM, the Heap is a memory region allocated dynamically during program runtime. If you have experience with using <b>'malloc()'</b> and <b>'free()'</b>, you'll understand this concept.

As for its advantages,
1. Whenever an application or you require it, you can reserve Heap memory with different sizes. This is one of the biggest advantages of using the Heap.
2. The Heap is shared by all software functions, but it requires the programmer to manage Heap memory, which can be complex.

Regarding its drawbacks,
1. The implementation of accessing Heap memory may vary based on different operating systems and CPUs.
2. Heap is prone to Memory Leakage (Memory Fragmentation) since the required Heap size will differ every time. This is one reason why your computer may slow down as you use the device. To address this problem, Java provides an 'Automatic Garbage Collection' function.
3. Heap has slower access times compared to the Stack.

# 2. Features provided by FreeRTOS
## 2.1 Concept
Kernel objects include <b>tasks</b>, <b>queues</b>, <b>semaphores</b>, and <b>event groups</b>. These kernel objects are stored in the heap memory of the RAM. One distinct feature is that FreeRTOS abstracts memory allocation from the core codebase. In other words, it treats memory allocation as a portable layer, providing users with flexibility. Therefore, users can implement their own memory allocation by replacing the FreeRTOS-provided memory allocation.

## 2.2 Statically allocated RAM (Heap)
1. FreeRTOS provides a statically allocated RAM feature. In the FreeRTOSConfig.h file, configSUPPORT_STATIC_ALLOCATION should be set to 1.
2. A statically allocated heap makes FreeRTOS appear to consume a lot of RAM because the heap becomes part of the FreeRTOS data.

## 2.3 Dynamically allocated RAM (Heap)
FreeRTOS has implemented <b>pvPortMalloc()</b> and <b>vPortFree()</b> for dynamic memory allocation, instead of using <b>malloc()</b> and <b>free()</b> from the C standard library. The main reasons are:
* The implementation of <b>malloc()</b> and <b>free()</b> is relatively large, making it unsuitable for the small size of embedded systems.
* Using the C library functions may cause memory fragmentation in the embedded system.
* The linker configuration and debugging may be more difficult with the C library functions.

FreeRTOS provides five types of heap memory, which I will pave the way to understand FreeRTOS Memory Mangement. Detailed explanations can be found at [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

### 2.3.1 Heap_1
1. Heap_1 is a basic heap implementation. Heap_1 only creates tasks and other kernel objects before starting the FreeRTOS scheduler.
2. This means that memory is allocated only before the application starts, and the allocated memory remains for the application's lifetime.
3. <b><span style="color:Greenyellow"> It does not support vPortFree().</span></b>
4. The purpose of Heap_1 is to be used in commercially critical and safety-critical systems, such as those using the Rust programming language, which prohibits dynamic memory allocation.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap_1.png" width="500"/>
    <br><b>[Pic.1] Heap_1 Heap Memory Creation</b>
</p>

### 2.3.2 Heap_2
1. Heap_2 is superseded by Heap_4, which is an enhanced version of Heap_2. FreeRTOS does not recommend using Heap_2 for new designs.
2. Heap_2 uses the <b><span style="color:Greenyellow"> best-fit algorithm</span></b> to allocate memory. For example, if pvPortMalloc() requests 20 bytes of RAM, and the available RAM blocks are 5 bytes, 25 bytes, and 100 bytes, FreeRTOS will choose the 25-byte RAM block. It then divides it into a 20-byte block and a 5-byte block, returning the pointer to the 20-byte block. The remaining 5-byte block will be used in the future.

Note that Heap_2 does not combine adjacent free blocks into a single large block.
{: .highlight }

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap_2.png" width="500"/>
    <br><b>[Pic.2] Heap_2 Heap Memory Creation and Deletion</b>
</p>

### 2.3.3 Heap_3
1. Heap_3 uses the stand library 'malloc()' and 'free()'
2. <b>configTOTAL_HEAP_SIZE</b> constant, which can be modified in FreeRTOSConfig.h is not used.


### 2.3.4 Heap_4
1. Heap_4 subdivides an array into smaller blocks, and the array should be statically allocated and dimensioned by configTOTAL_HEAP_SIZE.
2. Heap_4 uses the <b><span style="color:Greenyellow"> first-fit algorithm</span></b> to allocate memory. For example, if pvPortMalloc() requests 20 bytes of RAM, and the available RAM blocks are 5 bytes, 200 bytes, and 100 bytes, FreeRTOS will choose the 200-byte RAM block. It then divides it into a 20-byte block and a 180-byte block, returning the pointer to the 20-byte block. The remaining 180-byte block will be used in the future.

Note that Heap_4 does combine adjacent free blocks into a single large block, which minimizes the risk of memory fragmentation.
{: .highlight }

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap_4.png" width="500"/>
    <br><b>[Pic.3] Heap_4 Heap Memory Creation and Deletion</b>
</p>

### 2.3.5 Heap_5
1. Heap_5 uses the same allocation algorithm as Heap_4.
2. <b><span style="color:Greenyellow"> Heap_5 links multiple separated physical memory spaces like linked list. But it does not combine the multiple memory seperated physically into a single large block </span></b> 
3. Heap_5 is useful when the RAM provided by the system on which FreeRTOS is running does not appear as a single contiguous block in the system's memory map.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap_5.png" width="500"/>
    <br><b>[Pic.5] Heap_5 for multiple separated memory space</b>
</p>

# 3. APIs for memory allocation
All APIs for memory allocation, which are supported by FreeRTOS, is elaborated at [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

# 4. Summary and Conclusion

|Heap Type|Summary|
|:--:|:---|
|Heap_1|- Primitive Memory Allocation Scheme <br> - Usefule to memory safe system <br> - Does not support <b>'vPortFree()'</b>|
|Heap_2|- Superseded by Heap_4 <br> - Uses <b>'Best-Fit algorithm'</b> <br> - Not Combine adjacent free blocks into a single large block|
|Heap_3|- Heap_3 uses the stand library 'malloc()' and 'free()'|
|Heap_4|- an array is subdivided into a smaller blocks <br> - Use <b>'First-Fit algorithm'</b> <br> - Combine adjacent free blocks into a single large block|
|Heap_5|- Suitable for the modern MUCs Memory Scheme that has multiple seperated RAM Blocks <br> - Use <b>'First-Fit algorithm'</b> <br> - Combine adjacent free blocks into a single large block|

We will go over the source code of each Heap and key technics used in FreeRTOS in detail. 

# Reference
- [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

[Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]:https://github.com/FreeRTOS/FreeRTOS-Kernel-Book/blob/main/booktitle.md