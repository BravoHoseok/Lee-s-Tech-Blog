---
layout: default
title: 7_Secure Hash Algorithm (SHA)
parent: Topic_Cybersecurity
nav_order: 10
---

# Advanced Encryption Stadard (AES)
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
Secure Hash Algorithm (SHA) is a family of cryptographic functions designed to keep data secured. SHA is a extension version origined from Hash algorithm and data structure, which is equipped with security policiese, such as bitwise, XOR, moduloar additions and compression functions. The main purpose of SHA is to produce a fixed-size string that looks nothin glike the original input string. In other word, we conceals the original data to the extent where it's impossible for attackers to predict and hijack the original data. One of the key features of SHA is that these algorithms are designed to be *<U>one-way functions</U>*, meaning that once they’re transformed into their respective hash values, it’s virtually impossible to transform them back into the original data. A few algorithms of interest are SHA-1, SHA-2, and SHA-3, each of which was successively designed with increasingly stronger encryption in response to hacker attacks.

## Reference
Please refer to the listed reference below. That help you understand what is SHA.
- [Hash Concept]
- [SHA High Level Explanation]
- [Step by Stpe SHA256 Process]
- [Salt WikiPedia]
- [Initialization Vector WikiPedia]
- [Hash Table WikiPedia]

## Terminology
* <b><span style="color:LightSkyBlue">Salt</span></b> 
  * In cryptography, a salt is *<U>random data</U>* that is used as *<U>an additional input to a one-way function</U>* that hashs data, a password or passphrase. Definition can be cound at [Salt WikiPedia]
* <b><span style="color:LightSkyBlue">Digest</span></b>
  * Digest is the kind of *<U>initial chain variables and dynamically changing variables by SHA calcuation.</U>* For example, in case of SHA-256bit, it has 8 components and each size is 32-bit. Each are denoted as a, b, c, d, e, f, g, h, and the 8 components are also denoted by 'H' as a group. Here H means Hash Value
  * Each component (a to h) is calculated like
    * a = sqrt(prime) - int(prime)
    * a = a (leftshift) 32 bit (= multiply 2^32)
    * a = int(a) => convert Hex value
    <p align="center">
    <img src="../../../asset/images/digestcomponents.JPG" width="450"/>
    <br><b>[Pic.1] SHA Digest </b></p>

  * These variables are dynamically updated during the SHA algorithm computation.
  * In the final step of SHA algorithm computation, the chain variables are computed with the initial hash value (IV) or the previous hash value of each hash function block.
  
* <b><span style="color:LightSkyBlue">Initialization Vector</span> </b>
  * The definition of <b>Initialization Venctor</b> can be found at [Initialization Vector WikiPedia]. As you guess, seeing the name literally, it is nothing but a starting variable.
  * IV is an input to a cryptographic primitive being used to provide the initial state. The IV is typically required to be random or pseudorandom, but sometimes an IV only needs to be unpredictable or unique.
  * In SHA, IV corresponds to a initial variable for SHA. And they are used to extract the initial digest variables.
  * For example, In case of SHA256, they are a set of h[0..7].
  * This initial vector shall the initial value of chain variable.
* <b><span style="color:LightSkyBlue">K value</span></b>
  * In case of SHA256, the number of a K value set shall be 64 (i.e., k1...k63)
  * K value is *<U>constant value</U>*, and the calculation of K is similar to the one of extracting initial digest variables.
    * k1 = cqrt(prime) - int(prime)
    * k1 = k1 << 32bit (= multiply 2^32)
    * k1 = int(k1)

## Deep Dive
1. <b>Hash Table</b>
   * The definition and the basic notion of Hash Table can be found in [Hash Table WikiPedia]. Hash Table is sometimes called Hash Data Strcutre, Hash, Hash Map, but they means the same thing.
   * Hash Table is a data structure that implements dictionary type of array (associative array).
   <p align="center">
   <img src="../../../asset/images/HashTableWiki.JPG" width="400"/>
   <br><b>[Pic.2] Hash Table </b></p> 
   * As shown in <b>[Pic.2]</b> the input string (Keys) will go through *<U>Hash Function</U>* which returns a unique index of array. And this index is coupled with the input string. Then, we sotre the corresponding data to the input string in the place that the resulting index points. We call this storage buckets.
   * This kind of indexing approach paves the way to the fast computation on searching something. Imagine that you are looking a word in the dictionary. If you received the input word, how do you return the corresponding meaning to it in terms of porgramming? The first notion would be brute force search in dictionary type of array by using for-loop. But when it comes to scalability, it would not be a good choice. Think about that how the website can parse your ID and check if the ID is registered or not on their database.
   * The key elements of Hash Table is *<U>Hash Function</U>*. Let's take a look at it a little bit.
