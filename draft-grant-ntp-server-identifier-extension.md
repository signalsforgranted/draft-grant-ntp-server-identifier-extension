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

The Server Identifier field contains the following structure:

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

: The type which identifies the Server Identifier extension field. For this draft, this value should be set to **0xf101**.

Length:

: Length of the Server Identifier field. In requests this field MUST be zero, in responses it should be the true length of the server identifier field.

Server Identifier:

: A Unicode string containing information about the server. In requests, this field MUST be empty. In responses padding is not required.

## Use of the Server Identifier Extension Field

TODO: Describe how implementations and operators should use the field.

# Security Considerations

Operators should remember that NTP packets are not confidential, and that revealing this information to clients may expose sensitive details about their network, services and their configuration. Deployments not wishing to expose this data to clients should be configured to filter or ignore this extension.

# IANA Considerations

IANA is requested to allocate the following entries in the NTP Extension Field Types registry {{RFC5905}}:

Field Type | Meaning           | Reference
-----------|-------------------|-----------------
   TBD     | Server Identifier | this memo

--- back

# Acknowledgments
{:numbered="false"}

The authors would like to acknowledge Marco Davids and the NTP working group
mailing list for inspiration for this document.
