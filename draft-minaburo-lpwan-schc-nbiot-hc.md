---
stand_alone: true
ipr: trust200902
docname: draft-ietf-lpwan-schc-over-nbiot-06
cat: info
pi:
  symrefs: 'yes'
  sortrefs: 'yes'
  strict: 'yes'
  compact: 'yes'
  toc: 'yes'
title: SCHC over NB-IoT
abbrev: SCHC NB-IoT
wg: lpwan Working Group
author:
- ins: E. Ramos
  name: Edgar Ramos
  org: Ericsson 
  street: Hirsalantie 11
  city: 02420 Jorvas, Kirkkonummi
  country: Finland
  email: edgar.ramos@ericsson.com
- ins: A. Minaburo
  name: Ana Minaburo
  org: Acklio
  street: 1137A Avenue des Champs Blancs
  city: 35510 Cesson-Sevigne Cedex
  country: France
  email: ana@ackl.io
normative:  
  RFC8376:
  RFC8724:
  RFC5795:
		  
  
--- abstract

The Static Context Header Compression (SCHC) specification describes header compression and fragmentation 
functionalities for LPWAN (Low Power Wide Area Networks) technologies.  
The Narrow Band Internet of Things (NB-IoT) architecture may adapt SCHC to improve its capacities.

This document describes the use of SCHC over the NB-IoT wireless access and provides elements for efficient parameterization.

--- middle
 
# Introduction {#Introduction}

The Static Context Header Compression (SCHC) {{RFC8724}} defines a header compression scheme, 
and fragmentation functionality suitable for the Low Power Wide Area Networks (LPWAN) networks defined in {{RFC8376}}.

In an NB-IoT network, header compression efficiently brings Internet connectivity to the node. 
This document describes the SCHC parameters used to performs the static context header compression into the NB-IoT wireless access. 
This document assumes functionality for NB-IoT of 3GPP release 15. 
Otherwise, the text explicitly mentions other versions' functionality.

# Terminology

This document will follow the terms defined in {{RFC8724}}, in {{RFC8376}}, and the TGPP23720. 

* CIoT. Cellular IoT
* NGW-C-SGN. Network Gateway - CIoT Serving Gateway Node
* Dev-UE. Device - User Equipment
* RGW-eNB. Radio Gateway - Node B. Base Station that controls the UE 
* EPC. Evolved Packet Connectivity. Core network of 3GPP LTE systems.
* EUTRAN. Evolved Universal Terrestrial Radio Access Network. Radio network from LTE based systems.
* NGW-MME. Network Gateway - Mobility Management Entity. Handle mobility of the UE
* NB-IoT. Narrow Band IoT. Referring to 3GPP LPWAN technology based in LTE architecture but with additional optimization for IoT and using a Narrow Band spectrum frequency. 
* NGW-SGW. Network Gateway - Serving Gateway. Routes and forwards the user data packets through the access network
* HSS. Home Subscriber Server. It is a database that performs mobility management
* NGW-PGW. Network Gateway - Packet Data Node Gateway. An interface between the internal with the external network
* PDU. Protocol Data Unit. Data packets including headers that are transmitted between entities through a protocol.
* SDU. Service Data Unit. Data packets (PDUs) from higher layers protocols used by lower layer protocols as a payload of their own PDUs that has not yet been encapsulated.
* IWK-SCEF. InterWorking Service Capabilities Exposure Function. Used in roaming scenarios and serves for interconnection with the SCEF of the Home PLMN and is located in the Visited PLMN
* NGW-SCEF. Network Gateway - Service Capability Exposure Function. EPC node for exposure of 3GPP network service capabilities to 3rd party applications.

# Architecture

~~~~~~
                    
  +---+                            +------+
  |Dev| \              +-----+ ----| HSS  |    
  +---+  \             | NGW |     +------+
         |             |-MME |\__
          \          / +-----+   \   
  +---+    \+-----+ /    |       +------+
  |Dev| ----| RGW |-     |       | NGW- |
  +---+     |-eNB |      |       | SCEF |---------+
           /+-----+ \    |       +------+         |
          /          \ +------+                   |
         /            \| NGW- |  +-----+   +-----------+
  +---+ /              | CSGW |--| NGW-|---|Application|
  |Dev|                |      |  | PGW |   |   Server  |
  +---+                +------+  +-----+   +-----------+
 
 
