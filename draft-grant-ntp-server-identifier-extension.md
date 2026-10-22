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

This document defines an extension field that allows operators of NTP services the ability to provide additional information about their servers to clients which request it.

--- middle

# Introduction

Operators of NTP services may choose to have system architectures which result in multiple servers responding for the same IP address. This is particularly notable in the case of deployments which use load balancing of UDP traffic, or the use of anycast IP addresses. In such situations, the server operator may want to provide an indication which actual server responded to a request. This information can be useful in identifying infrastructure, providing ongoing monitoring and assist in triaging faults or issues with services.

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

: Length of the Server Identifier field. The length is in octets expressed as an unsigned 16-bit integer and it includes the header itself. Implementations SHALL keep the length of this extension field at less than 64 bytes.

Server Identifier:

: A UTF-8 encoded Unicode string containing information about the server. In requests, this field shall be filled with zeroes. The length of this field should be chosen such that the length requirements on extension fields from the NTP version in use are satisfied. If the string is shorter than the field, the sender SHALL pad the remainder of the field with zeroes. The identifier ends at the first zero octet or at the end of the field, whichever comes first; receivers MUST ignore any octets following the first zero octet.

## Use of the Server Identifier Extension Field {#use}

To request the server identifier of a server, a client includes in its request a Server Identifier Extension Field. This extension field shall be sent with a zeroed out server-identifier field, of length sufficient that the client expects the servers identifier to fit within the length of the server's identifier field in its request.

On receiving a Server Identifier Extension Field a server MAY choose to send its server identifier in the response. If it chooses to do so, it shall include a Server Identifier Extension Field in the response. The length of this Extension field SHALL be identical to the length of the Server Identifier Extension Field in the request. If the server's identifier doesn't fit within the length requested by the client to the longest prefix that fits and that ends on a UTF-8 character boundary. If the identifier is shorter than the field, the server SHALL pad with zeroes.

# Security Considerations

Operators should note that NTP packets are not confidential and that revealing this information to clients may expose sensitive details about their network, services, or their configuration. Deployments not wishing to expose this data to clients should be configured to filter or ignore this extension on non-monitoring interfaces.

As the extension field in a response is always the same length as the corrosponding request ({{use}}), this extension does not expose an amplification risk to the use of NTP, provided that servers MUST NOT respond with a field longer than the one sent in a given request.

The identifier sent in responses doesn't depend on the requested length, so varying the request length discloses no additional information. Operators concerned about enumeration of servers should consider limiting the extension to trusted clients.

# IANA Considerations

IANA is requested to allocate the following entries in the NTP Extension Field Types registry {{RFC5905}}:

Field Type | Meaning           | Reference
-----------|-------------------|-----------------
   TBD     | Server Identifier | this memo

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge Marco Davids, Miroslav Lichvar, Giovane Moura, Ruben Nijveld, and Ask Bjørn Hansen for providing thoughtful discussion and inspiration for this document.
