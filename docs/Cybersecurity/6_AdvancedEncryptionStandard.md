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
Advanced Encryption Standard (AES) is the encryption standard set by the U.S. National Institute of Standards and Technology (NIST) in 2001, which is a type of symmetric cryptography. It means that the two objects have the same secret key that should not be revealed to attackers. AES is devised firstly by two Belgian cryptographers, Joan Daemen and Vincent Rijmen, who submitted a proposal to NIST. In this proposal, they proposed 'Rijndael' algorithm that is a family of ciphers with different key and block sizes. AES is widely used today as it is a much stronger than DES and triple DES despite being harder to implement. In the past, older encryption methods were sufficient for protecting sensitive information, but they no longer meet today's security needs. That's where AES encryption comes in. AES offers a high level of security and has become the go-to choice for encrypting sensitive data.

## Reference
Please refer to the listed reference below. That help you understand what is AES.
- [Blcok Cipher Mode]
- [Plaintest Wikipedia]
- [Ciphertext Wikipedia]
- [AES Lecture Note Purdue]
- [Substitute Bytes Transformation]
- [Multiplicative Inverse Wikipedia]
- [AES Basic]
- [Animated AES Encryption Steps]
- [Galua Field Lecture - Kor]
- [Multiplecative Inverse and how to extract S-Box Table]
- [How to calculate Mix Column-Kor] 
- [AES Explanation from Microchip]
- [AES Step By Step Hands On]


## Deep Dive
1. <b>Advanced Encryption Stnadard (AES)</b>
   * In this chapter, we will take a look at AES128-CBC (Cipher Block Changing) and AES128-ECB (Electronic codebook) which are used widely.
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

   * Note that the first four bytes of a 128-bit input block occupy the first column in the 4 × 4 array of bytes. The next four bytes occupy the second column, and so on.
   
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
   > Note that the reason of this expansion is that AES128-CBC conducts 10 rounds and each round consumes four words from the key schedule. Thus, <b><span style="color:Greenyellow"> the resulting size of this expansion shall be 40 word.</span></b> For the first 4 words of 44 words, they are used for adding to the input state array before any round-based processing can begin. This 4 words is called Initial Vector. And the remaining 40 words used for the ten rounds of processing that are required for the case a 128-bit encryption key.
   
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

   1. <b>Encryption</b>
      * <b>Step 1: Substitue Bytes Transformation</b>
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

      * <b>Step 2: Shift Rows</b>
         * Animated-Shift Rows can be found in [AES Animation Explanation]
         * The rule of shift rows is as follows.
           * <b><span style="color:Greenyellow"> Not shifting</span></b> the first row of the state array at all.
           * Circularly shifting the second row by <b><span style="color:Greenyellow"> one</span></b> byte to the left.
           * Circularly shifting the third row by <b><span style="color:Greenyellow"> two</span></b> bytes to the left.
           * Circularly shifting the last row by <b><span style="color:Greenyellow"> three</span></b> bytes to the left

      * <b>Step 3: Mix Columns</b>
         * This step operates on the State column by column. Each column is treated as a vector of bytes and is multiplied by a fixed matrix to get the column for the modified State.
         * The fixed Matrix for Mix Colums step is shown in <b>[Pic.7].</b>
            <p align="center">
            <img src="../../../asset/images/FixedMatrix.JPG" width="200"/>
            <br><b>[Pic.7] Fixed Matrix in GF(2^8) </b></p>
         * A column of a stat matrix is multiplied by the fixed matrix as shown in <b>[Pic.8].</b>
            <p align="center">
            <img src="../../../asset/images/MixedColumnEq.JPG" width="600"/>
            <br><b>[Pic.8] Mixed Column Step Equation in GF(2^8) </b></p>    
         
         {: .highlight }
         > Note that <b><span style="color:Greenyellow"> The additions are merely XOR operations</span></b> in the AES world.
         > Note that, when you compute 'mutiply' matrixs, <b><span style="color:Greenyellow"> we should consider the range of galois field GF(2^8) of each element</span></b>. If the result go beyond the GF, we need to compute 'mod' computation. See <b>[Pic.9]</b> as an example
         
         <p align="center">
         <img src="../../../asset/images/temp.JPG" width="600"/>
         <br><b>[Pic.9] Multiplication in GF(2^8) </b></p>
         
      
      * <b>Step 4: Add round key</b>
         * Feed the encrypted output from n round cipher block to the next round plain text (actually do XOR computation between these two inputs ot the next cipher block)
         * <b>[Pic.3]</b> above will help you understand this step.

      * <b>Key Expansion Algorithm</b>
         * The AES Key Expansion algorithm is used to derive the 128-bit round key for each round from the original 128-bit encryption key.
         * We will get the initial key of 128-bits. The first four bytes of the first column of the encryption key constitute the word w0, the next four bytes the word w1, and so on. See <b>[Pic.10]</b>.
         
         <p align="center">
         <img src="../../../asset/images/KeyExpansion1.JPG" width="200"/>
         <br><b>[Pic.9] Key Expansion Fist Step</b></p>
         
         * The words [w0, w1, w2, w3] are bitwise XOR’ed with the input block before the round-based processing begins.
         * The remaining 40 words of the key schedule are used fourwords at a time in each of the 10 rounds.
         * <b>[Pic.11]</b> illustrate how to excute key expansion algorithm from the initial key. In this picture, the function 'g' consists of the follwing steps. The return value from g function is only utilized, when calculation the first word of a each word group (i.e., w4, w8, w12, w16 ...)
           * <b>Step 1</b>: Perform a one-byte left circular rotation on the argument 4-byte word.
           * <b>Step 2</b>: Perform a byte substitution for each byte of the word returned by the previous step by using the same 16 × 16 lookup table as used in the SubBytes step of the encryption rounds.
           * <b>Step 3</b>: XOR the bytes obtained from the previous step with what is known as a <b>round constant</b>. The round constant is a word whose three rightmost bytes are always zero. Therefore, XOR’ing with the round constant amounts to XOR’ing with just its leftmost byte.
           * <b><span style="color:Greenyellow"> As an example of the follwing first word of a each group, it shall be w4 = w0 XOR g(w3)</span></b>
           * The remaining 3 words of each word group will be calculated by follwing <b>[Pic.11]</b>

         <p align="center">
         <img src="../../../asset/images/KeyExpansion2.JPG" width="500"/>
         <br><b>[Pic.11] Key Expansion Processing Step</b></p>


      * You can get high level intution of the entire process of AES encryption in <b>[Pic.12]</b>.
      <p align="center">
      <img src="../../../asset/images/AESBlockDiagram.JPG" width="700"/>
      <br><b>[Pic.12] AES Block Cipher Process</b></p>

   2. <b>Decryption</b>
      * The decryption process is a straightforward reverse of the encryption algorithm as shown in <b>[Pic.13]</b>. Let's take a look at the corresponding reverse steps.
      <p align="center">
      <img src="../../../asset/images/AESDecryptFlowChart.JPG" width="500"/>
      <br><b>[Pic.13] AES Decryption Flow Chart</b></p>

      * <b>Step 1: Inverse Shift Rows</b>
        * For encryption, we did the circularly left-shifting by 0, 1, 2, and 3 for each row. For decryption, we do the circularly right-shifting by 0, 1, 2, and 3
      * <b>Step 2: Inverse Substitue Bytes Transformation</b>
        * For InvSub, we utilize another look up table as shown in [Pic.14], which is called 'Inverse S-Box'.
        <p align="center">
         <img src="../../../asset/images/InvSBox.JPG" width="500"/>
         <br><b>[Pic.14] S-Box for Decryption</b></p> 
      * <b>Step 3: Add round key</b>
        * XOR computation with a key and a encrypted input.
      * <b>Step 4: Inverse Mix Columns</b>
        * This step is exactly the inverse process of MixColumns used for encryption. It performs a matrix multiplication of the state with a static matrix.

