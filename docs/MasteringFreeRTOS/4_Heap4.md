---
layout: default
title: 4_Heap4
parent: Topic_FreeRTOS
nav_order: 10
---

# Heap4 Overview
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 1. Introduction
<b><span style="color:Greenyellow">'First-fit'</span></b> algorithm is used in Heap4 to improve memory leakage problem caused by Heap2. 'First-fit' algorithm provide combining the adjecent free blocks to one large block. Heap4 is similar to Heap2 except merging adjecent blocks and the position of `xEnd` block.  We will see how the algorithm works.

# 2. First-fit Algorithm


# Reference
- [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

[HeapMemoryManagement]: http://127.0.0.1:4000/docs/MasteringFreeRTOS/1_HeapMemoryManagement/
[Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]:https://github.com/FreeRTOS/FreeRTOS-Kernel-Book/blob/main/booktitle.md