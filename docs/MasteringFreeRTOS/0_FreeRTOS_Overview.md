---
layout: default
title: 0_FreeRTOS_Overview
parent: Topic_FreeRTOS
nav_order: 6
---

# FreeRTOS Overview
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Overview
In the fields of Automotive, Aviation, and Military, which are life-critical sectors, an Electrical Control Unit (ECU) must guarantee precise execution at the desired time. Otherwise, catastrophic accidents could occur. To ensure time-based execution, we typically adopt a Real-Time Operating System (RTOS). In modern technology, RTOS is frequently used, and understanding it is one of the key skills required to become a versatile embedded software developer.

With the aim of becoming skilled software programmers, we are starting to master FreeRTOS. As a first step, we will go through the document [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0], written by Richard Barry and the FreeRTOS team. This document explains modern OS theory in an easy-to-understand manner. Additionally, we will analyze the source code of FreeRTOS in detail. To reinforce our understanding of FreeRTOS, we will utilize a well-known lecture available on [Udemy FreeRTOS Lecture]. This lecture provides hands-on experiments with the 'STM32F407VGT60' discovery board. After extensively studying FreeRTOS, we will undertake a mini-project to port FreeRTOS onto the Infineon Tricore TC275 Evaluation Kit.

For the mini-project, we will base our work on the [TC1775 Demo Project] and [Legacy FreeRTOS Port on TC277]. The latter is a contribution from nine years ago, so if we identify areas for improvement, we will make the necessary updates. One of the goals of this project is to ensure versatility, meaning we will avoid relying on specific paid tools. Therefore, when implementing the port on the TC275, we will select open-source tools that are freely available.

Throughout this journey, we will cover topics such as FreeRTOS, STM32F407VGT60 and TC275 MCU Architecture, hands-on experiments, and development environments.

## Reference
- [Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]
- [Udemy FreeRTOS Lecture]
- [STM32F407VGT60 DataSheet]
- [STM32F407VGT60 User Manual and Product Specificatioin]
- [TC1775 Demo Project]
- [Legacy FreeRTOS Port on TC277]
- [TC275 lite kit]
- [FreeRTOS Port on TC399]
- [FreeRTOS Kernel v10.5.1]


## Sutdy with hands-on experiement
1. STM32F407VGT60 discovery board
2. STM32CubeIDE 1.16.0

## Mini Project Development Environment
1. TC275 lite kit
2. Compiler: GCC
3. FreeRTOS Kernel Version: the lastes one.
3. Development Environment: Windows
4. Reference Source Code: [TC1775 Demo Project] and [Legacy FreeRTOS Port on TC277]


[TC275 lite kit]:https://www.infineon.com/cms/en/product/promopages/AURIX-microcontroller-boards/low-cost-arduino-kits/AURIX-TC275-lite-kit/
[Mastering-the-FreeRTOS-Real-Time-Kernel.v1.0]:https://github.com/FreeRTOS/FreeRTOS-Kernel-Book/blob/main/booktitle.md
[TC1775 Demo Project]:https://www.freertos.org/FreeRTOS-for-Infineon-TriCore-TC1782-using-HighTec-GCC.html
[Legacy FreeRTOS Port on TC277]:https://interactive.freertos.org/hc/en-us/community/posts/210026366-FreeRTOS-7-1-Port-for-Aurix-TC27x-using-Free-Entry-Toolchain?_ga=2.60494381.1877225190.1712758092-1651550433.1712758092
[FreeRTOS Port on TC399]: https://forums.freertos.org/t/freertos-for-infineon-tc399xx/8399
[FreeRTOS Kernel v10.5.1]: https://forums.freertos.org/t/new-freertos-kernel-version-now-available/17825
[Udemy FreeRTOS Lecture]: https://www.udemy.com/course/mastering-rtos-hands-on-with-freertos-arduino-and-stm32fx/?couponCode=OF83024D#reviews
[STM32F407VGT60 DataSheet]: https://www.st.com/en/microcontrollers-microprocessors/stm32f407vg.html#documentation
[STM32F407VGT60 User Manual and Product Specificatioin]: https://www.st.com/en/evaluation-tools/stm32f4discovery.html#documentation