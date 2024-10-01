---
layout: default
title: 3_Heap2
parent: Topic_FreeRTOS
nav_order: 9
---

# Heap2 Overview
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

# 1. Introduction
As mentioned in [HeapMemoryManagement], Heap2 is superseded by Heap4. The main reasons I think is that <b><span style="color:Greenyellow">'Best-fit'</span></b> algorithm that Heap2 has adopted is vulnerable to memory leakage problem, since the algirhtm does not combine the adjecent free memory blocks into one large free block. We will see how the algorithm work.

# 2. Best-fit Algorithm
The best-fit algorithm finds a free block that is closest to the requested block size. To do this, when executing `vPortFree` to release an occupied block, it arranges the free blocks in a linked list in ascending size order. This way, the algorithm does not need to check the size of each free block in every search loop to see if it matches the requested block size. For example, the free blocks might be aligned like 5 bytes -> 25 bytes -> 100 bytes. If the requested block size is 20 bytes, the algorithm will search from the 5 bytes block and stop at the 25 bytes block, as the 25-byte block is sufficient to allocate the requested 20 bytes. However, one disadvantage of Heap2 is that it does not combine the 25 bytes and 100 bytes blocks, even if the two free blocks are adjacent.

# 3. Analysis of Heap2 Source Code
When allocating a block with the requested size, an additional block, Block Header, is appended to the haed of a block that will be allocated as shown in <b>[Pic.1]</b>. Although you requested 20 bytes, 28 bytes are actually necessary. With the block header information, 'Best-fit' algorithm align the free blocks.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_AllocatedBlockFormat.png" width="150"/>
    <br><b>[Pic.1] Allocated Block Format</b>
</p>

## 3.1 Alignment
As we study what is alignment, same principle is applied to Heap2. However, we add the block header size (`xHeapStructSize`) to `xWantedSize`, then align the `xWantedSize`. as shonw in <b>[Pic.2] </b>

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Code1.png" width="750"/>
    <br><b>[Pic.2] pvPortMalloc Code Snippet 1</b>
</p>

## 3.2 prvHeapInit
The next step is initialization process to make a linked list. <b>[Pic.3]</b> shows prvHeapInit source code. 

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Code2.png" width="750"/>
    <br><b>[Pic.3] prvHeapInit Code Snippet</b>
</p>


When we expand 371 line, that will be shown like below. That is, `pucAlignedHeap` points the `ucHeap[0]` address, if the address of `ucHeap[7]` ends with 7, which represent that it is aligned by 8 correctly.

```c
pucAlignedHeap = ( uint8_t * ) ( ( ( portPOINTER_SIZE_TYPE ) & ucHeap[ portBYTE_ALIGNMENT - 1 ] ) & ( ~( ( portPOINTER_SIZE_TYPE ) portBYTE_ALIGNMENT_MASK ) ) );

pucAlignedHeap = ( uint8_t * ) ( ( ( uint32_t ) & ucHeap[ 7 - 1 ] ) & ( ~( ( uint32_t ) 0x0007 ) ) );
```

To make the linked list of free blocks, we need two additional blocks, `xStart` and `xEnd`. They represent the start and end of the linked list. Lines 375 to 380 show the process of creating these two blocks. Lastly, we place block header information (free block size and the address of the next free block) into a single free block. The initialized linked list is shown in <b>[Pic.4]</b>. Let's say the addresses of `xStart` and `xEnd` are `0x42cd58` and `0x42cd50`, respectively. After the initialization step, `0x42cd50` will be filled from ucHeap[0] to ucHeap[3], and the free block size 4096 - 8 will be filled from ucHeap[4] to ucHeap[5]. Depending on the endianness, the filled data may be reversed or not. The reason for `-8` is for the worst-case scenario in alignment.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Init.png" width="750"/>
    <br><b>[Pic.4] Initialized linked list</b>
</p>

## 3.3 Allocation 
The last step of `pvPortMalloc` is memory allication of the requested size. <b>[Pic.5]</b> shows that process. Let's say a user requested 8 bytes memory allocation, `pvPortMalloc(8)`. Then `xWantedSize` shall be 16 bytes, because we need to add the block header size.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Code3.png" width="750"/>
    <br><b>[Pic.5] Initialized linked list</b>
</p>

