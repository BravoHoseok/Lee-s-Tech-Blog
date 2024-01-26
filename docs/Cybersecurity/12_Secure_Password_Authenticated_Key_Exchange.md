---
layout: default
title: 12_Secure Password Authenticated Key Exchange (SPAKE2+)
parent: Topic_Cybersecurity
nav_order: 15
---

# Secure Password Authenticated Key Exchange (SPAKE2+)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
SPAKE2+ is the security mechanism based on a Password Authenticated Key Exchange (PAKE) protocol running between two parties for deriving a strong shared key with no risk of disclosing the password. SPAKE2+ is an augmented PAKE protocol, as only one party makes direct use of the password during the execution of the protocol. The other party only needs a verification value at the time of the protocol execution instead of the password. The verification value can be computed once, during an offline initialization phase. The party using the password directly would typically be a client, and acts as a prover, while the other party would be a server, and acts as verifier. The details of SPAKE+ protocol can be found in the two links below.

## Reference
Please refer to the listed reference below. That help you understand what is Base64.

## Terminology

## Deep Dive

[SPAKE2+ Vidoe Lecture]:https://www.youtube.com/watch?v=IAUhBRr8Rgc
[SPAKE2+ Paper]:https://www.ietf.org/archive/id/draft-bar-cfrg-spake2plus-01.html