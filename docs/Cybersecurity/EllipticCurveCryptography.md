---
layout: default
title: 0_Elliptic Curve Cryptography (ECC)
parent: Topic_Cybersecurity
nav_order: 2
---

# Elliptic Curve Cryptography (ECC)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
Elliptic Curve Cryptography (ECC) is a type of the trapdoor function. In theoretical computer science and cryptography, the trapdoor function is a function that is easy to compute in one direction, yet difficult to compute in the opposite direction (finding its inverse) without special information, called the "Trapdoor". In Publick Key Infrastructure (PKI) using ECC, the "Trapdoor" shall be the 'public key'. For example, In <b>[Pic.1]</b>, the given <b>'t'</b> shall be the public key. Thus, the main purpose of the usage of ECC is to create the public key by feeding a private key which is generated randomly (e.g., 32 bytes positive integer) into ECC function.

<p align="center">
    <img src="../../../asset/images/Trapdoor.jpg" width="400"/>
    <br><b>[Pic.1] Trapdoor</b>
</p>

## Reference
Please refer to the listed reference below. That help you understand what is ECC in deatil.
- [ECC Overview]
- [ECC Mathmetical Explanation]
- [Trapdoor Wikipedia definition]
- [ECC Research paper]
- [Dot Operand Law]

## Deep Dive
1. <b>Elliptic Curve Cryptography</b>
    * The elliptic curve of ECC is based on the mathmetical function in the finite field, which contains a finite number of elements. Thus, the result of the operations of multiplication, addition, subtraction and division meet the rules of arithmetic known as the field axioms. The most common examples of finite fields are given by the integers mod p, which is a prime/binary number.
    * The trapdoor function of ECC can be defined as shown in <b>[Pic.2]</b>. This function is ranging from 0 to (prime - 1). It means that there are no neative integers.
    * <p align="center">
    <img src="../../../asset/images/TrapdoorEq.jpg" width="300"/>
    <br><b>[Pic.2] Trapdoor Function</b></p>
    * Elliptic curve parameters (a, b) defines the property of the curve. For example, the parameter of secp256k curve is a = 0, b = 7 and the property of this curve is shown in <b>[Pic.3]</b>.
    * <p align="center">
    <img src="../../../asset/images/EllipticalCurve.jpg" width="300"/>
    <br><b>[Pic.3] Elliptic Curve</b></p>
2. <b>Dot Operand</b>
    * The dot operand is a protocol to calculate a point on the elliptic curve. For example, we have two points (A, B) and draw a straight line that intersects ‘A’ and ‘B’. Then, another point ‘C’ that the intersecting line is made on the curve will be shown. Fianlly, we do x-axis symmetric transposition with the point ‘C’. As a result, we get the point ‘D’. This is 1 time dot operation.
    * <p align="center">
    <img src="../../../asset/images/DotOperand.jpg" width="300"/>
    <br><b>[Pic.4] Dot Operand</b></p>
    * 'n' times dot operation means that we do 1 time dot operation by 'n' times. With this principle, we genearte <b>'Private and Public'</b> key system.
    * Dot Operand has more complex law than the above simple explanation. More details can be found at section 2.1 Geometric addition in [Dot Operand Law] document. 
3. <b>ECC Domain Parameter</b>
    * <b>p:</b> modulo prime number.
    * <b>a:</b> coefficient of the elliptic curve.
    * <b>b:</b> coefficient of the elliptic curve.
    * <b>G:</b> base point on the curve, which is already known according to the type of curve.
    * <b>n:</b> order of point G, which makes G infinite. Ideally, the maximum size of a private key should be ’n’ satisfiying the below equation.
    * <p align="center">
    <img src="../../../asset/images/InfiniteEq.jpg" width="80"/>
    <br><b>[Pic.5] Ideal Condition</b></p>
    * <b>H:</b> Cofactor

## Example of ECC domain parameter (secp256k1)
```scss
p: FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFF FFFFFC2F
a: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000000
b: 00000000 00000000 00000000 00000000 00000000 00000000 00000000 00000007
G: 02 79BE667E F9DCBBAC 55A06295 CE870B07 029BFCDB 2DCE28D9 59F2815B 16F81798
n: FFFFFFFF FFFFFFFF FFFFFFFF FFFFFFFE BAAEDCE6 AF48A03B BFD25E8C D0364141
h: 01
```

{: .highlight }
Parameters we saw are nothing but positive integer. For better understanding, they are just utilized for a sepcific computation in ECC. What parameters should be used shall be defined in Certificate isseud by the trusted authrotiy. Thus, accoring to the security policy, that parameters shall be different.

---
[ECC Overview]:https://www.youtube.com/watch?v=dCvB-mhkT0w
[ECC Mathmetical Explanation]:https://andrea.corbellini.name/2015/05/17/elliptic-curve-cryptography-a-gentle-introduction/
[Trapdoor Wikipedia definition]:https://en.wikipedia.org/wiki/Trapdoor_function
[ECC Research paper]:https://www.secg.org/sec1-v2.pdf
[Dot Operand Law]:https://hal.science/hal-01914807/document