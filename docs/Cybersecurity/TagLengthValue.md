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
Tag Length Value (TLV) is the encoding rule proposed by X.509 organization. The data that should be included in a certificate have to be encrypted with the format of TLV. TLV is a byte array that has tag indicating what the data is, length specifying its length, and value itself. The TLV allows groups of variable-length data elements to be combined into one buffer. ASN.1 (Abstract Syntax Notation One) specifies TLV standard. TLVs are the subsets of DER (Distinguished Encoding Rules) / BER (Basic Encoding Rules) format certificate. A simple TLV example is shown at <b>[Pic.1]</b>

<p align="center">
    <img src="../../../asset/images/TLVFormat.JPG" width="300"/>
    <br><b>[Pic.1] Tag-Length-Value Format</b>
</p>

## Reference
Please refer to the listed reference below. That help you understand what is TLV in deatil.
- [Tag Length Value Explanation]
- [ANS.1 Tag Type]
- [BER-TLV]

## Deep Dive

---
[Tag Length Value Explanation]:https://docs.yubico.com/yesdk/users-manual/support/support-tlv.html#length
[ANS.1 Tag Type]:https://www.oss.com/asn1/resources/asn1-made-simple/asn1-quick-reference.html
[BER-TLV]:https://pypi.org/project/ber-tlv/