1. The while statement in line 226 is used to find a suitable free block that is large enough to allocate the requested memory size (in this case, 8 bytes). From the initialized linked-list shown in <b>[Pic.4]</b>, `pxPreviousBlock` points to the start address of the `xStart` block, and `pxBlock` points to the address of `ucHeap[0]`.

2. At line 237, `pvReturn` stores the return address (start address) of the allocated memory for 8 bytes (requested size). Since an allocated block includes an 8-byte block header, as shown in <b>[Pic.1]</b>, the return address is incremented by 8 bytes from `pxBlock`. Therefore, `pvReturn` points to the address of `ucHeap[8]`.

3. At line 241, `1` connection is disconnected, and `2` connection are created, as shown in <b>[Pic.6]</b> steps 1 and 2. This is a prerequisite step to update the linked list of free blocks after memory allocation.

4. The line 244 'if' statement is used to split the free block into two blocks if that free block is larger than the requested size. In this case, since we requested 8 bytes, the program will enter the if statement. `pxNewBlockLink` will point to the address of `ucHeap[16]`, because `pxBlock` points to the address of `ucHeap[0]` and `xWantedSize` is 16. From `ucHeap[16]`, 8 bytes are used to store the remaining free block size and a pointer to the next block as header information (In this case, xEnd).

    <p align="center">
        <img src="../../../asset/images/FreeRTOS/Heap2_BlockDependency.png" width="750"/>
        <br><b>[Pic.6] Block Dependency</b>
    </p>

5. At line 259, as shown in <b>[Pic.5]</b>, `prvInsertBlockIntoFreeList` macro inserts `pxNewBlockLink` to the linked list of free blocks. <b>[Pic.7]</b> shows prvInsertBlockIntoFreeList macro implementation.

    <p align="center">
        <img src="../../../asset/images/FreeRTOS/Heap2_Code4.png" width="750"/>
        <br><b>[Pic.7] prvInsertBlockIntoFreeList Code Snippet</b>
    </p>

6. In <b>[Pic.7]</b>, `pxBlockToInsert` is `pxNewBlockLink`, pointing to the address of `ucHeap[16]` in this case. The 'for' statement is used to make `pxIterator` point to the correct position (which should be `xStart`). Then, lines 151 and 152 in <b>[Pic.7]</b> create connections `3` and `4` in <b>[Pic.6]</b> step 3. If you look at <b>[Pic.6]</b>, you can understand how the connections between free blocks are created step by step. <b><span style="color:Greenyellow">The 'for' statement is the key to create the linked list of free blocks in ascending size order.</span></b> We will see the details in a later section.
 
 The result after `pvPortMalloc(8)` is shown in <b>[Pic.8]</b> in detail. The blue-colored region in the ucHeap array is allocated. The interesting point of this region is that the MSB of `ucHeap[7]` is masked with 1 to represent that this block is allocated. That is the reason why the value of `ucHeap[7]` is `0x80`.

 The remaining free block is connected with `xStart` and `xEnd` again to make the linked-list of free blocks.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Allocation.png" width="750"/>
    <br><b>[Pic.8] 8-byte memory allocation</b>
</p>

## 3.4 Releasing an allocated memory region.
`vPortFree` is responsible to release an allocated memory region by `pvPortMalloc`. <b>[Pic.9]</b> shows the vPortFree Code Snippet.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Code5.png" width="750"/>
    <br><b>[Pic.9] vPortFree Code Snippet</b>
</p>

1. Since an allocated memory region includes 8-byte header information, the address which is given to release is deducted by 8 bytes. If we execute `pvPrtFree(&ucHeap[8])`, which is `pvReturn` value in the above case, `puc` should be the address of `ucHeap[0]` as shown in line 298.

2. Lines 307 and 309 are used to check if the requested block to release is an properly allocated block. It checks if the MSB of the size byte is set to 1 and `pvNextFreeBlock` is NULL in an allocated block.

3. If the requated block to release is correct one, then insert the block to the linked-list of free blocks by executing `prvInsertBlockIntoFreeList(&ucHeap[0])` in this case. 

4. <b>[Pic.10]</b> shows the resulting linked-list of free blocks after `prvInsertBlockIntoFreeList(&ucHeap[0])` from step 3 in <b>[Pic.6]</b>

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Release.png" width="750"/>
    <br><b>[Pic.10] Linked-List of free blocks</b>
