---
title: "NTP Server Identifier Extension"
abbrev: "NTP Server Identifier"
category: info

docname: draft-grant-ntp-server-identifier-extension-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "Internet"
workgroup: "Network Time Protocols"
venue:
  group: "Network Time Protocols"
  type: "Working Group"
  mail: "ntp@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/ntp/"
  github: "signalsforgranted/draft-grant-ntp-server-identifier-extension"

author:
 -
    fullname: Sarah Grant
    email: "sarah.grant.ietf@gmail.com"
 -
    fullname: David Venhoek
    email: "david@venhoek.nl"

normative:
    RFC5905:

informative:

...

--- abstract

This document defines an extension field that allows operators of NTP services the ability to provide additional information about their services to clients which request it.

--- middle

# Introduction

Operators of NTP services may choose to have system architectures which lead. This is particularly notable in the case of deployments which use load balancing of UDP traffic, or the use of anycast IP addresses. This information can be useful in identifying infrastructure, providing ongoing monitoring and assist in triaging faults or issues with services.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Current Deployments

Many operators use various methods to include diagnostic information about the network segment, datacentre location, region, or other details pertaining to their infrastructure in NTP responses with the NTP reference identifier most commonly being used.

# The Server Identifier Extension Field

The Server Identifier field contains the following structure, based on the general structure of an NTP extension field defined in {{RFC5905}}:

~~~
 0                   1                   2                   3
 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1 2 3 4 5 6 7 8 9 0 1
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|       Type = TBD              |             Length            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
.                                                               .
.                      Server Identifier                        .
.                                                               .
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
~~~
{: #fig-extension-field-diagram title="Extension Field Structure"}

Field Type:

: The type which identifies the Server Identifier extension field. To be determined by IANA. (Draft implementations: **0xf101**)

Length:

: Length of the Server Identifier field. The length is in octets expressed as an unsigned 16-bit integer and it includes the header itself. Implementations SHALL keep the length of this extension field at les than 256 bytes.

Server Identifier:

: A Unicode string containing information about the server. In requests, this field shall be filled with zeroes. The length of this field SHALL be chosen such that the length requirements on extension fields from the NTP version in use are satisfied. If doing this requires padding of the string, the sender shall use zeroes to pad this field to the required length.

## Use of the Server Identifier Extension Field

To request the server identifier of a server, a client includes in its request a Server Identifier Extension Field. This extension field shall be sent with a zeroed out server-identifier field, of length sufficient that the client expects the servers identifier to fit within the length of the server identifier field in its request.

On receiving a Server Identifier Extension Field a server MAY choose to send its server identifier in the response. If it chooses to do so, it shall include a Server Identifier Extension Field in the response. The length of this Extension field SHALL be at most the length of the Server Identifier Extension Field in the request. If the servers identifier doesn't fit within the length requested by the client, the server SHALL truncate the identifier, providing as many bytes of it as fit within the space chosen in the request. If the identifier is shorter than the length of Server Identifier field in the Server Identifier Extension Field, the server MAY choose to pad the identifier with zeroes to make the lenght of the request and response identifical.

# Security Considerations

Operators should note that NTP packets are not confidential and that revealing this information to clients may expose sensitive details about their network, services, or their configuration. Deployments not wishing to expose this data to clients should be configured to filter or ignore this extension on non-monitoring interfaces.

TODO: discuss data minimisation to reduce amplification and asymmetry.

# IANA Considerations

IANA is requested to allocate the following entries in the NTP Extension Field Types registry {{RFC5905}}:

Field Type | Meaning           | Reference
-----------|-------------------|-----------------
   TBD     | Server Identifier | this memo

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge Marco Davids, Miroslav Lichvar, Giovane Moura, and Ruben Nijveld for providing thoughtful discussion and inspiration for this document.
