---
layout: default
title: 1_Bluetooth OP GATT Flow
parent: Topic_BLE
nav_order: 4
---

# Bluetooth Low Energy (BLE) GATT Flow
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Definition

## Reference

## Terminology

## Deep Dive
1. <b> BLE Stack Architecture</b>: is made of three levels, APPs, HOST, and CONTROLLERS. Each level has corresponding components.
<p align="center">
<img src="../../../asset/images/BLEStack.JPG" width="300"/>
<br><b>[Pic.1] BLE Stack Architecture </b></p>

   * <b>Physical Layer (PHY)</b>: It refers to the physical radio used for communication and for modulating/demodulating the data. It operates in the ISM band (2.4 GHz spectrum).
   * <b>Link Layer</b>: It is the layer that interfaces with the Physical Layer (Radio) and provides the higher levels an abstraction and a way to interact with the radio (through an intermediary level called the HCI layer which we’ll discuss shortly). It is responsible for managing the state of the radio as well as the timing requirements for adhering to the Bluetooth Low Energy specification.
   * <b>Host Controller Interface (HCI)</b>: This layer is a standard protocol defined by the Bluetooth specification that allows the Host layer to communicate with the Controller layer. These layers could exist on separate chips, or they could exist on the same chip. You can assume that this layer is similar to RTE layer of AUTOSAR architecture.
   * <b>Logical Link Control and Adaptation Protocol (L2CAP)</b>: This layer acts as a protocol multiplexing layer. It takes multiple protocols from the upper layers and places them in standard BLE packets that are passed down to the lower layers beneath it.
   * <b>Security Manager</b>: It defines the protocols and algorithms for generating and exchanging keys between two devices. It involves five security features: pairing, bonding, authentication, encryption, and message integrity.
   * <b>Attribute Protocol (ATT)</b>: ATT defines how a server exposes its data to a client and how this data is structured. the ATT protocol is also responsible for data transfer of the attributes via methods to read and write the data. This is possible via commands, requests, responses, notification, indications and confirmation messages defined by the ATT.
   * <b>Generic Access Profile (GAP)</b>: This layer provides a framework that defines how BLE devices interact with each other.
    * Role of BLE devices (Broadcaster, Observer, Central, Peripheral)
    * Advertisement (Broadcasting, Discovery, Advertisement Parameters, Advertisement Data) => We saw the data that have to be included in ADV_IND at 2_Bluetooth LE Link Layer Connection Establishment - VNI_CE_SW_ICH - Automotive Confluence (continental.cloud). The data will be handled at GAP layer.
    * Connection Establishment (Initiating Connections, Accepting Connections, Connection Parameters)
    * Security
   * <b>Generic Attribute Profile (GATT)</b>: Here is a fundamental question. Why we need the connection between two BLE devices? The answer is that they want to exchange data. Thus, without the connection, it is not possible to have a bidirectional data transfer between two BLE devices. That brings us to the concept of GATT.  (GATT) is a layer which works on top of the ATT and uses it to exchange data between devices. Generic Attribute Profile (GATT) defines
    * The format of the data exposed to an opposite BLE device
    * The procedure needed to access the data exposed
   * <b>Similar to the GAP, the GATT also distinguishes two roles</b>: the server and the client.
    * The Server is the device that exposes the data it controls or contains, and possibly some other aspects of its behavior that other devices may be able to control.
    * A Client is the device that interfaces with the Server with the purpose of reading the Server’s exposed data and/or controlling the Server’s behavior.