~~~~~~
{: #Figure-Archi title='3GPP network architecture'}

The Narrow Band Internet of Things (NB-IoT) architecture has a more complex structure. 
It relies on different NGWs from different providers and can send data by different paths, each path with different characteristics such as bandwidths, feedback, and layer two reliability and segmentation. 

{{Figure-Archi}} shows this architecture where the Network Gateway Cellular Internet of Things Serving Gateway Node (NGW-CSGN) optimizes co-locating entities in different paths. For example, a Dev using the path form by the Network Gateway Mobility Management Entity (NGW-MME), the NGW-CSGW, and Network Gateway Packet Data Node Gateway (NGW-PGW) may get a small bandwidth transmission from some bytes to one thousand bytes only.
                                                                                                                                                               
<!-- The NGW-CSGN also supports at least some of the following CIoT EPS Optimizations:

* Control Plane CIoT EPS Optimization for small data transmission.
* User Plane CIoT EPS Optimization for small data transmission.
* Necessary security procedures for efficient small data transmission.
* SMS without combined attach for NB-IoT only UEs.
* Paging optimizations for coverage enhancements.
* Support for non-IP data transmission via SGi tunneling and/or SCEF.
* Support for Attach without PDN (Packet Data Network) connectivity. -->

Another node introduced in the NBIoT architecture is the Network Gateway Service Capability Exposure Function (NGW-SCEF), 
which securely exposes service and network capabilities to entities external to the network operator. OMA and OneM2M define the northbound APIS. In this case, the path is small for data transmission. The main functions of the NGW-SCEF are:
* Connectivity path 
* Device Monitoring 

<!--* Non-IP Data Delivery (NIDD) established through the SCEF.
* Monitoring and exposure of event related to UE reachability, loss of connectivity, location reporting, roaming status, communication failure and change of IMEI-IMSI association. 

~~~~~~

                                          +-------+                                 
                                          |  HSS  |
                                          +-+-----+
                               NGW         /
 DEV              RGW      +---------+  __/S6a
              +--------+   | +-----+ +_/                  NGW
+----+ C-Uu   |        +---+-+ MME | | T6i+--------+ T7 +----+
|CIOT+--------+  eNB   |S1 | |     +-+----+IWK-SCEF+----+SCEF|
|UE  |        |(NB-IoT)|   | +---+-+ |    +--------+    +----+
+----+        +--------+   |     |   |        
                           |C-SGN|   |     
                           |     |S11|
                +------+   |     |   |        
+--------+LTE-Uu|      |   |  +--+-+ |        
|LTE eMTC|(eMTC)|eNB   +---+--+SGW | | S8+---+    +-----------+
|   UE   +------+(eMTC)|S1 |  |    +-+---+PGW|SGi |Application|
+--------+      +------+   |  +----+ |   |   +----+Server (AS)|
                           +---------+   +---+    +-----------+
   DEV            RGW          NGW        NGW         App

~~~~~~                                                                                                     
{: #Fig--Archi2 title='3GPP optimized CIOT network architecture'}
-->

# Data Transmission

NB-IoT networks deal with end-to-end user data and in-band signaling between the nodes and functions to configure, control, and monitor the system functions and behaviors. The signaling data uses a different path with specific protocols, handling processes, and entities but can transport end-to-end user data for IoT services. In contrast, the end-to-end application only transports end-to-end data. 

The maximum recommended MTU size is 1358 Bytes. The radio network protocols limit the packet sizes over the air, including radio protocol overhead to 1600 Octets. However, the MTU is smaller to avoid fragmentation in the network backbone due to the payload encryption size (multiple of 16) and the additional core transport overhead handling.

3GPP standardizes NB-IoT and, in general, the cellular technologies interfaces and functions. Therefore the introduction of SCHC entities to Dev, RGW-eNB, and NGW-CSGN needs to be specified in the NB-IoT standard, which implies that standard specified SCHC support would not be backward compatible. A terminal or a network supporting a version of the standard without SCHC or without capability implementation (in case of not being standardized as mandatory capability) cannot utilize the compression services with this approach.

SCHC could be deployed differently depending on where the header compression and the fragmentation are applied. The SCHC functionalities can be used over the radio transmission only, between the Dev and the RGW-eNB. Alternatively, the packets transmitted over the end-to-end link can use SCHC. Else, when the transmissions over the NGW-MME or NGW-SCEF, the NGW-CSGN uses SCHC entity. For these two cases, the functions are to be standardized by 3GPP.

Another possibility is to apply SCHC functionalities to the end-to-end connection or at least up to the operator network edge. SCHC functionalities are available in the application layer of the Dev and the Application Servers or a broker function at the edge of the operator network. The radio network transmits the packets as non-IP traffic using IP tunneling or SCEF services. Since this option does not necessarily require 3GPP standardization, it is possible to also benefit legacy devices with SCHC by utilizing the non-IP transmission features of the operator network.

# IP based Data Transmission

## SCHC over the Radio link 															  
Deploying SCHC only over the radio link would require placing it as part of the protocol stack for data transfer. This stack is the functional layer responsible for transporting data over the wireless connection and managing radio resources. There is support for features such as reliability, segmentation, and concatenation. The transmissions use link adaptation, meaning that the system will optimize the transport format used according to the radio conditions, the number of bits to transmit, and the power and interference constraints. That means that the number of bits transmitted over the air depends on the Modulation and Coding Schemes (MCS) selected. A Transport Block (TB) transmissions happen in the physical layer at network synchronized intervals called Transmission Time Interval (TTI). Each Transport Block has a different MCS and number of bits available to transmit. The MAC layer [TGPP36321] defines the Transport Blocks characteristics. The Radio link stack comprises the Packet Data Convergence Protocol (PDCP) [TGPP36323], Radio Link Protocol (RLC) [TGPP36322], Medium Access Control protocol (MAC) [TGPP36321], and the Physical Layer [TGPP36201]. The Appendix gives more details of these protocols.

### SCHC Entities Placing
The current architecture provides support for header compression in PDCP with RoHC {{RFC5795}}. Therefore SCHC entities can be deployed similarly without the need for significant changes in the 3GPP specifications.

In this scenario, RLC takes care of fragmentation unless for the transparent mode. When packets exceed the transport block size at the time of transmission, SCHC fragmentation is unnecessary and should not be used to avoid the additional protocol overhead. It is not common to configure RLC in Transparent Mode for IP-based data. However, given the case in the future, SCHC fragmentation may be used. In that case, an SCHC tile would match the minimum transport block size minus the PDCP and MAC headers.

~~~~~~

  +---------+                              +---------+  |
  |IP/non-IP+------------------------------+IP/non-IP+->+
  +---------+   |   +---------------+   |  +---------+  |
  | PDCP    +-------+ PDCP  | GTP|U +------+ GTP-U   |->+
  | (SCHC)  +       + (SCHC)|       +      +         |  |           
  +---------+   |   +---------------+   |  +---------+  |
  | RLC     +-------+ RLC   |UDP/IP +------+ UDP/IP  +->+
  +---------+   |   +---------------+   |  +---------+  |
  | MAC     +-------+ MAC   | L2    +------+ L2      +->+
  +---------+   |   +---------------+   |  +---------+  |
  | PHY     +-------+ PHY   | PHY   +------+ PHY     +->+
  +---------+       +---------------+      +---------+  |
               C-Uu/                    S1-U             SGi
     CIOT/     LTE+Uu     C-BS/eNB             C-SGN
    LTE eMTC
      UE
       
~~~~~~
{: #Fig--ProtocolArchi3 title='SCHC entities placement in the 3GPP CIOT radio protocol architecture for data over user plane'} 

## Data Over Control Plane
The Non-Access Stratum (NAS), conveys mainly control signaling between the UE and the cellular network TGPP24301. NAS is transported on top of the Access Stratum (AS) already mentioned in the previous section.

NAS has been adapted to provide support for user plane data transmissions to reduce the overhead when transmitting infrequent small quantities of data. This is known as Data over NAS (DoNAS) or Control Plane CIoT EPS optimization. In DoNAS the UE makes use of the pre-established NAS security and piggyback uplink small data into the initial NAS uplink message, and uses an additional NAS message to receive downlink small data response.

The data encryption from the network side is performed by the C-SGN in a NAS PDU. Depending on the data type signaled indication (IP or non-IP data), the network allocates an IP address or just establish a direct forwarding path. 
DoNAS (Data over NAS)  is regulated under rate control upon previous agreement, meaning that a maximum number of bits per unit of time is agreed per device subscription beforehand and configured in the device. 

The use of DoNAS is typically expected when a terminal in a power saving state requires to do a short transmission and receive an acknowledgment or short feedback from the network. Depending on the size of buffered data to transmit, the UE might be instructed to deploy the connected mode transmissions instead, limiting and controlling the DoNAS transmissions to predefined thresholds and a good resource optimization balance for the terminal and the network. The support for mobility of DoNAS is present but produces additional overhead. Additional details of DoNAS are given in the Appendix.

### SCHC Entities Placing
In this scenario SCHC can be applied in the NAS protocol layer instead of PDCP. The same principles than for user plane transmissions applies here as well. The main difference is the physical placing of the SCHC entities in the network side as the C-SGN (placed in the core network) is the terminating node for NAS instead of the eNB.  

~~~~~~		  										
+--------+                       +--------+--------+  +  +--------+
| IP/    +--+-----------------+--+  IP/   |   IP/  +-----+   IP/  |
| Non-IP |  |                 |  | Non-IP | Non-IP |  |  | Non-IP |
+--------+  |                 |  +-----------------+  |  +--------+
| NAS    +-----------------------+   NAS  |GTP|C/U +-----+GTP|C/U |
|(SCHC)  |  |                 |  | (SCHC) |        |  |  |        |
+--------+  |  +-----------+  |  +-----------------+  |  +--------+
| RRC    +-----+RRC  |S1|AP+-----+ S1|AP  |        |  |  |        |
+--------+  |  +-----------+  |  +--------+  UDP   +-----+  UDP   |
| PDCP*  +-----+PDCP*|SCTP +-----+ SCTP   |        |  |  |        |
+--------+  |  +-----------+  |  +-----------------+  |  +--------+
| RLC    +-----+ RLC | IP  +-----+ IP     | IP     +-----+ IP     |
+--------+  |  +-----------+  |  +-----------------+  |  +--------+
| MAC    +-----+ MAC | L2  +-----+ L2     | L2     +-----+ L2     |
+--------+  |  +-----------+  |  +-----------------+  |  +--------+
| PHY    +--+--+ PHY | PHY +--+--+ PHY    | PHY    +-----+ PHY    |
+--------+     +-----+-----+     +--------+--------+  |  +--------+
           C-Uu/           S1-lite                   SGi
   CIOT/  LTE-Uu   C-BS/eNB            C-SGN                PGW
 LTE eMTC
   UE

    *PDCP is bypassed until AS security is activated TGPP36300. 
    
~~~~~~
{: #Fig--ProtocolArchi4 title= 'SCHC entities placement in the 3GPP CIOT radio protocol architecture for DoNAS transmissions'}  
		 

## Parameters for Static Context Header Compression (SCHC)

### SCHC Context initialization
RRC (Radio Resource Control) protocol is the main tool used to configure the operation parameters of the AS transmissions for 3GPP technologies. RoHC operation is configured with this protocol and it is to expect that SCHC will be configured and the static context distributed in similar fashion for these scenarios.
 

### SCHC Rules
The number of rules in a context are defined by the network operator in these scenarios. For this, the operator must be aware of the type of IP traffic that the device will carry out. This means that the operator might provision sets of rules compatible with the use case of the device. For devices acting as gateways of other devices several rules that match the diversity of devices and protocols used by the devices associated to the gateway. Meanwhile than simpler devices (for example an electricity meter) may have a predetermined set of protocols and parameters fixed. Additionally, the deployment of IPV4 addresses in addition to IPV6 may force to provision separate rules to deal with each of the cases.

### Rule ID 
For these transmission scenarios in NB-IoT, a reasonable assumption of 9 bytes of radio protocol overhead can be expected. PDCP 5 bytes due to  header and integrity protection, and 4 bytes of RLC and MAC. The minimum physical Transport Block (TB) that can withhold this overhead value according to 3GPP Release 15 specifications are: 88, 104, 120 and 144 bits. If it is wished to optimize the number of transmissions of a very small application packet so that in some cases can be transmitted using only one physical layer transmission, then the SCHC overhead should not exceed the available number of bits of the smallest utile physical TB available. The packets handled by 3GPP networks are byte-aligned, and therefore the minimum payload possible (including padding) is 8 bits. Therefore in order to utilize the smallest TB the maximum SCHC is 8 bits.
This must include the Compression Residue in addition to the Rule ID. In the other hand, it is possible that more complex NB-IoT devices (such as a capillarity gateway) might require additional bits to handle the variety and multiple parameters the of higher layer protocols deployed. In that sense, the operator may want to have flexibility on the number and type of rules supported by each device independently, and consequently a configurable value is preferred for these scenarios. The configuration may be set as part of the operation profile agreed together with the context distribution. The Rule Id field size may range for example from  2 bits resulting in  4 rules to a 8 bits value that would yield up to 256 rules which can be used together with the operators and seems quite a reasonable maximum limit even for a device acting as a NAT. More bits could be configured, but it should take in account the byte-alignment of the expected Compression Residue too. In the minimum TB size case, 2 bits size of Rule Id leave only 6 bits available for Compression Residue. 

<!-- The Rule ID the SCHC identifies are:

* In the SCHC C/D context the Rule used to keep the Field Description of the header packet. 

* In SCHC Fragmentation the specific modes and settings.     
(No need to discuss fragmentation. Since it is not needed in these scenarios )-->	


### SCHC MAX_PACKET_SIZE
The Access Stratum can handle the fragmentation of SCHC packets if needed including reliability. Hence the packet size is limited by the MTU possible to be handled by the AS radio protocols that corresponds to 1600 bytes for 3GPP Release 15.

### Fragmentation
For these scenarios the SCHC fragmentation functions are recommend to be disabled. The RLC layer of NB-IoT can segment packets in suitable units that fit the selected transport blocks for transmissions of the physical layer. The selection of the blocks is done according to the input of the link adaptation function in the MAC layer and the quantity of data in the buffer. The link adaptation layer may produce different results at each Time Transmission Interval (TTI) resulting in varying physical transport blocks that depends of the network load, interference and number of bits to be transmitted and QoS. Even if setting a value that allows the construction of data units following SCHC tiles principle, the protocol overhead may be greater or equal than allowing the AS radio protocols to take care of the fragmentation natively. 
   
#### Fragmentation in Transparent Mode
If RLC is configured to operate in Transparent Mode, there could be a case to activate a fragmentation function together with a light reliability function such as the ACK-Always mode. In practice , it is very rare to transmit user plane data using this configuration and it is mainly targeting control plane transmissions. In those cases the reliability is normally ensured by MAC based mechanisms, such as repetitions or automatic retransmissions, and additional reliability might only generate protocol overhead.

In future operations, it could be devised the utilization of SCHC to reduce radio network protocols overhead and support the reliability of the transmissions, and targeting small data with the fewer possible transmissions.  This could be realized by using fixed or limited set of transport blocks compatible with the tiling SCHC fragmentation handling.

# Non-IP based Data Transmission
The Non-IP Data Delivery (NIDD) services of 3GPP enable the possibility of transmitting SCHC packets compressed by the application layer. The packets can be delivered by means of IP-tunnels to the 3GPP network or using SCEF functions (i.e., API calls). In both cases the packet IP is not understood by the 3GPP network since it is already compressed and the network does not has information of the context used for compression. Therefore the network will treat the packet as a Non-IP traffic and deliver it to the UE without any other stack element, directly under the L2.

## SCHC Entities Placing
In the two scenarios using NIDD, SCHC entities are located almost in top of the stack. In the terminal, it may be implemented by a application utilizing the NB-IoT connectivity services. In the network side, the SCHC entities are located in the Application Server (AS). The IP tunneling scenario requires that the Application Server sends the compressed packet over an IP connection that is terminated by the 3GPP core network. If instead the SCEF services are used, then it is possible to utilize a API call to transfer the SCHC packets between the core network and the AS, also an IP tunnel could be established by the AS, if negotiated with the SCEF.

~~~~~~						   

+---------+       XXXXXXXXXXXXXXXXXXXXXXXX             +--------+
| SCHC    |      XXX                    XXX            | SCHC   |
|(Non-IP) +-----XX........................XX....+--*---+(Non-IP)|
+---------+    XX                  +----+  XX   |  |   +--------+
|         |    XX                  |SCEF+-------+  |   |        |
|         |   XXX     3GPP RAN &   +----+  XXX     +---+  UDP   |
|         |   XXX    CORE NETWORK          XXX     |   |        |
|  L2     +---+XX                  +------------+  |   +--------+
|         |     XX                 |IP TUNNELING+--+   |        |
|         |      XXX               +------------+  +---+  IP    |
+---------+       XXXX                 XXXX        |   +--------+
| PHY     +------+ XXXXXXXXXXXXXXXXXXXXXXX         +---+  PHY   |
+---------+                                            +--------+
   UE                                                       AS
    
~~~~~~
{: #Fig--NIDD title='SCHC entities placed when using Non-IP Delivery (NIDD) 3GPP Sevices'} 


## Parameters for Static Context Header Compression

### SCHC Context initialization
The static context is handled in the application layer level, consequently the contexts are required to be distributed according to the applications own capabilities, perhaps utilizing IP data transmissions up to context initialization. Also the same IP tunneling or SCEF services used later for the SCHC packets transport may be used by the applications in both ends to deliver the static contexts to be used. 
 
### SCHC Rules
Even when the transmissions content are not visible for the 3GPP network, the same limitations than for IP based data transmissions applies in these scenarios in terms of aiming to use the minimum number of transmission and minimize the protocol overhead. 

### Rule ID 
Similarly to the case of IP transmissions, the Rule ID size can be dynamically set prior the context delivery. For example negotiated between the applications when choosing a profile according to the type of traffic and type of application deployed. Same considerations related to the transport block size and performance mentioned for the IP type of traffic has to be follow when choosing a size value for the Rule ID field.
											
### SCHC MAX_PACKET_SIZE
In these scenarios the maximum recommended MTU size that applies is 1358 Bytes, since the SCHC packets (and fragments) are traversing the whole 3GPP network infrastructure (core and radio), and not only the radio as the IP transmissions case.

## Fragmentation
In principle the fragmentation function should be activated for packets greater than 1358 Bytes. Since the 3GPP reliability functions take great deal care of it, for simple point to point connections may be enough a NO-ACK mode. Nevertheless additional considerations for more complex cases are mentioned in the next subsection to be taken in account.

### Fragmentation modes
Depending of the QoS that has been assigned to the packets, it is possible that packets are lost before they arrive to 3GPP radio network transmission, for example in between the links of a capillarity gateway, or due to buffer overflow handling in a backhaul connection. 
In consequence, it is possible to secure additional reliability on the packets transmitted with a small trade-off on additional transmissions to signal the packets arrival indication end-to-end if no transport protocol takes care of retransmission.  To achieve this, the packets fragmentation is activated with the ACK-on-Error mode enabled.
In some cases, it is even desirable to keep track of all the SCHC packets delivered, in that case, the fragmentation function could be active for all packets transmitted by the applications (SCHC MAX_PACKET_SIZE == 1 Byte) and the ACK-on-Error mode.
In the NAS stratum, the use of only fragmentation when a non-IP packet is transmitted is possible if this packet is considered as a SCHC packet and is identifyed using the RuleID for non-compressing packets as {RFC8724} allows it, depending on the application an ACK-onError mode may be used.

### Fragmentation Parameters
The Fragmentation Rule ID is given when choosing the profile according to the fragmentation mode, 1 bit can be used to recognize each mode. 

To adapt SCHC to the NB-IoT constraints, two configuration are proposed to fill the best the transfer block (TB). The Header size needs to be multiple of 4 and the Tiles can keep a fix value of 4 or 8 bits to avoid the need of padding. 

* 8 bits-Header_size configuration, with the size of the header fields as follow: Rule ID 3 bits, DTag 1 bit, FCN 3 bits, W 1 bits. This configuration may ne used with TB less than 300 bits. 
* 16 bits-Header_size configuration, with the size of the header fields as follow: Rules ID 8 - 10 bits, DTag 1 or 2 bits, FCN 3 bits, W 2 or 3 bits. This configuration may be used with TB above 300 bits.

The IoT devices communicates with small data transfer and have a battery life of 10 years. To minise the power consumption these devices use the Power Save Mode and the Idle Mode DRX which govern how often the device wakes up, stays up and are reachable. The Table 10.5.163a in {3GPP-TS_24.088} specifies a range for the radio timers as N to 3N in increments of one where the units of N can be 1 hour or 10 hours. To adapt SCHC to the NB-IoT activities, the Innactivity Timer may be above 1h or 10h and the Retransmission Timer may be below than 1h or 10h.

# Padding
NB-IoT and 3GPP wireless access, in general, assumes byte aligned payload. Therefore the L2 word for NB-IoT MUST be considered 8 bits and the treatment of padding should use this value accordingly.

# Security considerations
3GPP access security is specified in (TGPP33203).

# 3GPP References

* TGPP23720   3GPP, "TR 23.720 v13.0.0 - Study on architecture enhancements for Cellular Internet of Things", 2016.              
* TGPP33203   3GPP, "TS 33.203 v13.1.0 - 3G security; Access security for IP-based services", 2016.              
* TGPP36321   3GPP, "TS 36.321 v13.2.0 - Evolved Universal Terrestrial Radio Access (E-UTRA); Medium Access Control (MAC) protocol specification", 2016
* TGPP36323   3GPP, "TS 36.323 v13.2.0 - Evolved Universal Terrestrial Radio Access (E-UTRA); Packet Data  Convergence Protocol (PDCP) specification", 2016.
* TGPP36331   3GPP, "TS 36.331 v13.2.0 - Evolved Universal Terrestrial Radio Access (E-UTRA); Radio Resource Control (RRC); Protocol specification", 2016.
* TGPP36300   3GPP, "TS 36.300 v15.1.0 - Evolved Universal Terrestrial Radio Access (E-UTRA) and Evolved Universal Terrestrial Radio Access Network (E-UTRAN); Overall description; Stage 2", 2018
* TGPP24301   3GPP "TS 24.301 v15.2.0 - Non-Access-Stratum (NAS) protocol for Evolved Packet System (EPS); Stage 3", 2018 
* TGPP24088   3GPP, "TS 24.088 v12.9.0 - Mobile radio interface Layer 3 specification;Core network protocols; Stage 3", 2015.

# Appendix

## NB-IoT User Plane protocol architecture

### Packet Data Convergence Protocol (PDCP)
Each of the Radio Bearers (RB) are associated with one PDCP entity. And a PDCP entity is associated with one or two RLC entities depending of the unidirectional or bi-directional characteristics of the RB and RLC mode used. A PDCP entity is associated either control plane or user plane which independent configuration and functions. The maximum supported size for NB-IoT of a PDCP SDU is 1600 octets. The main services and functions of the PDCP sublayer for NB-IoT for the user plane include:

* Header compression and decompression by means of ROHC (Robust Header Compression)
* Transfer of user and control data to higher and lower layers
* Duplicate detection of lower layer SDUs when re-establishing connection (when RLC with Acknowledge Mode in use for User Plane only)
* Ciphering and deciphering
* Timer-based SDU discard in uplink

### Radio Link Protocol (RLC)
RLC is a layer-2 protocol that operates between the UE and the base station (eNB). It supports the packet delivery from higher layers to MAC creating packets that are transmitted over the air optimizing the Transport Block utilization. RLC flow of data packets is unidirectional and it is composed of a transmitter located in the transmission device and a receiver located in the destination device. Therefore to configure bi-directional flows, two set of entities, one in each direction (downlink and uplink) must be configured and they are effectively peered to each other. The peering allows the transmission of control packets (ex., status reports) between entities. RLC can be configured for data transfer in one of the following modes:

* Transparent Mode (TM). In this mode RLC do not segment or concatenate SDUs from higher layers and do not include any header to the payload. When acting as a transmitter, RLC receives SDUs from upper layers and transmit directly to its flow RLC receiver via lower layers. Similarly, an TM RLC receiver would only deliver without additional processing the packets to higher layers upon reception.

* Unacknowledged Mode (UM). This mode provides support for segmentation and concatenation of payload. The size of the RLC packet depends of the indication given at a particular transmission opportunity by the lower layer (MAC) and are octets aligned. The packet delivery to the receiver do not include support for reliability and the lost of a segment from a packet means a whole packet loss. Also in case of lower layer retransmissions there is no support for re-segmentation in case of change of the radio conditions triggering the selection of a smaller transport block. Additionally it provides PDU duplication detection and discard, reordering of out of sequence and loss detection.
 
* Acknowledged Mode (AM). Additional to the same functions supported from UM, this mode also adds a moving windows based reliability service on top of the lower layer services. It also provides support for re-segmentation and it requires bidirectional communication to exchange acknowledgment reports called RLC Status Report and trigger retransmissions is needed. Protocol error detection is also supported by this mode. The mode uses depends of the operator configuration for the type of data to be transmitted. For example, data transmissions supporting mobility or requiring high reliability would be most likely configured using AM, meanwhile streaming and real time data would be map to a UM configuration.

### Medium Access Control (MAC)
MAC provides a mapping between the higher layers abstraction called Logical Channels comprised by the previously described protocols to the Physical layer channels (transport channels). Additionally, MAC may multiplex packets from different Logical Channels and prioritize what to fit into one Transport Block if there is data and space available to maximize the efficiency of data transmission. MAC also provides error correction and reliability support by means of HARQ, transport format selection and scheduling information reporting from the terminal to the network. MAC also adds the necessary padding and piggyback control elements when possible additional to the higher layers data.

~~~~~~

                                            <Max. 1600 bytes>
                    +---+         +---+           +------+
Application         |AP1|         |AP1|           |  AP2 |
(IP/non-IP)         |PDU|         |PDU|           |  PDU |  
                    +---+         +---+           +------+
                    |   |         |   |           |      |
PDCP           +--------+    +--------+      +-----------+
               |PDCP|AP1|    |PDCP|AP1|      |PDCP|  AP2 |
               |Head|PDU|    |Head|PDU|      |Head|  PDU |
               +--------+    +--------+      +--------+--\
               |    |   |    |     |  |      |    |   |\  `----\
         +---------------------------+      |    |(1)| `-----\(2)'-\
RLC      |RLC |PDCP|AP1|RLC |PDCP|AP1| +-------------+    +----|---+
         |Head|Head|PDU|Head|Head|PDU| |RLC |PDCP|AP2|    |RLC |AP2|
         +-------------|-------------+ |Head|Head|PDU|    |Head|PDU|
         |         |   |         |   | +---------|---+    +--------+
         |         |   | LCID1   |   | /         /   /   /         /
        /         /   /        _/  _//        _/  _/    / LCID2   /
        |        |   |        |   | /       _/  _/     /      ___/
        |        |   |        |   ||       |   |      /      /   
    +------------------------------------------+ +-----------+---+
MAC |MAC|RLC|PDCP|AP1|RLC|PDCP|AP1|RLC|PDCP|AP2| |MAC|RLC|AP2|Pad|
    |Hea|Hea|Hea |PDU|Hea|Hea |PDU|Hea|Hea |PDU| |Hea|Hea|PDU|din|
    |der|der|der |   |der|der |   |der|der |   | |der|der|   |g  |
    +------------------------------------------+ +-----------+---+
                      TB1                               TB2
                                  
~~~~~~
{: #Fig--MAC title='Example of User Plane packet encapsulation for two transport blocks'} 

## NB-IoT Data over NAS (DoNAS)
The AS protocol stack used by DoNAS is somehow special. Since the security associations are not established yet in the radio network, to reduce the protocol overhead, PDCP (Packet Data Convergence Protocol) is bypassed until AS security is activated. RLC (Radio Link Control protocol) is configured by default in AM mode, but depending of the features supported by the network and the terminal it may be configured in other modes by the network operator. For example, the transparent mode does not add any header or does not process the payload in any way reducing the overhead, but the MTU would be limited by the transport block used to transmit the data which is couple of thousand of bits maximum. If UM (only Release 15 compatible terminals) is used, the RLC mechanisms of reliability is disabled and only the reliability provided by the MAC layer by Hybrid Automatic Repeat reQuest (HARQ) is available. In this case, the protocol overhead might be smaller than for the AM case because the lack of status reporting but with the same support for segmentation up to 16000 Bytes. NAS packet are encapsulated within a RRC (Radio Resource Control) TGPP36331 message.

Depending of the data type indication signaled (IP or non-IP data), the network allocates an IP address or just establish a direct forwarding path. DoNAS is regulated under rate control upon previous agreement, meaning that a maximum number of bits per unit of time is agreed per device subscription beforehand and configured in the device. The use of DoNAS is typically expected when a terminal in a power saving state requires to do a short transmission and receive an acknowledgment or short feedback from the network. Depending of the size of buffered data to transmit, the UE might be instructed to deploy the connected mode transmissions instead, limiting and controlling the DoNAS transmissions to predefined thresholds and a good resource optimization balance for the terminal and the network. The support for mobility of DoNAS is present but produces additional overhead.

~~~~~~                                                                                                                       

    +--------+   +--------+   +--------+
    |        |   |        |   |        |       +-----------------+
    |   UE   |   |  C-BS  |   |  C-SGN |       |Roaming Scenarios|
    +----|---+   +--------+   +--------+       |  +--------+     | 
         |            |            |           |  |        |     |
     +----------------|------------|+          |  |  P-GW  |     | 
     |        Attach                |          |  +--------+     | 
     +------------------------------+          |       |         | 
         |            |            |           |       |         | 
  +------|------------|--------+   |           |       |         |  
  |RRC Connection Establishment|   |           |       |         | 
  |with NAS PDU transmission   |   |           |       |         | 
  |& Ack Rsp                   |   |           |       |         | 
  +----------------------------+   |           |       |         | 
         |            |            |           |       |         | 
         |            |Initial UE  |           |       |         | 
         |            |message     |           |       |         | 
         |            |----------->|           |       |         | 
         |            |            |           |       |         | 
         |            | +---------------------+|       |         |
         |            | |Checks Integrity     ||       |         | 
         |            | |protection, decrypts ||       |         |
         |            | |data                 ||       |         |
         |            | +---------------------+|       |         | 
         |            |            |       Small data packet     | 
         |            |            |-------------------------------> 
         |            |            |       Small data packet     |
         |            |            |<-------------------------------
         |            | +----------|---------+ |       |         |
         |            | Integrity protection,| |       |         |
         |            | encrypts data        | |       |         | 
         |            | +--------------------+ |       |         | 
         |            |            |           |       |         | 
         |            |Downlink NAS|           |       |         | 
         |            |message     |           |       |         | 
         |            |<-----------|           |       |         | 
 +-----------------------+         |           |       |         | 
 |Small Data Delivery,   |         |           |       |         | 
 |RRC connection release |         |           |       |         | 
 +-----------------------+         |           |       |         | 
                                               |                 | 
                                               |                 | 
                                               +-----------------+  

~~~~~~
{: #Fig--DONAS title='DoNAS transmission sequence from an Uplink initiated access'} 

~~~~~~ 







                                                                                                                
                   +---+ +---+ +---+                  +----+ 
 Application       |AP1| |AP1| |AP2|                  |AP2 | 
(IP/non-IP)        |PDU| |PDU| |PDU|  ............... |PDU | 
                   +---+ +---+ +---+                  +----+ 
                   |   |/   /  |    \                 |    | 
NAS /RRC      +--------+---|---+----+            +---------+ 
              |NAS/|AP1|AP1|AP2|NAS/|            |NAS/|AP2 | 
              |RRC |PDU|PDU|PDU|RRC |            |RRC |PDU |
              +--------+-|-+---+----+            +---------|
              |          |\         |            |         |  
              |<--Max. 1600 bytes-->|__          |_        |
              |          |  \__        \___        \_       \_            
              |          |     \           \         \__      \_
         +---------------|+-----|----------+             \      \
RLC      |RLC | NAS/RRC  ||RLC  | NAS/RRC  |       +----|-------+ 
         |Head|  PDU(1/2)||Head | PDU (2/2)|       |RLC |NAS/RRC| 
         +---------------++----------------+       |Head|PDU    | 
         |    |          | \               |       +------------+  
         |    |    LCID1 |  \              |       |           / 
         |    |          |   \              \      |           |
         |    |          |    \              \     |           |
         |    |          |     \              \     \          |
    +----+----+----------++-----|----+---------++----+---------|---+ 
MAC |MAC |RLC |    RLC   ||MAC  |RLC |  RLC    ||MAC |  RLC    |Pad| 
    |Head|Head|  PAYLOAD ||Head |Head| PAYLOAD ||Head|  PDU    |   |
    +----+----+----------++-----+----+---------++----+---------+---+
             TB1                   TB2                     TB3           

~~~~~~
{: #Fig--ProtocolArchi5 title='Example of User Plane packet encapsulation for Data over NAS'} 


--- back