</p>

## 3.5 Ascending Order Linked-List of free blocks.
Let's look at how 'Best-fit' algoritmh make the linked-list of free blocks ascending order. As I mentioned eariler, the key point is 'for' statement in the code snippet below.

```c
#define prvInsertBlockIntoFreeList (pvBlockToInsert)
{
    BlockLink_t * pxIterator;
    size_t xBlockSize;

    xBlockSize = pxBlockToInsert->xBlockSize;

    /* Iterate through the list until a block is found that has a larger size */
    /* than the block we are inserting. */
    for(pxIterator = &xStart; pxIterator->pxNextFreeBlock->xBlockSize < xBlockSize; pxIterator = pxIterator->pxNextFreeBlock)
    {
        /* There is nothing to do here - just iterate to the correct position. */
    }

    /* Update the list to include the block being inserted in the correct */ 
    /* position. */
    pxBlockToInsert->pxNextFreeBlock = pxIterator->pxNextFreeBlock;
    pxIterator->pxNextFreeBlock = pxBlockToInsert;
}
```

1. Let's say we allocated 10 bytes, 100 bytes, 200 bytes. Then, the resulting linked-list will be shonw in <b>[Pic.11]</b>. In this allocation step, `pxInterator` in the code snippet above always points the address of `xStart`block.

2. when executing `pvPortMalloc(10)`, `3` and `4` connections are created and `1` and `2` connections are removed.

3. when executing `pvPortMalloc(100)`, `5` and `6` connections are created and `3` and `4` connections are removed.

4. when executing `pvPortMalloc(200)`, `7` and `8` connections are created and `5` and `6` connections are removed.

    <p align="center">
        <img src="../../../asset/images/FreeRTOS/Heap2_Allocation1.png" width="750"/>
        <br><b>[Pic.11] Linked-List after 10, 100, 200 bytes memory allocation</b>
    </p>    

5. Then, let's say we release 108-byte, 208-byte, and 18-byte allocated blocks in this order and see `pxIterator` value. <b>[Pic.12]</b> shows the progress of the linked-list of free blocks whenever releasing the allocated block.

6. When releasing 108-byte allocated block, `pxIterator` points the address of `xStart`, then `3`, `4`, and `5` connections are created and `1` and `2` connections are removed.

7. When releasing 208-byte allocated block, `pxIterator` points the address of `108-byte free block`, then `6`, `7`, `8`, and `9` connections are created and `3`, `4`, and `5` connections are removed.

8. When releasing 18-byte allocated block, `pxIterator` points the address of `xStart`, then `10`, `11`, `12`, `13`, `14` connections are created and `6`, `7`, `8`, and `9` connections are removed.

9. Did you get the idea of 'for' statement in the code snippet above? In that statement, comparision is conducted based on the requeset size to release and the block size of free blocks, then set `pxIterator` to a proper position.

    <p align="center">
        <img src="../../../asset/images/FreeRTOS/Heap2_Release1.png" width="750"/>
        <br><b>[Pic.12] Linked-List after 108, 208, 18 bytes memory release</b>
    </p>

10. Let's foward one step more. In the linked-list of free blocks. What will happen, if `pvPortMalloc(190)` is executed? First, find a proper free block which is enough to allocate 198 bytes. We get 208-byte free block. Second, 208-byte free block is splited into two blocks, 198-byte block (including 8-byte header size) and 10-byte block. Lastly, 10-byte block is inserted into the linked list of free blocks in ascending size order. The progress if this sequence is shown in the <b><[Pic.13]</b>. `1`, `2`, and `3` connections are created newly.

<p align="center">
    <img src="../../../asset/images/FreeRTOS/Heap2_Allocation2.png" width="750"/>
    <br><b>[Pic.13] Linked-List after pvPortMalloc(190)</b>
</p>

As you can see,'Best-fit' algorithm aligns the linked-list of free blocks in ascending size order.
{: .highlight }

# Reference
- [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]

[HeapMemoryManagement]: http://127.0.0.1:4000/docs/MasteringFreeRTOS/1_HeapMemoryManagement/
[Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]:https://github.com/FreeRTOS/FreeRTOS-Kernel-Book/blob/main/booktitle.md