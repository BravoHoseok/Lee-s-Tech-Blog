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

# 1. Data Alignment
Before diving into Heap1, we first look at what is Alignment in terms of C and processor. As mentioned in [Alignment in C], great care should be given to working with memory, for it causes the most time consuming task in the processor. To handle memory, the modern processor normally use 'Memory Addressing' which determines the computation speed of it. Computers commonly address their memory in workd-sized chunks. A word is a computer's natural unit for data. Its size is defined by the computers architecture. Modern general purpose computers generally have a word-size of either 4 byte (32bit) or 8 byte (64 bit). In other word, the word-sized data gurantee the most fast operation speed such as fetch and add.

<b>[Pic.1]</b> shows 4 bytes Alignment in an memory region. Each cell represent 1 byte memory space and 4 bytes belong to each 4 byte address. With such visualization, I will explain why we need alignment. 

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_4BytesAlignment.png" width="500"/>
    <br><b>[Pic.1] 4 bytes Alignment</b>
</p>

Let's say we put 4 byte <b>int</b>, the green color, at 0x00000000 of the memory. It makes the 4 byte integer properly algined without doing any special work, because an int on this memory architecture exactly fits 4 bytes into the first slot as shown in <b>[Pic.2]</b>

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_4BytesAlignment_Int.png" width="500"/>
    <br><b>[Pic.2] Memory with an int</b>
</p>

If we decdied to put a char (Red color), a short (Blue Color), and an int (Green Color) into the memory region, it will make a problem as shown in [Pic.3].

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap1_AlignmentProblem.png" width="500"/>
    <br><b>[Pic.3] Memory with an char, an short, and an int</b>
</p>

Why this is problem? Let take into a situation where we get the 4 bytes int. In <b>[Pic3]</b>, we would like to fetch the int. Then we first access 0x00000000 and extract the last green byte. Next, we access 0x00000004 and extract the three green bytes. Fianlly, we assemble the fetched green blocks with bit-shifiting to make an complete int. Compared to [Pic.2], this case requires additional actions that comsume CPU clock cycle. This is the reason why we need 'Data Alignment' to speed up fetching the int in the memory.

# Reference
- [Alignment in C]

[Alignment in C]: chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://hps.vi4io.org/_media/teaching/wintersemester_2013_2014/epc-14-haase-svenhendrik-alignmentinc-paper.pdf