2. <b>GATT</b>: has the hierachical concept which is composed of attribute, service, characteristic, and profile. Such concept is dedicitaed to low-power energy consumption. A discovery of services, characteristics and descriptors is required for the GATT to operate. Thus, We need to understand them, as it builds the foundation for further data transactions. The example of the hierachical structure can be found in the picture below.
<p align="center">
<img src="../../../asset/images/GATT.JPG" width="300"/>
<br><b>[Pic.2] GATT Structure </b></p>

   * Attribute: An attribute is a data representation format which is exposed by the server to the client. It is composed of four fields
    * Type Description
      * It defines the type of an attribute. That is, the type specifies what this attribute represents. For example, service declaration, characteristic definition, and characteristic value.
    * Attribute type, defined by a UUID
      * All UUIDs in Bluetooth Low Energy have a length of 128 bits. (e.g., UUID XXXXXXXX-0000-1000-8000-00805f9b34fb)
      * The standardized services, characteristics, and descriptors are defined in the Bluetooth Low Energy stack as a shortened 16-bit values.
      * As the UUIDs must be a 128 bit, all the predefined attribute UUIDs will be integrated in the form of a base UUID XXXXXXXX-0000-1000-8000-00805f9b34fb A Heart Rate service for example has the UUID 0x180D which leads to a UUID of: 0000180d-0000-1000-8000-00805f9b34fb.
      * An advantage of using these predefined UUIDs, is that these 16 bits can be used during the service discovery instead of the complete 128 bits.
      * For services which are not defined by the Bluetooth Low Energy stack, custom UUIDs can be created.
      * The custom defined UUIDs can use all the remaining 128-bits UUIDs apart from the previously mentioned base UUID. For example, The bold part in UUID XXXXXXXX-0000-1000-8000-00805f9b34fb
    * Attribute handle, which is an unsigned number unique for the attribute
      * This is a 16-bit value that the server assigns to each of its attributes — think of it as an address. This value is used by the client to reference a specific attribute and is guaranteed by the server to uniquely identify the attribute during the life of the connection between two devices. The range of handles is 0x0001-0xFFFF, where the value of 0x0000 is reserved.
    * Attribute permissions, which control if the client can read or modify a resource
      * Permissions determine whether an attribute can be read or written to, whether it can be notified or indicated, and what security levels are required for each of these operations. These permissions are not defined or discovered via the Attribute Protocol (ATT) but rather defined at a higher layer (GATT layer or Application layer).
    * Attribute value
      * This field represent the value of the corresponding attribute.
   * Service: a grouping of one or more Attributes (some of which are Characteristics). It’s meant to group together related Attributes that satisfy a specific functionality on the Server. A service discovery process is required to enable the data transfer between both BLE devices. This service discovery will discover all the capabilities of the peripheral device by requesting its attributes. The service discovery process thus contains a series of requestsand responses from central and peripheral.
   * Characteristic: it is always part of a service and it represents a piece of information/data that a Server wants to expose to a client. For example, the Battery Level Characteristic represents the remaining power level of a battery in the device which can be read by a client.
   * Profile: They are much broader in definition from services. They are concerned with defining the behavior of both the Client and Server when it comes to Services, Characteristics and even Connections and security requirements. Services and their specifications, on the other hand, deal with the implementation of these Services and Characteristics on the Server side only.
   * The Service is a collection of information about some actions we want to do, and the Characteristic is a set of actual data corresponding the service. The actual data for the services and characteristics are stored in the attribute table. The picture below shows the structure of Service, Characteristic, Profiles, and Attribute Table. For example, as shown the attribute table, we define a service having UUID 0x2800 in the first row, and this service has the value, 0x180D (UUID), that represents Heart Ration Measurement Service. In Bluetooth SIG, it defines Service UUIDs that are used commonly. In the second row, we define a characteristic having 0x2803 UUID, and this characteristic definition has the value, including several characteristics UUIDs and Descriptors, such as UUID 0x2A37 with the handle value 0x000E, UUID 0x2803 with handle value 0x000F, etc.. The third row shows the corresponding characteristic having 0x2A37 UUID and it has the actual Heart Beat value. One BLE device can has several services, and one service can include several characteristics.
   <p align="center">
   <img src="../../../asset/images/GATTST.JPG" width="600"/>
   <br><b>[Pic.3] Detailed GATT Structure</b></p>
   * Another example can be found at the picture below that shows the two types of services, Generic Access Profile and Cable Replacement. GAP is the service we have shown so far. The later is the custom-specific service. Thus, if OEMs or customers want to modify GATT service, we have to modify the GATT Layer. Normally, the establishment of GATT layer is handled by BLE device driver provided by the vendor. Although there is no chance to implement the BLE device driver, we need to know the strcuture of GATT Service and the format of the service and characteristic.
   <p align="center">
   <img src="../../../asset/images/GATTST2.JPG" width="600"/>
   <br><b>[Pic.4] Detailed GATT Structure2</b></p>

   {: .highlight }
   So far, we have seen what is GATT. From the GATT server side (Vehicle), it exposes the attributes (service and characteristic) to the GATT client (Smart Device) to exchange the data between them. From the GATT client side, it is mandatory to parse what services and characteristics are available. That is why this step is named as 'flow', and such interaction is conducted as shown the picture below. In order to parse the services and characteristics stored in the attribute table, 'Read by Group Type Reqeust/Response' and ATT_READ_BY_REQ/RES' are used.

