---
layout: default
title: 8_Rivest–Shamir–Adleman (RSA)
parent: Topic_Cybersecurity
nav_order: 11
---

# Rivest–Shamir–Adleman (RSA)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
<b>RSA</b> Crypto Algorithm is invented by <b>R</b>ivest, <b>S</b>hamir, and <b>A</b>dleman. RSA is the abbreviation of their name. RSA is based on the principle that multiplying two large numbers is easy, *<U>but factorization of the large <b>prime number</b> is very difficult.</U>* For example, it is easy to check that 187 and 323 multiply to 92,701, but trying to find the two prime factors of 92,701 will take a much longer process. RSA is *<U>public-key cryptography system</U>*, which I mentioned in [Chapter 1]. It means that RSA needs private and public key, and also it can be used in Digital Signature. For RSA Encryption/Decryption, it has several steps. 1) Key Generation, 2) Key Distribution, 3) Encryption, 4) Decryption. Let's take a look at these steps in detail.

## Reference
Please refer to the listed reference below. That help you understand what is RSA.


## Terminology
* <b>Prime Factorization</b>
  * A way of expressing a number as a product of its prime factors.
* <b>Prime Number</b>
  * A integer number that has exactly two factors, 1 and the number itself.
* <b>Euler's Phi(totient) Function</b>
  * It counts the positive integers up to a given integer n that are relatively prime to n. That is. do not contain any factor in common with n, where 1 is counted as being relatively prime to all numbers.
  * Totative is the number which is less than or equal to <b>and</b> relatively prime to a given number n.
  * The totient function phi(n) can be simply defined as the number of totatives of n. For example, there are eight totatives of 24 (1, 5, 7, 11, 13, 17, 19, and 23), so phi(24)=8.


## Deep Dive
1. <b>Time Complexity</b>
   * RSA is based on <b><span style="color:Greenyellow">Prime Number Factorization</span></b>. Euclidean shows that every number has excatly 1 prime factorization.
   * For example, one of the prime factorization of 92,701 shall be 187x323. Each prime number can be converted into another prime factorization like (11x17)x(17x19). For the number of combination of the prime factorization of 92,701 should be 4.
   * Assume that if the prime number is very large, figuring out all of the prime factors would be tedious work. That is, the bigger the prime number is, the longer the time when attackers find out a exact one prime factorization is. In <b>[Pic.1]</b>, it takes 198 years. RSA is exactly utilizing this property in order to prevent attackers from hijacking data.
   <p align="center">
   <img src="../../../asset/images/RSA_TimeComplexity.JPG" width="300"/>
   <br><b>[Pic.1] Time Complexity of finding the prime factorization of large prime number </b></p>

2. <b>Key Generation</b>
   * <b>RSA modulus N</b> 
     * RSA Modulus 'N' is the key of this algorithm. It is a positive integer which equals the product of two distinct prime numbers 'p' and 'q'.
     * N = pxq (e.g., N= 5x7 = 35)
     * When you choose the two prime number, pick them which are greater than 1024-bit for the reason of security.
     * N will be the part of private and public key. Normally, when it comes to the size of RSK key, N is referred.
     * The picked two prime number should not be revealed to public.
   * <b>Euler's Phi(totient) Function</b>
     * As we saw the Euler's Phi(totient) Function as terminology, Finding out result of Phi functio is very difficult.
     * However, when it comes to the prime number, The phi function shows an interesting pattern as shonw in <b>[Pic.2]</b>, which is <b><span style="color:Greenyellow"> Phi (prime number) = prinmumber -1 always</span></b>. For exampe, Phi(5) = 4 (1, 2, 3, 4).
     <p align="center">
     <img src="../../../asset/images/RSA_Phi.JPG" width="300"/>
     <br><b>[Pic.2] Phi function property for the prime number </b></p> 
     
     * So, we calculate Phi (pxq) = Phi (p-1) x Phi (q-1) (p and q is the prime number). In this example, Phi (35) = Phi (5x7) = 4x6 = 24.
   * Pick encoding exponent 'e' which is compliant with gcd (e, phi (pxq))= = gcd (e, (p-1)x(q-1)) = 1.
     * gcd stands for 'Greatest Common Divisor'. If you mull over the equation above, the applicants of e shall be a prime number except the prime numbers which are included in phi(pxq) factorization.
     * In this example, we calculate gcd (e, phi (5x7)) = 1. Then, e shall be 5, 7, 11, 13, 15, 17, 19.
     * Let's see the factorization of phi (5x7) = 24. It shall be 1, 2, 3, 4, 6, 7, 12, 24. The prime number of this result should be 1, 2, 3.
     * Thus, e shall be the prime numbers that are less than 24 except for the prime numbers which are included in 24 factorization.
     * For this example, pick e = 19.

[Chapter 1]:https://bravohoseok.github.io/Lee-s-Tech-Blog/docs/Cybersecurity/1_PrivateAndPublicKey/
[RSA Video-Easy2Understand]:https://www.khanacademy.org/computing/computer-science/cryptography/modern-crypt/v/intro-to-rsa-encryption
[Euler's Totient Function]:https://mathworld.wolfram.com/TotientFunction.html