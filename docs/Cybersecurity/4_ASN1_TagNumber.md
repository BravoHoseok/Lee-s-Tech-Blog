---
layout: default
title: 4_ASN.1 Tag
parent: Topic_Cybersecurity
nav_order: 7
---

# Private and Public Key
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
To ensure that encodings are not ambiguous, every ASN.1 type is associated with a tag. A tag consists of two parts: the class and the number as we saw at [3_Tag Length Value] chapter. TLV encoding may be recursive (netest construction, constructed format). One value field can consist of one or more other TLVs, which are encoded. No maximum level of nesting is defined, so complex structures can be created. The porperty of Tag is unique. However, the same tag to two (or more) can be assigned to different types, but should avoid ambiguity. The context in which an instance of the type occurs must be sufficient to allow unique identification.

## Reference
Please refer to the listed reference below. That help you understand what is TLV in deatil.
- [ASN1 Tyep and Tag]
- [ASN1 Type]
- [Object Identifier of ASN1]

## Deep Dive
1. Tyeps and Tags
   * Let's take a look at the table below. We cna figure out which tag corresponds to a type, and they are defined by ASN.1.

        |       Type       | Tag (dec) |       Type        | Tag (dec) |       Type      | Tag (dec) |          
        |:-----------------|:----------|:------------------|:----------|:----------------|:----------|
        | Bit String       | 03        | GraphicString     | 25        | SET             | 17        |
        | BMPString        | 30        | IA5String         | 22        | SET OF          | 17        |
        | BOOLEAN          | 01        | INTEGER           | 02        | TeletexString   | 20        |
        | CHARACTER STRING | 29        | NULL              | 05        | T61String       | 20        |
        | CHOICE           |           | NumericString     | 18        | TIME            | 14        |
        | DATE             | 31        | ObjectDescriptor  | 07        | TIME-OF-DAY     | 32        |
        | DATE-TIME        | 33        | OBJECT IDENTIFIER | 06        | UniversalString | 28        |
        | DURATION         | 34        | OCTET STRING      | 04        | UTCTime         | 23        |
        | EMBEDDED PDV     | 11        | PrintableString   | 19        | UTF8String      | 12        |
        | ENUMERATED       | 10        | REAL              | 09        | VideotexString  | 21        |
        | EXTERNAL         | 08        | RELATIVE-OID      | 13        | VisibleString   | 26        |
        | GeneralString    | 27        | SEQUENCE          | 16        |                 |           |
        | GeneralizedTime  | 24        | SEQUENCE OF       | 16        |                 |           |
        
   * As an example, if we use Universal Class, Primitive TLV, and Tag 03, then the tag field value will be 00h + 0h + 03h = 0x03.
   * Let's take a look at a couple of tag. The details and more information can be found at [ASN1 Tyep and Tag]

2. Bit String (Tag: 0x03)
   *  The corresponding Value field to ASN.1 BIT STRING type is <b>arbitrary length strings of bits.</b>
   *  The Value field contains a leading byte that specifies the number of bits left unused in the final byte of content.
   *  For example, In <b>[Pic.2]</b>, the last 5 bits are not used.
   <p align="center">
    <img src="../../../asset/images/BITStringEx.JPG" width="600"/>
    <br><b>[Pic.2] Bit String Example</b></p>

3. Octet String (Tag: 0x04)
   *  Octet String is similar to Bit String data types. But one difference is Octet String cannot have unused bits.
   *  The leading bytes must not be added to the contents.
   *  Example is shown in <b>[Pic.3]</b>.
   <p align="center">
    <img src="../../../asset/images/Octet.JPG" width="500"/>
    <br><b>[Pic.3] Octet String Example</b></p>  

4. Boolean (Tag: 0x01)
   * The value of type Boolean can be TRUE(0x00) or FALSE(0xFF).
   * The example is shown in <b>[Pic.4]</b>.
   <p align="center">
    <img src="../../../asset/images/Boolean.JPG" width="500"/>
    <br><b>[Pic.4] Boolean Example</b></p>

5. Integer (Tag: 0x02)
   * The value of integer contains the encoded integer if it is positive, or its two's complement if it is negative.
   * If the integer is positive but the high order bit is set to 1, a leading 0x00 is added to the content to indicate that the number is not negative.
   * The example is shown in <b>[Pic.5]</b>.
   <p align="center">
    <img src="../../../asset/images/Integer.JPG" width="500"/>
    <br><b>[Pic.5] Integer Example</b></p>

6. Object Identifier (Tag: 0x06)
   *  Object Identifier is composed of the combination of number and dot.
   *  The type of Object Identifier is listed in [Object Identifier of ASN1].
   *  As an example, Object Identification will be written with <b>1.2.840.113549.1.7.1</b>
   *  We will see the Encoding/Decoding rule of Object Identifier in [5_ASN1_ObjectIdentifier] chapter in detail, because they are not attached to certificate as the plan form.
   <p align="center">
    <img src="../../../asset/images/objectidentifier.JPG" width="400"/>
    <br><b>[Pic.6] Object Identifier Example</b></p>


[3_Tag Length Value]:https://bravohoseok.github.io/Lee-s-Tech-Blog/docs/Cybersecurity/3_TagLengthValue/
[ASN1 Tyep and Tag]:https://www.oss.com/asn1/resources/asn1-made-simple/asn1-quick-reference/asn1-tags.html#Definition
[ASN1 Type]:https://learn.microsoft.com/en-us/windows/win32/seccertenroll/about-der-encoding-of-asn-1-types
[Object Identifier of ASN1]: https://public.websites.umich.edu/~x509/ssleay/asn1-oids.html
[5_ASN1_ObjectIdentifier]: http://127.0.0.1:4000/docs/Cybersecurity/5_ASN1_ObjectIdentifier/