3. <b>Read By Group</b>
   * <b>Read By Group Type Request</b>: The Read By Group Type Request is used to obtain the values of attributes where the attribute type is known, the type of a grouping attribute as defined by a higher layer specification, but the handle is not known. The format of this request is as shown the table below. Since we don't know the starting and ending handle, we parse the handle from 0x0001 to 0xFFFF. It is okay to assume that the handle is the address pointing each attribute. Since we are going to parse the supported services, we will put 0x2800 to the UUID field. So, you can see such parameters in the step 3 of Figure 19-2 above. Only the attributes with attribute handles between and including the Starting Handle and the Ending Handle with the attribute type that is the same as the Attribute Group Type given will be returned.
   <p align="center">
   <img src="../../../asset/images/R1.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p>

   * <b>Ready By Group Type Response</b>:The Read By Group Type Response is sent in reply to a received Read By Group Type Request and contains the handles and values of the attributes that have been read. The format of this request is as shown the table below. The Read By Group Type Response shall contain complete Attribute Data. An Attribute Data shall not be split across response packets. The Attribute Data List is ordered sequentially based on the attribute handles.
   <p align="center">
   <img src="../../../asset/images/R2.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p> 

     * Since the requested UUID is 0x2800, the Attribute Data List of the respone will contain a list of tuples corresponding to the found services. The each tuple consist of
       * The attribute handle where the requested declaration was found (where UUID is equal to 0x2800)
       * The so-called end-group handle – the last attribute handle before a new similar declaration is found, basically the last attribute that belongs to this service
       * The attribute value of the declaration; this is the service UUID of the found service (the service type, e.g. Heart Rate Service); it may be a 16-bit, a 32-bit or a 128-bit UUID
     * The format of Attirbute Data List can be found in the table below.
       * Service 1: from 0x0001 to 0x0004, value (16-bit service UUID) = 0x1234 
       * Service 2: from 0x0006 to 0x0009, value (16-bit service UUID) = 0x5678
       * Service 3: from 0x0010 to 0x0018, value (128-bit service UUID) = 0x1234567890abcdef1234567890abcdef
       * Service 4: from 0x0020 to 0x0030, value (16-bit service UUID) = 0x2345
     <p align="center">
     <img src="../../../asset/images/R3.JPG" width="300"/>
     <br><b>[Pic.x] xx</b></p>

     * After the first request (0x0001-0xFFFF), the Read By Group Type response will include these bytes, in order (LSB first): 0x01, 0x00, (handle of start of service), 0x04, 0x00 (end-group handle), 0x34, 0x12 (UUID), 0x06, 0x00, 0x09, 0x00, 0x78, 0x56. The next service type is a 128-bit UUID, so the Server cannot include it in the response and it stops after the first two services. The Client, upon seeing that the last service in this list ended at 0x0009, proceeds to sending a new Read By Group Type request with this handle range: 0x0010-0xFFFF. The response will only include Service 3, so the Client will need to issue a third request with the range: 0x0019-0xFFFF.
     
     * <b>Read by Type Request (ATT_READ_BY_TYPE_REQ)</b>:The Read By Type Request is used to obtain the values of attributes where the attribute type is known but the handle is not known. Only the attributes with attribute handles between and including the Starting Handle and the Ending Handle with the attribute type that is the same as the Attribute Type given will be returned. To search through all attributes, the starting handle shall be set to 0x0001 and the ending handle shall be set to 0xFFFF.
     <p align="center">
     <img src="../../../asset/images/R4.JPG" width="300"/>
     <br><b>[Pic.x] xx</b></p>

     * <b>Read by Type Response (ATT_READ_BY_TYPE_RES)</b>:  The Read By Type Response is sent in reply to a received Read By Type Request and contains the handles and values of the attributes that have been read. The Read By Type Response shall contain complete handle-value pairs. Such pairs shall not be split across response packets. The handle-value pairs shall be returned sequentially based on the attribute handle.
     <p align="center">
     <img src="../../../asset/images/R5.JPG" width="300"/>
     <br><b>[Pic.x] xx</b></p>

     * The format of Attirbute Data List can be found in the table below.
     <p align="center">
     <img src="../../../asset/images/R6.JPG" width="300"/>
     <br><b>[Pic.x] xx</b></p> 
