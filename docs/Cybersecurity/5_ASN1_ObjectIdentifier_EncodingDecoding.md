---
layout: default
title: 5_ASN.1 Object Identifier Encoding and Decoding
parent: Topic_Cybersecurity
nav_order: 8
---

# ASN.1 Object Identifier Encoding and Decoding
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
Object Identifier (OID) used in ASN.1 system shall be encoded and decoded based on a specific rule, <b><span style="color:Greenyellow"> which similar to Base64 Encoding and Decoding</span></b>. This rule shall be a subset of Basic Encoding Rule (BER) or Distinguished Encoding Rule (DER). The specific encoding and decoding rule shall only correspond to OID. I reffered the idea describing how to encode and decode OID at [OID Encoding and Decoding Rule]. In this chapter, I will elaborate the rule based on <b>[Pic.1].</b>

<p align="center">
    <img src="../../../asset/images/OID.JPG" width="500"/>
    <br><b>[Pic.1] OID Encoding and Decoding Rule</b>
</p>

## Reference
Please refer to the listed reference below. That help you understand what is ASN.1.
- [OID Encoding and Decoding Rule]
- [Layman's Guide]

## Deep Dive
1. <b>Example OID: 1.2.840.113549.1.7.1</b>
2. <b>Encoding Rule</b>
   * <b>1st Step (the first two node)</b>
      * Slash decimal values based on dot (.). Then the values shall be splited to <b>7 nodes (1 2 840 113549 1 7 1)</b>.
      * Grab the first two decimal nodes and do the following operation.
        * 40(dec) x (first node) + (second node).
        *  In this example, the result shall be <b>40x1+2 = 42 (0x2A).</b>
   * <b>2nd Step (Encode multiple bytes of a dicimal node - 2 byte)</b>
      * Based on <b>OCTET</b>, encode multiple bytes of a decimal node.
      * bit7 of each OCTET should be set to '0' or '1' in order to get to know whether or not the following bytes exist.
      * Shifting the right nibble of the left most byte by 1.
      * In this case, we encode 840 (dec) = 0x0348 = b'0000 0011 0100 1000, and summarized the detailed sequence in <b>[Pic.2]</b>
        <p align="center">
        <img src="../../../asset/images/Encoding1.JPG" width="650"/>
        <br><b>[Pic.2] Encoding 2 byte</b></p>
      * The result shall be <b>0x86, 0x48</b>

        Note that, since we do 1-bit leftshift in the yellow range, we lost the data in bit6 of Byte1. It means that we must not put data in the bit6 position. So, the actual numerical range of Byte1 shall be from b'0000 0000 (0x00 = 0 dec) to b'0011 1111 (0x3F = 63 dec). The actual decimal range that can be put in the 2 byte shall be b'0000 0000 0000 0000 (0x00 = 0 dec) to b'0011 1111 1111 1111 (0x3FFF = 16384 dec).
        {: .highlight }

    * <b>3nd Step (Encode multiple bytes of a dicimal node - 3 byte)</b>
      * Encoding 3 byte follows the same principle of 2nd step, but we do 1-bit leftshift 2 times as shown in <b>[Pic.3]</b>.
      * The range where we do 1-bit leftshift will be narrowed down from the largest one to the smallest one.
        <p align="center">
        <img src="../../../asset/images/Encoding2.JPG" width="750"/>
        <br><b>[Pic.3] Encoding 3 byte</b></p>
      * The result shall be <b>0x86, 0xF7, 0x0D</b>

        Note that, like the highlight above, the digit information must not exist in bit6 and bit5 of Byte2 of a decimal. It means that the catul decimal range shall be from b'0000 0000 0000 0000 0000 0000 (0x00 = 0 dec) to b'0001 1111 1111 1111 1111 (131071 dec). The red-colored digits are left-shifted by 2-bit!
        {: .highlight }

    * <b>4th Step (Encode single byte of a dicimal node - 1 byte)</b>
       * Change the single byte to hex.

    * <b>Encoding Result</b>
       ```scss
       1.2.840.113549.1.7.1 => 0x2A, 0x86, 0x48, 0x86, 0xF7, 0x0D, 0x01, 0x07, 0x01 (Length = 9 bytes)
       ``` 

3. <b>Decoding Rule</b>
   * Same Rule is applied in reverse. Please refer to <b>[Pic.4]</b>
   <p align="center">
   <img src="../../../asset/images/Decode1.JPG" width="750"/>
   <br><b>[Pic.3] Decoding 4 byte</b></p>

[OID Encoding and Decoding Rule]:https://learn.microsoft.com/en-us/windows/win32/seccertenroll/about-object-identifier
[Layman's Guide]:https://luca.ntop.org/Teaching/Appunti/asn1.html
