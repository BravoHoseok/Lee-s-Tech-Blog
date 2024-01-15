---
layout: default
title: 6_Advanced Encryption Standard (AES)
parent: Topic_Cybersecurity
nav_order: 9
---

# Advanced Encryption Stadard (AES)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
Advanced Encryption Standard (AES) is the encryption standard set by the U.S. National Institute of Standards and Technology (NIST) in 2001, which is a type of 
symmetric cryptography. It means that the two objects have the same secret key that should not be revealed to attackers. AES is widely used today as it is a much stronger than DES and triple DES despite being harder to implement. In the past, older encryption methods were sufficient for protecting sensitive information, but they no longer meet today's security needs. That's where AES encryption comes in. AES offers a high level of security and has become the go-to choice for encrypting sensitive data.

## Reference
Please refer to the listed reference below. That help you understand what is AES.
- [Blcok Cipher Mode]
- [Plaintest Wikipedia]
- [Ciphertext Wikipedia]
- [AES Lecture Note Purdue]
- [Substitute Bytes Transformation]
- [Multiplicative Inverse]

## Deep Dive
1. <b>Advanced Encryption Stnadard (AES)</b>
   * In this chapter, we will take a look at AES128-CBC (Cipher Block Changing) and AES128-ECB (Electronic codebook) which are used normally.
   * AES has several mode according to specific formulas which is used in encryption process as shown in <b>[Pic.1]</b>
    <p align="center">
    <img src="../../../asset/images/AESMODE.JPG" width="700"/>
    <br><b>[Pic.1] AES Mode</b></p>

   * AES supports a couple of different key size that has different rounds. The main difference of using different key is how you generate the key schedule from the key. See the table below.
    
      |   Rounds   | Key Size |           
      |:-----------|:---------|
      | 10 rounds  | 128-bits |
      | 12 rounds  | 192-bits |
      | 14 rounds  | 256-bits |
        
   * Except for the last round in each case, all other rounds are identical.
   * In terms of computation, we normally use 'Matrix' as consisting of a 4x4 arrays of bytes as shown in <b>[Pic.2]</b>
    <p align="center">
    <img src="../../../asset/images/AESMATRIX.JPG" width="300"/>
    <br><b>[Pic.2] AES128 Matrix Notation</b></p> 

   * Note that, Notice that the first four bytes of a 128-bit input block occupy the first column in the 4 × 4 array of bytes. The next four bytes occupy the second column, and so on.
   
   {: .highlight }
   > The 4 × 4 array of bytes shown above is referred to as <b><span style="color:Greenyellow"> the state array in AES!!</span></b>
   
   * AES also has the notion of a word. A word consists of four bytes, that is 32 bits (8 bits x 4). Therefore, each column of the state array is a word, as is each row.
   * Each round of processing works on the input state array and produces an output state array.
   * The output state array produced by the last round is rearranged into a 128-bit output block.
   * Unlike DES, the decryption algorithm differs substantially from the encryption algorithm. Although, overall, very similar steps are used in encryption and decryption, their implementations are not identical and the order in which the steps are invoked is different, as mentioned previously.
   * AES is <b><span style="color:Greenyellow"> an iterated block cipher</span></b> in which plaintext is subject to multiple rounds of processing, with each round applying the same overall transformation function to the incoming block. <b>[Pic.3]</b> will help you understand how AES processing work.
   <p align="center">
   <img src="../../../asset/images/AESCBC.JPG" width="700"/>
   <br><b>[Pic.3] AES128-CBC Mode Encryption</b></p>  

   * AES is a type of <b><span style="color:Greenyellow"> key-alternating block ciphers</span></b>. In such ciphers, each round first applies a diffusion-achieving transformation operation, which is a combination of linear and nonlinear steps, to the entire incoming block, which is then followed by the application of the round key to the entire block.
   * About the security of AES, considering how many years have passed since the cipher was introduced in 2001, all of the threats against the cipher remain theoretical — meaning that their time complexity is way beyond what any computer system will be able to handle for a long time to come.
   * AES was designed using <b><span style="color:Greenyellow">  the wide-trail strategy.</span></b>

2. <b>AES Encryption KEY</b>
   * Key is input parameter of AES encryption and decryption processing. As shown in <b>[Pic.2]</b>, the size of Key shall be 128 bits. For the compuration processing, KEY is also arranged in the form of an array of 4 × 4 bytes. Just think about that, from the computation of matrix, a input plain text and key should have same form.
   * The four column words of the key array are expanded into a schedule of 44 words as shown in <b>[Pic.4]</b> (Kn represents a word).

   {: .highlight } 
   > Note that the reason of this expansion is that AES128-CBC conducts 10 rounds and each round consumes four words from the key schedule. Thus, <b><span style="color:Greenyellow"> the resulting size of this expansion shall be 40 word.</span></b> For the first 4 words of 44 words, they are used for adding to the input state array before any round-based processing can begin, and the remaining 40 words used for the ten rounds of processing that are required for the case a 128-bit encryption key.
   
    <p align="center">
    <img src="../../../asset/images/ExpandedKey.JPG" width="500"/>
    <br><b>[Pic.4] AES128 KEY Expansion</b></p> 
    