4. <b>Service Group</b>
   *  You might realize that 'Read By Group Type Reqeust/Response' and 'Read by Type Request/Reponse' look like they have same BLE packet structure. They seem to be only using different Attribute Opcode. So what is the difference between them? In consequence, 'Read By Group Type Reqeust/Response' is to find specific group attributes and rages (i.e., Services). And 'Read by Type Request/Reponse' is to read attribute values of a given type (i.e., Characteristics). So, with 'Read By Group Type Reqeust/Response', we can identify of the structure of services, and with 'Read by Type Request/Reponse', we can figure out the data included in the one service group, characteristics. The picture below shows that grouping services give a hierarchical structure. 
   <p align="center">
   <img src="../../../asset/images/R7.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p>
   
    * <b>Find Information Request</b>: For discovering descriptor in the attribute table, the Find Information Request is used to obtain the mapping of attribute handles with their associated types. Only attributes with attribute handles between and including the Starting Handle parameter and the Ending Handle parameter will be returned.
    <p align="center">
    <img src="../../../asset/images/R8.JPG" width="300"/>
    <br><b>[Pic.x] xx</b></p>

    * <b>Find Information Response</b>: The Find Information Response is sent in reply to a received Find Information Request and contains information about this server. The Find Information Response shall have complete handle-UUID pairs.
    <p align="center">
    <img src="../../../asset/images/R9.JPG" width="300"/>
    <br><b>[Pic.x] xx</b></p>

    * <The format field value of Table 3.7 can be found in the picture below.
    <p align="center">
    <img src="../../../asset/images/R10.JPG" width="300"/>
    <br><b>[Pic.x] xx</b></p>