2. <b>Hash Function</b>
   * Hash Function is the key element of Hash Table. It should gurantee that the resulting index of an input key should be <b><span style="color:Greenyellow">unique!</span></b>
   * Imagine that the resulting index of your ID and your friend ID via Hash Function is the same. And we save the each password in the space that the index points. Although we can solve this issue with the Chaining Hash technique, but it would bring about haphazard search function. So, the corresponding indices to two inputs should be not conflicted to save the unique password correctly.
   * According to how to implement Hash function, it will affect the usage of memory and computation speed.
3. <b>Secure Hash Algorithm (SHA)</b>
   * SHA is based on the concept of Hash. It means that SHA also has so called <b>unique</b> characteristic. A common application of SHA is to the password encryption, as the server side only needs to keep track of a specific user’s hash value, rather than the actual password. By doing so, they can protect user's credentials form attackers.
   * Another example of SHA is Digital Signature. We can compute 'Hash value' of hex file, then append it to the end of hex file. This signed hex file will be delivered to an opposite. The recipient will calculate the data they get except Digital Signature. By comparing to the oroginal Digital Signature, they can verify whether or not the data is manipulated by attackers, because the modified data will show the different hash vale.
   * SHA has the follwing characteristics.
     * <b>Pre-Image Resistance</b>: It hard and time-consuming for an attacker to find an original message, which is originated from one-way.
     * <b>Second-Image Resistance</b>: When a message 1 is known, but it is hard to find another message 2 which hashes to the same value. This is the point I mentioned 'unique'. Without this property, the two messages will yield the same hash value.
     * <b>Collisition Resistance</b>: Because of the second characteristic of SHA, this property is necessary. THus, finding two inputs that hash to the same hash value is extremely difficult.
   * SHA has a couple of algirhtm as shown the table below. The number at the end of SHA means the size of digest variables.

   |  Type |                         Algorithm                          |                Operand              |  Collision |          
   |:------|:-----------------------------------------------------------|:------------------------------------|:-----------|
   | MD5   |                                                            | +, and, xor, rot, add (mod 2^32), or|  Detected  |
   | SHA-0 |                                                            | +, and, or, xor, rotl               |  Detected  |
   | SHA-1 |                                                            | +, and, or, xor, rotl               |  Detected  |
   | SHA-2 | SHA-224, SHA-256, SHA-348, SHA-512                         | +, and, or, xor, rotl, rotr         |            | 
   | SHA-3 | SHA3-224, SHA3-256, SHA3-348, SHA3-512, SHAKE128, SHAKE256 | +.nd, Xor, Rot, Not                 |            | 