3. <b>Overall Structure of AES128-CBC Processing</b>
   * AES Computation structure of Encryption and Decryption is shown in <b>[Pic.5]</b>.
   * The input state array is XORed with the first four words of the key schedule.
   * The same thing happens during decryption — <b><span style="color:Greenyellow">except doing XOR the ciphertext state array with the last four words of the key schedule.</span></b>

    <p align="center">
    <img src="../../../asset/images/AesStructure.JPG" width="450"/>
    <br><b>[Pic.5] AES128 Encryption Processing Structure</b></p>

    * AES128 Encryption and Decryption step are decribed in the table below from programming perspective. Each round has 4 steps excpet the last round.

        | Step |           Encryption           |               Decryption               |          
        |:-----|:-------------------------------|:---------------------------------------|
        | 1st  | Substitue Bytes Transformation | Inverse Shift Rows                     |
        | 2nd  | Shift Rows                     | Inverse Substitue Bytes Transformation |
        | 3rd  | Mix Columns                    | Add round key                          |
        | 4th  | Add round key                  | Inverse Mix Columns                    |

    {: .highlight } 
    > The last round for encryption does not involve the “Mix columns” step. The last round for decryption does not involve the “Inverse mix columns” step.

   1. <b>Substitue Bytes Transformation</b>
      * The Substitute Bytes transformation, also known as the "SubBytes" or "S-Box" operation, is a nonlinear substitution operation that replaces each byte in the input data with a corresponding byte from a fixed substitution table called the "S-Box.
      * The S-Box is a predefined, constant table (Look up table) containing 256 entries, each 8 bits in length. The S-Box is carefully designed to introduce non-linearity and confusion into the data, making it resistant to various cryptographic attacks, such as differential and linear cryptanalysis.
      * The S-Box is a fixed, publicly known component of the AES algorithm and serves to obscure the relationship between the input and the output, making it more challenging for attackers to deduce patterns or information about the encrypted data. S-Box (Look Up Table) is implemented with GF(2^8) and bit scrambling.
      * The SubBytes transformation helps in achieving the confusion and diffusion properties required for a strong encryption algorithm.
      * Confusion ensures that the relationship between the key and the ciphertext is complex, making it difficult to analyze, while diffusion ensures that changes in one part of the plaintext affect a large part of the ciphertext.
      * Thus, The goal of the substitution step is to reduce the correlation between the input bits and the output bits at the byte level.
      
      {: .highlight } 
      > The bit scrambling part of the substitution step ensures that the substitution cannot be described in the form of evaluating a simple mathematical function.

      <p align="center">
      <img src="../../../asset/images/MI.JPG" width="450"/>
      <br><b>[Pic.6] S-Box Look Up Table in GF(2^8) </b></p> 

      * When we extract the S-Box Look Up table, we apply Multiplicative Inverse (MI) and bit scrambling based on the resulting MI.
      * The specific calucation of MI and Bit Scrambling can be found at [Multiplecative Inverse Cal] and [AES Lecture Note Purdue] repectively

   2. <b>Shift Rows</b>
      * Shift Rows is depcited in animation at [AES Animation Explanation]
      * The rule of shift rows is as follows.
        * <b><span style="color:Greenyellow"> Not shifting</span></b> the first row of the state array at all.
        * Circularly shifting the second row by <b><span style="color:Greenyellow"> one</span></b> byte to the left.
        * Circularly shifting the third row by <b><span style="color:Greenyellow"> two</span></b> bytes to the left.
        * Circularly shifting the last row by <b><span style="color:Greenyellow"> three</span></b> bytes to the left

   3. <b>Mix Columns</b>

   4. <b>Add round key</b>



[Blcok Cipher Mode]:https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation
[Plaintest Wikipedia]:https://en.wikipedia.org/wiki/Plaintext
[Ciphertext Wikipedia]:https://en.wikipedia.org/wiki/Ciphertext
[AES Lecture Note Purdue]:https://engineering.purdue.edu/kak/compsec/NewLectures/Lecture8.pdf
[Substitute Bytes Transformation]:https://www.linkedin.com/pulse/substitute-bytes-transformation-muhammad-azeem-qureshi-unqtf/
[Multiplicative Inverse]:https://en.wikipedia.org/wiki/Multiplicative_inverse
[AES Basic]:https://www.youtube.com/watch?v=O4xNJsjtN6E
[AES Animation Explanation]:https://www.youtube.com/watch?v=gP4PqVGudtg
[Galua Field - Kor Lecture]:https://www.youtube.com/watch?v=8tyL27VVUeo
[Multiplicative Inverse]:https://medium.com/analytics-vidhya/bytesubstitution-a-follow-up-1806affdadd8
[Multiplecative Inverse Cal]:https://medium.com/analytics-vidhya/bytesubstitution-a-follow-up-1806affdadd8