[Blcok Cipher Mode]:https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation
[Plaintest Wikipedia]:https://en.wikipedia.org/wiki/Plaintext
[Ciphertext Wikipedia]:https://en.wikipedia.org/wiki/Ciphertext
[AES Lecture Note Purdue]:https://engineering.purdue.edu/kak/compsec/NewLectures/Lecture8.pdf
[Substitute Bytes Transformation]:https://www.linkedin.com/pulse/substitute-bytes-transformation-muhammad-azeem-qureshi-unqtf/
[Multiplicative Inverse Wikipedia]:https://en.wikipedia.org/wiki/Multiplicative_inverse
[AES Basic]:https://www.youtube.com/watch?v=O4xNJsjtN6E
[Animated AES Encryption Steps]:https://www.youtube.com/watch?v=gP4PqVGudtg
[Galua Field Lecture - Kor]:https://www.youtube.com/watch?v=8tyL27VVUeo
[Multiplecative Inverse and how to extract S-Box Table]:https://medium.com/analytics-vidhya/bytesubstitution-a-follow-up-1806affdadd8
[How to calculate Mix Column-Kor]:https://hipolarbear.tistory.com/36
[AES Explanation from Microchip]:https://onlinedocs.microchip.com/pr/GUID-E67D40A5-DD02-4379-82B4-9BE2A7E7BEA0-en-US-3/index.html?GUID-4BCE9427-FF62-4374-8F90-0C87F18BDC44
[AES Decryption Explanation]:https://www.herongyang.com/Cryptography/AES-Standard-Decryption-Algorithm.html
[AES Step By Step Hands On]:https://www.cryptool.org/en/cto/aes-step-by-step