---
layout: default
title: 2_Heap1
parent: Topic_FreeRTOS
nav_order: 8
---

# Heap1 Overview
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 1. Alignment
Before diving into Heap1, we first look at what alignment is in terms of C and the processor. As mentioned in [Alignment in C], great care should be taken when working with memory, as it can be one of the most time-consuming tasks for the processor. To handle memory, modern processors typically use 'Memory Addressing,' which influences their computation speed. Computers commonly address their memory in word-sized chunks. A word is the computer's natural unit for data, and its size is defined by the computer's architecture. Modern general-purpose computers generally have a word size of either 4 bytes (32-bit) or 8 bytes (64-bit). In other words, word-sized data guarantees the fastest operation speeds for tasks such as fetching and adding.

<b>[Pic.1]</b> shows 4-byte alignment in a memory region. Each cell represents 1 byte of memory space, and 4 bytes belong to each 4-byte address. With this visualization, I will explain why alignment is necessary.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_4BytesAlignment.png" width="500"/>
    <br><b>[Pic.1] 4 bytes Alignment</b>
</p>

Let's say, as shown in <b>[Pic.2]</b>, we place a 4-byte <b>int</b> (in green) at memory address 0x00000000. This 4-byte integer is properly aligned without requiring any special adjustments because, on this memory architecture, an int fits exactly into the 4-byte memory slot.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_4BytesAlignment_Int.png" width="500"/>
    <br><b>[Pic.2] Memory with an int</b>
</p>

If we decide to place a char (in red), a short (in blue), and an int (in green) into the memory region as shown in <b>[Pic.3]</b>, it will cause a problem.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AlignmentProblem.png" width="500"/>
    <br><b>[Pic.3] Memory with an char, an short, and an int</b>
</p>

Why is this a problem? Let's take a situation where we need to fetch the 4-byte int. In <b>[Pic.3]</b>, we want to retrieve the int. First, we access 0x00000000 and extract the last green byte. Next, we access 0x00000004 and extract the remaining three green bytes. Finally, we assemble the fetched green blocks using bit-shifting to form a complete int. Compared to <b>[Pic.2]</b>, this case requires additional actions that consume CPU clock cycles. This is why we need 'Data Alignment' to speed up fetching the int from memory.

To solve this problem, we insert a padding byte (in gray) between the char and the short to ensure the int is aligned. This way, we can simply access 0x00000004 to fetch the int in one step. <b>[Pic.4]</b> illustrates the aligned int.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AlignmentPadding.png" width="500"/>
    <br><b>[Pic.4] Memory with an char, an short, and an aligned int</b>
</p>

However, we still encounter a problem when fetching the char and short, as additional actions like bit-shifting and & operations are required to extract them. To address this, we insert more padding bytes, as shown in <b>[Pic.5]</b>. Imagine a situation where we fetch all three data items and sum them. We only need to access 0x00000000, 0x00000004, and 0x00000008 to fetch and sum them. The char and short will be considered as 4 bytes each, but they still retain their original values with their respective sizes (1 byte and 2 bytes).

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AlignmentPadding2.png" width="500"/>
    <br><b>[Pic.5] Memory with an char, an short, and an int, which are all aligned</b>
</p>

# 2. Structure Alignment
Due to alignment, the size of a data structure might be different from what you expect. The code snippet below is a simple real-world example of structs. A programming beginner might assume that the size of the structure below is 7 bytes.

```c
struct MyStruct
{
    char a;  // 1 byte
    short b; // 2 byte
    int c;   // 4 byte
};
```

As you have learned about alignment, the size of the struct will be 24 bytes. The elements of the structure will be aligned and padded based on the largest element in it.

```c
struct MyStruct
{
    char a;  // 4 byte
    short b; // 2 byte
    int c;   // 4 byte
};
```

Padding bytes indeed consume more memory space, but this is a trade-off between speed and space. If the data is not aligned, the consequences of data structure misalignment can vary widely between architectures. For example, some RISC, ARM, and MIPS processors will respond with an alignment fault if an attempt is made to access a misaligned address. Specialized processors, such as DSPs, usually do not support accessing misaligned locations. Most modern general-purpose processors can access misaligned addresses, albeit with a significant performance penalty, often at least twice the time required for aligned access.

# 3. Anlaysis of Heap1 Source Code
As we reviewed Heap1 in the previous chapter [HeapMemoryManagement], Heap1 uses an already allocated array, <b>ucHeap[configTOTAL_HEAP_SIZE]</b>. The size of the array can be adjusted in FreeRTOSConfig.h. In the given code, the original size of ucHeap is 4096 bytes. It is not difficult to understand Heap1 and its source code because it only has pvPortMalloc and does not provide pvPortFree. Whenever the kernel receives a request for a specific size of heap, it takes the size from the array and returns the start address of the allocated space in the array. However, we would like to analyze two techniques for alignment. <b>[Pic.6]</b> shows the code snippet of Heap1.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_SourceCode.png" width="1024"/>
    <br><b>[Pic.6] Heap1 Code Snippet</b>
