---
layout: default
title: 2_Tag Length Value (TLV)
parent: Topic_Cybersecurity
nav_order: 4
---

# Private and Public Key
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
Tag Length Value (TLV) is the encoding rule proposed by X.509 organization. The data that should be included in a certificate have to be encrypted with the format of TLV. TLV is a byte array that has tag indicating what the data is, length specifying its length, and value itself. The TLV allows groups of variable-length data elements to be combined into one buffer. ASN.1 (Abstract Syntax Notation One) specifies TLV standard. TLVs are the subsets of DER (Distinguished Encoding Rules) / BER (Basic Encoding Rules) format certificate. A simple TLV example is shown at <b>[Pic.1]</b>. For thoese who want to learn what is certificate, DER, and BER, please check the reference below. In this topic, we will just focus on TLV.

<p align="center">
    <img src="../../../asset/images/TLVFormat.JPG" width="300"/>
    <br><b>[Pic.1] Tag-Length-Value Format</b>
</p>

## Reference
Please refer to the listed reference below. That help you understand what is TLV in deatil.
- [Tag Length Value Explanation]
- [ANS.1 Tag Type]
- [ANS.1 and BER and DER]
- [Certificate Introduction]

## Deep Dive
1. TLV format <br/>
   * TLV format is described by X.609 as show in <b>[Pic.2]</b>
   * <p align="center">
    <img src="../../../asset/images/TLVDetailFormat.JPG" width="500"/>
    <br><b>[Pic.2] Tag-Length-Value Format by X.609</b></p>
      * <b><span style="color:Gray;">Tag:</span></b> Tag has variable size (1 to 4 byte). It is included in Identifier Octets field. The formate of Tag field is shown in <b>[Pic.3]</b>. Let's dive into each bit field.
        * <p align="center"> <img src="../../../asset/images/IdentifierFormat.JPG" width="500"/> <br><b>[Pic.3] Identifier (Tag) format</b></p>
        * <b>bit8 -7</b>: Class field


---
[Tag Length Value Explanation]:https://docs.yubico.com/yesdk/users-manual/support/support-tlv.html#length
[ANS.1 Tag Type]:https://www.oss.com/asn1/resources/asn1-made-simple/asn1-quick-reference.html
[BER-TLV]:https://pypi.org/project/ber-tlv/
[ANS.1 and BER and DER]:https://luca.ntop.org/Teaching/Appunti/asn1.html
[Certificate Introduction]:https://letsencrypt.org/docs/a-warm-welcome-to-asn1-and-der/