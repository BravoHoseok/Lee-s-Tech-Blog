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
<b>RSA</b> Crypto Algorithm is invented by <b>R</b>ivest, <b>S</b>hamir, and <b>A</b>dleman, which is the abbreviation of their name. RSA is based on the principle that multiplying two large numbers is easy, *<b><U>but factorization of the large number is very difficult, especially the prime number</U></b>*. For example, it is easy to check if multiplying 187 and 323 is 92,701 or not, but finding the two prime factors of 92,701 will take a much longer time. RSA is *<U>public-key cryptography system</U>*, which is firstly introduced in the cybersecurity world. It means that RSA needs private and public key, and also it can be utilized in Digital Signature. For RSA Encryption/Decryption, it has several steps. 1) Key Generation, 2) Key Distribution, 3) Encryption, 4) Decryption. Let's take a look at these steps in detail.

## Reference
Please refer to the listed reference below. That help you understand what is RSA.
- [RSA Video-Easy2Understand]
- [Euler's Totient Function]
- [Understanding the RSA algorithm]
- [RSA Example]
- [RSA Vidoe Explanation]
 
## Terminology
* <b><span style="color:LightSkyBlue">Prime Factorization</span></b>
  * A way of expressing a number as a product of its prime factors.
* <b><span style="color:LightSkyBlue">Prime Number</span></b>
  * A integer number that has exactly two factors, 1 and the number itself.
* <b><span style="color:LightSkyBlue">Euler's Phi(totient) Function</span></b>
  * It counts the positive integers up to a given integer n that are relatively prime to n. That is. do not contain any factor in common with n, where 1 is counted as being relatively prime to all numbers.
  * Totative is the number which is less than or equal to <b>and</b> relatively prime to a given number n.
  * The totient function phi(n) can be simply defined as the number of totatives of n. For example, there are eight totatives of 24 (1, 5, 7, 11, 13, 17, 19, and 23), so phi(24)=8.
* <b><span style="color:LightSkyBlue">Euler's Theorem</span></b>


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
     
     * So, we calculate Phi (pxq) = Phi (p) x Phi (q) (p and q is the prime number). In this example, Phi (35) = Phi (5x7) = Phi (5) x Phi (7) = (5-1) x (6-1) = 24.
   * Pick encoding exponent 'e' which is compliant with gcd (e, phi (pxq)) = gcd (e, (p-1)x(q-1)) = 1.
     * gcd stands for 'Greatest Common Divisor'. If you mull over the equation above, the applicants of 'e' shall be a prime number except the prime numbers which are included in phi(pxq) factorization.
     * In this example, we calculate gcd (e, phi (5x7)) = 1. Then, e shall be 5, 7, 11, 13, 15, 17, 19.
     * Let's see the factorization of phi (5x7) = 24. It shall be 1, 2, 3, 4, 6, 7, 12, 24. The prime number of this result should be 1, 2, 3.
     * Thus, e shall be the prime numbers that are less than 24 except for the prime numbers which are included in 24 factorization.
     * In this example, we pick 'e' = 5.
   * <b>Euler's Theorem</b>
     * As shown in <b>[Pic.3]</b>, Euler's Theorem is that, if 'a' and 'n' are relatively prime, the result of moduls 'n' of the left term will be always 1. So, if you multiply phi (n) by 'k' constant, the result will alwasy be 1.
     * Based on this, if you mutiply the left term by 'a', the the result will be 'a'.
     * This is the breakthrough. <b><span style="color:Greenyellow"> We make the resulting power correspond to 'e' x 'd'. Since RSA is based on the assumption that it is almost impossible to find out the result of Phi (n) and the private key d, if the 'n' is much longer enough.</span></b>
     <p align="center">
     <img src="../../../asset/images/RSA_ET.JPG" width="200"/>
     <br><b>[Pic.3] Euler's Theorem </b></p>

   * <b><span style="color:Greenyellow"> [Pic.4] shows the proof of RSA Encryption and Decryption with 'e' and 'd'</span></b>
     <p align="center">
     <img src="../../../asset/images/RSA_Proof.JPG" width="400"/>
     <br><b>[Pic.4] RSA Encryption and Decryption Proof </b></p>

3. <b>Hands on RSA</b>
   * <b>[Pic.5]</b> shows an hands on example of RSA encryption and decryption.
   <p align="center">
   <img src="../../../asset/images/RSA_Exp.JPG" width="500"/>
   <br><b>[Pic.5] Hands on example </b></p>  

## What if?
1. What if am attackers steal the RSA public key with small N?
   *  In this case, you and your intended opposites will be exposed to Man-in-the-middle-attack, Data interception, Phishing attacks, and Identity theft. RSA encryption and decryption is based on an assumption that the intended recipient can decrypt with the publisehd RSA public key, which means that attackers can obtain your public key also.
   *  Additionaly, RSA is based on an important assumption that finding the prime factorization of N and calculating Phi (N) will take a huge amount of time in terms of computation. So the 'N' should be super longer enough.
   *  Although attackers fail to find out your private key, they can encrypt malicious message and send you with the purpose of attack.
   *  This is kind of attestation problem, and it should be solved in terms of out of band vericiation.

## Limitation
1. No efficient way has been found on modern computers to do prime factorization. Compared to ECC, it will take much longer time.
2. If the quantum machines become widely spread out, RSA will not be up and running anymore.

[RSA Video-Easy2Understand]:https://www.khanacademy.org/computing/computer-science/cryptography/modern-crypt/v/intro-to-rsa-encryption
[Euler's Totient Function]:https://mathworld.wolfram.com/TotientFunction.html
[Understanding the RSA algorithm]:https://arxiv.org/abs/2308.02785
[RSA Example]:https://brilliant.org/wiki/rsa-encryption/
[RSA Vidoe Explanation]:https://www.google.com/search?newwindow=1&sca_esv=601619779&q=rsa,+cryptography&spell=1&sa=X&ved=2ahUKEwjs4ezynvqDAxW4m68BHapMDEUQBSgAegQICBAC&biw=1920&bih=1073&dpr=1#fpstate=ive&ip=1&vld=cid:044236d2,vid:qph77bTKJTM,st:0