---
layout: default
title: 0_Certificates Preparation
parent: Topic_BLE
nav_order: 3
---

# Certificates Preparation
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition
Before exchanging the certificates including public/private keys between two devices as NFC/BLE owner pairing procedure, they should go through the preparation step called <b>Public Key Infrastructure (PKI) mechanism</b>, which is <b>Certification Chain Model</b>. In [Car Connectivity Consortium], there are two type of the trust certification chain model of SE root, variant 1, and variant 2. In this chapter, we will step in variant 1 model, since it is more flexible and likely the way most device OEMs are going to use.

## Reference


## Deep Dive
1. <b>Certification Chain Model</b>
    * The purpose of this model is to provide <b><span style="color:Greenyellow"> full offline capability</span></b> through the verification chain. So, <b><span style="color:Greenyellow">Cross-Validation</span></b> of the certificates which is conducted from the back-end side is possible.
    * The basic principle of the validation procedure is utilizing the public key issued by trusted Certificate Authority (CA). When we build a signature to be appended at the end of each certificates, we normally use private key (or public key), security algorithm (e.g., SHA), and reveal the corresponding public key to an opponent.
    * If the known public key is trusted, we can verify a signature of another certificate, which is extracted from the known public key. For better understanding, just assume that Certificate 'A' is signed by Certificate 'B'.
    * This mechanism will prevent <b><span style="color:Greenyellow"> Man In The Middle Attack (MITM)</span></b>
    * However, this cahin model is based on an assumption where the truxted CAs don't allow attacker to invade their (server) system. If attackers infilterate into the system and obtain the trusted certificates, they can break the whole system. There were such kind of cybersecurity issues reported by [OWASP].
    * Thus, it is important for the trusted CAs to ensure that their fortress is impregnable. 
    * <b>[Pic.1]</b> shows variant 1 certification chain model. The key factors of this model are <b> 1) Sever, 2) Secure Element, 3) Device OS, and 4) Vehicle ECU</b>. Note that Secure Element is located in the device as a hardware component.
    <p align="center">
    <img src="../../../asset/images/PKI.JPG" width="800"/>
    <br><b>[Pic.1] PKI variant 1 model </b></p>
    * In <b>[Pic.1]</b>, the number on each arrow presents the mechnism sequence. SE-Root CA, Device OEM CA, Vehicle OEM CA at the top of the mechanism are the trusted entities that manage and issue each certificate. SE-Root at the bottom of the mechanism is also another trusted entity that handles SE-Root.SK and SE-Root.PK.
    * In this mechanism, a public key is generated by Elliptic Curve Cryptography and a private key.
    * Variant 1 Cetification Chain Model start from the initial state where each CAs is embedded in this mechanism.
    * The communication between Server and Secure Element in <b>[Pic.1]</b> is executed by the proprietary interface provided by Device OEM (e.g., Apple) as shown in <b>[Pic.2]</b>
    <p align="center">
    <img src="../../../asset/images/Interface.JPG" width="500"/>
    <br><b>[Pic.2] Proprietary Interface </b></p>
