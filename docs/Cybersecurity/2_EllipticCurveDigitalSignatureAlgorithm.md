---
layout: default
title: 2_Elliptic Curve Digital Signature Algorithm (ECDSA)
parent: Topic_Cybersecurity
nav_order: 10
---

# Elliptic Curve Digital Signature Algorithm (ECDSA)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
* Elliptic Curve Digital Signature Algorithm (ECDSA) is a Digital Signature Algorithm (DSA) which uses keys derived from Elliptic Curve Cryptography (ECC). It is a particularly efficient equation based on Public Key Cryptography (PKC), providing robust, efficient encryption.

## Reference
Please refer to the listed reference below. That help you understand what is ECDSA in deatil.
- [ECDSA High Level Definition]
- [ECDSA Overview]
- [ECDSA Paper]

## Deep Dive
1. <b> Elliptic Curve Digital Signature Algorithm (ECDSA) </b>
   * The ESDSA is a cryptographically secure digital signature scheme, based on the elliptic-curve cryptography (ECC).
   * The ECDSA sign / verify algorithm relies on Elliptic Curve point multiplication.
   * ECDSA keys and signatures are shorter than in RSA for the same security level, which means that ECDSA is more efficient than RSA in terms of computation and resource. (256-bit ECDSA signature has the same security strength like 3072-bit RSA signature)
   * ECDSA is also used for Transport Layer Security (TLS), the successor to Secure Sockets Layer (SSL), by encrypting connections between web browsers and a web application. The encrypted connection of an HTTPS website, illustrated by an image of a physical padlock shown in the browser, is made through signed certificates using ECDSA.

2. <b> Digital Signature </b>
   * As simplified explanation, Digital Signature based on ECDSA is a set of <b>_'r'_</b> and <b>_'s'_</b>, which are 32 byte positive integer respectively. The <b>_'r'_</b> and <b>_'s'_</b> are computed by Elliptical Curve and Private and Public Key. Then, they are appended at the end of certificate or message. We call them Digtal Signautre and the object is signed.
   * A receiver will get these two values and verify the received <b>_'s'_</b> value with the received <b>_'r'_</b>, crypto information menteiond in the certificate and ECC.
   * In other word, we reconstruct <b>_'s1'_</b> based on the received <b>_'r'_</b> and the information in the certificate. Then compare <b>_'s1'_</b> to the received <b>_'s'_</b> and check if they are same. If not, the received data is fabricated.

3. <b> Mathematical Explanation (r, v) and Digital Signature Verification </b>
   * For better understanding of Digital Signature based on ECDSA, let's take a look at the math behind the ECDSA.
    <p align="center">
    <img src="../../../asset/images/Math1.JPG" width="800"/>
    <br><b>[Pic.1] Computation process of Digital Signature</b></p> 
   * The computed 'r' and 's' values are appended at the end of certificate as shown in <b>[Pic.2]</b>
    <p align="center">
    <img src="../../../asset/images/Math2.JPG" width="200"/>
    <br><b>[Pic.2] Certificate with Digital Signature</b></p> 
   * Verification Process of Digital Signature is shown in <b>[Pic.3]</b>
    <p align="center">
    <img src="../../../asset/images/Math3.JPG" width="800"/>
    <br><b>[Pic.3] Verification Process of Digital Signature</b></p> 
   * In this mathmetical process, SHA is a type of Hash Function that returns a unique value.

[ECDSA High Level Definition]:https://www.hypr.com/security-encyclopedia/elliptic-curve-digital-signature-algorithm
[ECDSA Overview]:https://cryptobook.nakov.com/digital-signatures/ecdsa-sign-verify-messages
[ECDSA Paper]:https://csrc.nist.gov/csrc/media/events/workshop-on-elliptic-curve-cryptography-standards/documents/papers/session6-adalier-mehmet.pdf