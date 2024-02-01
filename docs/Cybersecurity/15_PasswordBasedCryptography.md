---
layout: default
title: 15_Password-Based Key Derivation Function
parent: Topic_Cybersecurity
nav_order: 17
---

# Password-Based Key Derivation Function
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
PBKDF1 and PBKDF2 (Password-Based Key Derivation Function 1 and 2) are key derivation functions with a sliding computational cost, used to reduce vulnerability to brute-force attacks. 'Salt' and 'iteration count' formed the backbone of password-based encryption in PKCS #5 v1.5 Thus, password-based key derivation is a function of a password, a salt, and an iteration count, where the latter two quantities need not be kept secret. The whole process to derive a key from a password is similar to the one of SHA2.

## Key Point
1. Combination of a <b><span style="color:Greenyellow">password</span></b> with a <b><span style="color:Greenyellow">salt</span></b> to produce a key.
2. Include an <b><span style="color:Greenyellow">iteration count</span></b> in the key derivation technique.
3. Secure Hash Algorithm <b><span style="color:Greenyellow">(SHA)</span></b>

## Reference
Please refer to the listed reference below. That help you understand what is PBKDF#2.

## Terminology

## Deep Dive


[PBKDF Definition Wikipedia]:https://en.wikipedia.org/wiki/PBKDF2
[PBKDF Implemenation By IETF]:https://www.ietf.org/rfc/rfc2898.txt