4. <b>SHA256 process</b>
   * SHA1 process can be found in [SHA High Level Explanation]. Here, I will explain SHA256 process with the help of [Step by Stpe SHA256 Process].
     * Divide "Message" to be encrypted based on 512-bit (64 bytes). If the size of the dividen block is lower than 512 bits, add padding bit (0) to fit the block size 512-bit. The red box of <b>[Pic.3]</b> shows the binary data of 'hello' string. So, the first step is to encode the input to binary using UTF-8.
     <p align="center">
     <img src="../../../asset/images/SHA2_Step1.JPG" width="400"/>
     <br><b>[Pic.3] SHA256 Step1 </b></p> 

     * Append a single '1' to the end of message string as shown <b>[Pic.4]</b>.
     <p align="center">
     <img src="../../../asset/images/SHA2_Step2.JPG" width="400"/>
     <br><b>[Pic.4] SHA256 Step2 </b></p>

     * Append the original message length (b'101000 = 40 dec) at the end of the message block as a 64-bit big-endian integer as show <b>[Pic5]</b>
     <p align="center">
     <img src="../../../asset/images/SHA2_Step4.JPG" width="400"/>
     <br><b>[Pic.5] SHA256 Step3 </b></p> 

     * As shown in <b>[Pic.6]</b>, append 407 zeros (Padding) between the encoded message and the length block so that the message block is a multiple of 512. In this case 40 + 1 + 407 + 64 = 512.
     <p align="center">
     <img src="../../../asset/images/SHA2_Step3.JPG" width="400"/>
     <br><b>[Pic.6] SHA256 Step4 </b></p>

     * Break the entire message block into 512-bit chunks. In this example, we got 1 chunk.
     * Create a 64-entry message schedule array w[0..63] of 32-bit word. w[0..15] shall be filled with the message block itself. w[16..63] will be calculated.
     * For example, w[16] = w[0] + gamma0 + w[9] + gamma1. So we can generalize w[n] = w[16-n] + gamma0 + w[25-n] + gamma1 (n >= 16)
     * gamma 0 is [(w[k] right rotate 7) XOR (w[k] right rotate 18) XOR (w[k] right shift 3)] (1<= k <= 48)
     * gamma 1 is [(w[m] right rotate 17) XOR (w[m] right rotate 19) XOR (w[m] right shift 10)] (14<= m <= 61)
     <p align="center">
     <img src="../../../asset/images/SHA2_HashSchedule.JPG" width="700"/>
     <br><b>[Pic.7] SHA256 Hash Schedule </b></p>

     {: .highlight } 
     So, the point of this caluclation step is to prepare SHA schedule table by calculating w[16] to w[63], which are the element of the shedule based on the law above. This schedule concept is similar to AES Key Expansion and Schedule.

     * There are two things we need to prepare before SHA round computation. The first one is Initial Vector (Initial Digest or Initial Chain Variables or Initial Hash Value) a, b, c, d, e, f, g, h. IV is the first 32 bits of the franction part of the square roots of the first 8 prime 2..19.
     * For example,
       * a = sqrt(2) = 1.4142135623730950488016887242097
       * a = a - int(a) = 0.4142135623730950488016887242097
       * a = a * 2^32 = a (leftShift) 32 = 1,779,033,703.95209938490
       * a = int (a) = 1,779,033,703
     * So, the first Hash Value will be as shown in <b>[Pic.8]</b>
     <p align="center">
     <img src="../../../asset/images/SHA2_IV.JPG" width="400"/>
     <br><b>[Pic.7] SHA256 IV </b></p>

     * For the Constant array k[0..63], the same priciple shall be applied above. Take the first 64 prime 2 .. 311. But one difference is cube roots of the primes.
       * k1 = cqrt(2) - int(2)
       * k1 = k1 << 32bit (= multiply 2^32)
       * k1 = int(k1)
     * The result will be as shown in <b>[Pic.8]</b>
     <p align="center">
     <img src="../../../asset/images/SHA2_K.JPG" width="400"/>
     <br><b>[Pic.8] SHA256 K Constant Table </b></p> 

     * The final step is 64 round computation based on IV, w[0..63], k[0..63]. During 64 round, the hash values (a, b, c, d, e, f, g, h) will be updated 64 times as shown in <b>[Pic.9]</b>
    <p align="center">
    <img src="../../../asset/images/SHA2_Round.JPG" width="700"/>
    <br><b>[Pic.9] SHA256 64 Round Computation </b></p> 

     * The final hash values would be output of SHA after 64 round. Then this output would be the next IV for the next SHA block to encrypt next 512 bits chunck. In our case, we only have one data chunk, the final hash value of 'hello' should be <b><span style="color:Red">2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824</span></b> as shown in <b>[Pic.10]</b>
     <p align="center">
    <img src="../../../asset/images/SHA2_Output.JPG" width="400"/>
    <br><b>[Pic.10] SHA256 Output </b></p>  

5. <b>SHA256 - One 512-bits Data Chunk</b>
   *  The whole process I mentioned above is well represented in <b>[Pic.11]</b>.
   <p align="center">
   <img src="../../../asset/images/SHA2_Process.JPG" width="700"/>
   <br><b>[Pic.11] SHA256 One 512-bits Data Chunk Process </b></p>  

6. <b>SHA - Multiple 512-bits Data Chunk</b>
   * <b>[Pic.13]</b> shows how to extract the hash value of the entire 'Message'. The 64 round calculation corresponds to the hash function block (Orange Box). The result of each hash function is fed in to the next input of the next hash function.
   * S0 corresponds to Initial Vector (IV)
   <p align="center">
   <img src="../../../asset/images/SHA2_Whole.JPG" width="700"/>
   <br><b>[Pic.13] SHA256 Multiple 512-bits Data Chunk Process </b></p>

{: .highlight }
>The exact equation of the 64 round computation can be found in [Step by Stpe SHA256 Process].

[Hash Concept]:https://www.authgear.com/post/password-hashing-salting
[SHA High Level Explanation]:https://brilliant.org/wiki/secure-hashing-algorithms/
[Step by Stpe SHA256 Process]:https://sha256algorithm.com/
[Salt WikiPedia]:https://en.wikipedia.org/wiki/Salt_(cryptography)
[Initialization Vector WikiPedia]:https://en.wikipedia.org/wiki/Initialization_vector
[Hash Table WikiPedia]:https://en.wikipedia.org/wiki/Hash_table