5. L2CAP Connection-Oriented Channel
   * L2CAP, which is an component of the BLE stack architecture above, provides connection-oriented and connectionless data services to upper layer protocols with protocol multiplexing capability and segmentation and reassembly operation. The L2CAP architecture is shown in the picture below.
   <p align="center">
   <img src="../../../asset/images/L2CAP.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p>

   * The main feature of L2CAP is 'Multiplexing'; the protocol multiplexing capability is used to route the connection to the correct upper layer protocol. It is similar to the ComStack of Autosar Architecture. The L2CAP layer of the BLE Host Stack performs data multiplexing through the use of a Channel ID (CID), which is a 2-byte value contained by the L2CAP Basic Header, as seen in the general L2CAP packet format is as follows.
   <p align="center">
   <img src="../../../asset/images/PLD.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p>

   * The L2CAP Connection-Oriented Channel function started to be supported from BLE 4.0, which improved the throughput of the data transmission. Several CIDs (Channel Identifier) are reserved. For instance, 0x0004 is used by the ATT, 0x0006 by the SMP, 0x0005 is the Signaling Channel. The 0x0040-0x007F range is assigned for the Connection-Oriented Channels. CID identifies the destination channel endpoint of the packet. The LE Credit-Based Flow Control is an L2CAP mode of operation for Connection-Oriented Channels in which an endpoint device has complete control over how many packets the peer device may send at any time through the use of credits. One credit represents the permission to send one LE-frame over the established channel. The receiving device gives a fixed number of credits to the sending device, so that the latter knows exactly how many LE-frames it may send. The attempt to send more frames than permitted results in the receiving device closing the channel. An initial number of credits are given by each device to the peer when the connection is created. Subsequently, each device can give more credits as it deems necessary; if a device wants to send more data than it is allowed, there’s nothing it can do but wait until it receives more credits from the peer. A Protocol Service Multiplexer must be associated with any credit-based connection. It is abbreviated as LE_PSM and is a two-byte value identifying the protocol that uses the credit-based connection. The specification defined the ranges for allowed LE_PSMs; some are reserved by specific profiles (e.g. IPSP) and others may be dynamically used by custom application profiles.
   * An LE Credit-Based connection is created and managed on the L2CAP Signaling Channel, with the following commands and parameters
     * LE Credit-Based Connection Request, with the parameters:
       * LE_PSM
       * Source Channel ID (SCID)
       * Maximum Transmission Unit (MTU)
       * Maximum Payload Size (MPS)
       * Initial Credits
     * LE Credit-Based Connection Response
       * Destination CID (DCID)
       * MTU
       * MPS
       * Initial Credits
       * Result
     * LE Flow Control Credit
       * CID
       * Credits
   * The requesting device must specify the LE_PSM for which the connection is opened, the CID to which the responder will send data (SCID), the supported MTU and MPS and the initial number of credits given to the responder. In turn, the responding device can reject the request (reason is found in the Result parameter) or can accept it (Result is 0) and also provide the CID where the requesting device can send data (DCID), initial credits for this CID and its own MTU and MPS. The minimum values between the two devices’ MTU and MPS are used. MTU is the maximum transmission unit, that is, the maximum size of data a SDU (Service Data Unit) to be transmitted. MPS is the maximum payload size, that is, the maximum size of a single L2CAP packet, which is an LE-frame and “eats” one credit when is transmitted. At any moment, any of the two devices can send a Flow Control Credit command to increase the credits of the peer on the given CID.
   * Let's take one example.
     * Device A sends an LE Credit-Based Connection Request:
       * LE_PSM = 0x0080
       * SCID = 0x004a
       * MTU = 100
       * MPS = 40
       * Initial Credits  = 5
     * Device B answers with an LE Credit-Based Connection Response:
       * DCID = 0x004b
       * MTU =260
       * MPS = 60
       * Initial Credits  = 10
   * From Device A side, MTU is100 and MPS is 40. This means that the responding device can send SDUs of maximum 100 bytes, but these 100 bytes will be fragmented to fit into LE-frames of maximum 40 bytes payload.
   * An LE-frame contains:
     * The basic L2CAP header: Length (2 bytes) and CID (2 bytes)
     * Payload (length maximum equal to MPS)
   * When a large size of SDU is sent, the first LE-frame Payload starts with 2 bytes of SDU Length then follow the first MPS-2 bytes of SDU. Subsequent LE-frames each contain MPS bytes of SDU, while the last one may contain less than MPS bytes. If device A from the example above wants to send an SDU of 90 bytes, it would need 3 credits because the 90 bytes can be split in 3 LE-frames:
   <p align="center">
   <img src="../../../asset/images/PLD2.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p>

   * To summarize, in BLE chip, the attribute table is established, and it acts as the Server. The Clinet, another smart device having BLE function, want to read data provided by the Server. To know where are the data stored, the Client needs to know the structure of the attribute table provided by the Server. Thus, they execute 'Read By Group Type Reqeust/Response' and 'Read by Type Request/Reponse' procedure. After the GATT establishment, the L2CAP Connection-Oriented Channel is created, and we need to follow the protocol provided by L2CAP Connection-Oriented Channel for future data exchage between the two BLE devices. So, the procedure I explained so far is executed at the Bluetooth Owner Pairing GATT Flow step. Now we are ready to send and receive data between the two BLE devices.
   
6. How the data ara assembled and fragmented?
   *  Now, you have become familiar with what is the attribute. The picture below shows an example where an attribute is assembled via ATT PDU and L2CAP and fragmented in Link Layer. ATT Opcode has six categories: Commands, Requests, Responses, Notifications, Indications, Confirmations. The payload of L2CAP includes the ATT PDUs (one or multiple). Then the L2CAP packet is fragmented to multiple LE_Frame in the Link Layer. The number of services fitting in one Link Layer packet is dependent on the ATT_MTU, as we saw the example above. If all the services do not fit into a single Link Layer packet, the master must request the other services starting from the end handle of the last received service. This until an “attribute not known” response is sent by the peripheral, which indicates that the service discovery is finished. Finally, the each LE_Frame is transferred through the air by Physical Layer.
   <p align="center">
   <img src="../../../asset/images/DataAssem.JPG" width="300"/>
   <br><b>[Pic.x] xx</b></p>