</p>

## 3.1 Alignment for size
① in <b>[Pic.6]</b> shows the alignment for the requested heap size, and the code snippet below shows the result after preprocessing. In the given code, it assumes that FreeRTOS uses 8-byte alignment (for a 64-bit processor). Therefore, the requested heap size, ```xWantedSize```, needs to be aligned to 8 bytes. As we discussed about alignment and padding, if ```xWantedSize``` is not a multiple of 8, several padding bytes are added to achieve 8-byte alignment.

```c
if( ( xWantedSize + ( 8 - ( xWantedSize & 0x0007 ) ) ) > xWantedSize )
{
    xWantedSize += ( 8 - ( xWantedSize & 0x0007 ) );
}
```

1. ``` ( xWantedSize & 0x0007 ) ``` represents the remaing of xWantedSize divided by 8.
2. ``` ( 8 - ( xWantedSize & 0x0007 ) ) ``` means that how many bytes are necessary to fit in 8 bytes.
3. For example, if ``` xWantedSize ``` is 7, then the remiaing of ``` xWantedSize```  divided by 8 is 7. And the necessary bytes to fit in 8 bytes shall be 1 byte.
4. So, ``` ( xWantedSize + ( 8 - ( xWantedSize & 0x0007 ) ) ) ``` shows the size of (xWantedSize + additional padding bytes) for 8 bytes alignment. If necessary to add padding bytes, we do that to xWantedSize. If not, we do not add padding bytes.

## 3.2 Alignment for address
For complete alignment to maximize a processor's performance (e.g., operation speed), it is also necessary to align addresses. ② in <b>[Pic.6]</b> shows the alignment for addresses. The code snippet below shows the result after preprocessing. In the given code, it also assumes that FreeRTOS uses 8-byte alignment (for a 64-bit processor). Let's take a look at the code snippet below step by step.

1. ``` ( ( uint32_t ) & ucHeap[ 8 - 1 ] ) ``` is to identify the adress of ucHeap[7].
2. ``` ( ( ( uint32_t ) & ucHeap[ 8 - 1 ] ) & ( ~( ( uint32_t ) 0x0007 ) ) ) ``` represent where is the very first element starting in the ucHeap array, which is compliant with the address alignment.
3. Let's say the address of ``` ucHeap[7] ``` is 0x00000007, then the first element shall be ucHeap[0] as shown in <b>[Pic.7]</b>
4. Let's say the address of ``` ucHeap[7] ``` is 0x0000000a, then the first element shall be ucHeap[5], which is its address is multiply by 8, as shown in <b>[Pic.8]</b>
5. Let's say the address of ``` ucHeap[7] ``` is 0x00000008, then the first element shall be ucHeap[7], which is its address is multiply by 8, as shown in <b>[Pic.9]</b>

``` c
if( pucAlignedHeap == NULL )
{
    /* Ensure the heap starts on a correctly aligned boundary. */
    pucAlignedHeap = ( uint8_t * ) ( ( ( uint32_t ) & ucHeap[ 8 - 1 ] ) & ( ~( ( uint32_t ) 0x0007 ) ) );
}
```

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AddressAlignmentCase1.png" width="500"/>
    <br><b>[Pic.7] Heap1 Address Alignment Case1</b>
</p>

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AddressAlignmentCase2.png" width="500"/>
    <br><b>[Pic.8] Heap1 Address Alignment Case2</b>
</p>

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AddressAlignmentCase3.png" width="500"/>
    <br><b>[Pic.9] Heap1 Address Alignment Case3</b>
</p>

Can you guess what the meaning of ``` value & ~0x00000007 ``` is? It represents the quotient of the value, which is devided by 8, then multiply the quotient by 8. That is ``` quotient = (value / 8), then quotient * 8 ```

The interesting thing is that if the address of ```ucHeap[7]``` is aligned based on 8 bytes (e.g., 8 (decimal), 16 (decimal), 32 (decimal)), the heap wastes 8 bytes, as shown in <b>[Pic.9]</b>. The table below shows the wasted bytes according to the address. If we align based on the address of ```ucHeap[0]```, we can reduce the wasted bytes by 1 byte compared to aligning based on the address of ```ucHeap[7]```, as shown in the table below.
{: .highlight }

|&ucHeap[7]|Wasted Bytes|&ucHeap[0]|Wasted Bytes|
|:--:|:--:||:--:|:--:|
|1|1|1|7|
|2|2|2|6|
|3|3|3|5|
|4|4|4|4|
|5|5|5|3|
|6|6|6|2|
|7|0|7|1|
|8|8|8|0|
|Total|29|Total|28|


# Reference
- [Alignment in C]

[Alignment in C]: chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://hps.vi4io.org/_media/teaching/wintersemester_2013_2014/epc-14-haase-svenhendrik-alignmentinc-paper.pdf
[HeapMemoryManagement]: http://127.0.0.1:4000/docs/MasteringFreeRTOS/1_HeapMemoryManagement/