2. <b>Type of Certificate</b>
    * <b>(1) SE Root CA</b>: issues SE-Root CA Certificate[A] by inserting SE-rootCA.PK and trusted signature (self-signature). This certificate sotred in it's server.
    * <b>(2) SE Root</b>: issues SE-Root Certificate[B] by inserting SE-Root.PK
    * (3) The signature signed by SE-Root CA is appended at the bottome of SE-Root Certificate[B], and it is stored in Secure Element.
    * <b>(4) DeviceOEM CA</b>: issues DeviceOEM CA Certificate[D] by inserting DeviceOEM.PK and the trusted signature (self-signature). This certificate is Device OEM Root of Digital Key System. Depending on the OEM's implementation, it can be stored in Server or Secure Element.
    * <b>(5)</b>: Absence
    * <b>(6) Device OEM CA Certificate[F]</b>: is extracted from DeviceOEM CA Certificate[D] except Truxted Signature.
    * <b>(7)</b>: The signature by Vehicle OEM CA is appended at the bottome of DeviceOEM CA Certificate[F]. We call it 'Vehicle OEM signed Deviced OEM CA Certificate'.
    * <b>(8) Vehicle OEM CA</b>: issues Vehicle OEM CA Certificate[J] by inserting VehicleOEM.PK and the trusted signature (self-signature). This certificate is embedded into Device OS and Vehicle ECU respectively.
    * <b>(9) DeviceOEM CA Certificate[F]</b>: can be embedded into Vehicle ECU (Optional)
    * <b>(10) Instance CA</b>: is intermediate CA that represents 'DeviceOEM CA' and reside within a device. Ths instance CA is allocated per Vehicle OEM. Ths instance CA issues Instance CA Attestation[C] by inserting InstanceCA.PK.
    * <b>(11) SE-Root</b>: embedes its signature to Instance CA Attestation[C]. 
    * <b>(12) SE-Root Certificate[B]</b>: is transferred to 'Server'. 
    * <b>(13) Instance CA Attestation[C]</b>: is transferred to 'Server'. 
    * <b>(14) The signature of Instance CA Attestation[C]</b>: is verified by SE-Root.PK in SE-Root Certificate[B], since the signature in Instance CA Attestation[C] is embedded by SE-Root.PK which is inserted to SE-Root Certificate[B]. 
    * <b>(15)</b>: If Instance CA Attestation[C] is successfully verified by SE-Root Certificate[B], Instance CA Attestation[C] is transferred to a device OS of the corresponding device. 
    * <b>(16) DeviceOEM CA</b>: signs the transferred Instance CA Attestation[C] which becomes Device OEM signed Instance CA Certifiate[E] 
    * <b>(17) Digital Key[G]</b>: is generated by Digital Key Applet (One digital key per a vehicle). All digial key elements as shown in <b>[Pic.3]</b> are provided by the vehicle and transferred to the device during Owner Pairing. 
    <p align="center">
    <img src="../../../asset/images/Friend.JPG" width="400"/>
    <br><b>[Pic.3] Friend Digital Key Structure </b></p>
    * <b>(18)</b>: When Digital Key is created, Digital Key Certificate[H] is generated by inserting DigitalKey.PK. Then, Instance CA signs the public key of digital key structure using it's private key.
    * <b>(19)</b>: Vehicle Key will be embedded into Vehicle ECU, and Vehicle.PK will be extracted by Vehicle.SK and ECC. 
    * <b>(20)</b>: Behicle Key issues Vehicle Key Certificate[K] by inserting Vehicle.PK, and Vehicle OEM CA signature will be appended at the end of this certificate.
    * <b>(21)</b>: Before accepting Digital Key Creation Data from the vehicle during Owner Pairing, the device cerifies the origin of Vehicle.PK (Verification of Vehicle OEM CA Signature) by utilizing Vehicle OEM CA Certificate[J].
    
    {: .highlight }
    Note that the device may use Device OEM signed Vehicle OEM CA Certificate[M] in order to validate the authenticity of Vehicle.PK which is contained in Vehicle Key Certificate[K]
3. <b>Type of Certificate Chain</b>
    * <b>Device Certificate Chain</b>: <b>[Pic.4]</b> shows the dependency between certificates from the device side in order to validate the origin of the certificates. With SE-Root.PK, SE Root CA Certificate[B] verifies Instance CA Attestation[C]. Device OEM CA trusts SE Root CA and Instance CA. So, with DeviceOEM.PK, Device OEM CA Certificate[D] validates Instance CA Certificate[E].
    <p align="center">
    <img src="../../../asset/images/Chain1.JPG" width="500"/>
    <br><b>[Pic.4] Device Certificate Chain </b></p>
    * <b>Vehicle Certificate Chain</b>: <b>[Pic.5]</b> shows the dependency between certificates from Vehicle ECU in order to validate the origin of the certificates. Device OEM CA trusts Vehicle OEM CA. From vehicle side, with VehicleOEM.PK, Vehicle OEM CA Certificate[J] verifies Vehicle Key Certificate[K], since Vehicle OEM CA Signature is extracted from VehicleOEM.PK in Vehicle OEm CA Certificate[J].
    <p align="center">
    <img src="../../../asset/images/Chain2.JPG" width="150"/>
    <br><b>[Pic.5] Vehicle Certificate Chain </b></p>
    * <b>Owner Pairing Certificate Chain</b>: <b>[Pic.7]</b> shows the dependency between certificates from Vehicle ECU in order to validate the origin of the certificates. Device OEM CA trusts Device OEM CA, and Device OEM CA trusts Instance CA Certificate[E]. With VehicleOEM.PK in Vehicle OEM CA Certificate[J] we validate DeviceOEM CA Certificate[F], since VehicleOEM Signature in that certificate is derived from VehicleOEM.PK. If we determine successfully the validity of DeviceOEM CA Certificate[F], with DeviceOEM.PK, we verify Instance CA Certificate[F]. The same procedure is applied to the last dependency. With InstanceCA.PK, we verify the validity of Digital Key Certificate[H].
    <p align="center">
    <img src="../../../asset/images/Chain3.JPG" width="200"/>
    <br><b>[Pic.7] Owner Pairing Certificate Chain </b></p>
4. Based on Certificate Chain Model, we get the necesary certificates and keys (VehicleOEM signed DeviceOEM CA Certificate[F], Vehicle OEM CA Certificate[J], Digital Key Certificate[H], Digital Key[G], Vehicle Key Certificate[K], Vehicle Key).

[Car Connectivity Consortium]:https://carconnectivity.org/digital-key/ccc-digital-key-certification/
[OWASP]:https://owasp.org/