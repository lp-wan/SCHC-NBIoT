---
stand_alone: true
ipr: trust200902
docname: draft-minaburo-lpwan-nbiot-hc-00
cat: info
pi:
  symrefs: 'yes'
  sortrefs: 'yes'
  strict: 'yes'
  compact: 'yes'
  toc: 'yes'
title: LPWAN Static Context Header Compression (SCHC) over NB-IoT
abbrev: SCHC NB-IoT
wg: lpwan Working Group
author:
- ins: A. Minaburo
  name: Ana Minaburo
  org: Acklio
  street: 2bis rue de la Chataigneraie
  city: 35510 Cesson-Sevigne Cedex
  country: France
  email: ana@ackl.io
- ins: E. Ramos
  name: Edgar Ramos
  org: Ericsson 
  street:
  - 2 rue 
  - CS 
  city: 35576 Cgne 
  country: F
  email: Edgar.Ramos@ericsson.com
normative:
  RFC4944: 
  RFC2460: 
  RFC3385:
  RFC7136:
  RFC5795:
  RFC7217:
informative:  
  I-D.ietf-lpwan-overview:
  I-D.ietf-lpwan-schc:
  
  
--- abstract

The Static Context Header Compression (SCHC) specification describes
a header compression and fragmentation functionalities for LPWAN
(Low Power Wide Area Networks) technologies.  SCHC was designed to be adapted
over any of the LPWAN technologies.

This document describes the use of SCHC over the NB-IoT channels, 
and provides elements for an efficient parameterization.

--- middle
 
# Introduction {#Introduction}

The Static Context Header Compression (SCHC) {{I-D.ietf-lpwan-ipv6--static-context-hc}} defines a header compression scheme 
and fragmentation functionality, both specially tailored for Low Power Wide Area Networks (LPWAN) networks defined in 
{{I-D.ietf-lpwan-overview}}.  

Header compression is needed to efficiently bring Internet connectivity to the node
within an NB-IoT network. SCHC uses an static context to performs header compression with specific parameters that need to be adapted into the NB-IoT channels. 

This document describes the use of SCHC and its parametrizing over the NB-IoT channels.


# Terminology

This document will follow the terms defined in {{I-D.ietf-lpwan-ipv6--static-context-hc}} and in {{I-D.ietf-lpwan-overview}}

# Architecture

## SCHC entities

## NB-IoT Channels
(Rule ID on L2)

# Static Context Header Compression

## SCHC Rules
### Rule ID 
The Rule ID the SCHC identifies are:
* In the SCHC C/D context the Rule used to keep the Field Description of the header packet. 

* In SCHC Fragmentation the specific modes and settings.

* And at least one Rule ID may be reserved to the case where no SCHC C/D nor SCHC fragmentation were possible.


## Packet processing
## SCHC Context

# Fragmentation
## Fragmentation Headers
## Fragmentation modes
## Fragmentation Parameters
Rule ID
DTag
FCN
Retransmission Timer
Inactivity Timer
MAX_ACK_Retries
MAX_ATTEMPS

# Padding

# Security considerations

# Acknowledgements

Thanks to . 

--- back

