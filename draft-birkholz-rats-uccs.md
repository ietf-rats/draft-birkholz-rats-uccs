---
title: A CBOR Tag for Unprotected CWT Claims Sets
abbrev: Unprotected CWT Claims Sets
docname: draft-birkholz-rats-uccs
stand_alone: true
ipr: trust200902
area: Security
wg: RATS Working Group
kw: Internet-Draft
cat: std
pi:
  toc: yes
  sortrefs: yes
  symrefs: yes

author:
- ins: H. Birkholz
  name: Henk Birkholz
  org: Fraunhofer SIT
  abbrev: Fraunhofer SIT
  email: henk.birkholz@sit.fraunhofer.de
  street: Rheinstrasse 75
  code: '64295'
  city: Darmstadt
- ins: N. Cam-Winget
  name: Nancy Cam-Winget
  org: Cisco Systems
  email: ncamwing@cisco.com
  street: 3550 Cisco Way
  code: '95134'
  city: San Jose
  region: CA
  country: USA
- ins: C. Bormann
  name: Carsten Bormann
  org: Universitaet Bremen TZI
  abbrev: Universitaet Bremen TZI
  email: cabo@tzi.de
  street: Bibliothekstrasse 1
  code: '28369'
  city: Bremen
  country: Germany
  phone: '+49-421-218-63921'

normative:
  RFC7049: cbor
  RFC8152: cose
  RFC8392: cwt
  IANA.cbor-tags: tags
  
--- abstract

CBOR Web Token (CWT, RFC 8392) Claims Sets sometimes do not need the protection afforded by wrapping them into COSE, as is required for a true CWT.  This specification defines a CBOR tag for such unprotected CWT claims sets (UCCS) and discusses conditions for its proper use.

--- middle

# Introduction

A CBOR Web Token (CWT) as specified by {{-cwt}} is always wrapped in a CBOR Object Signing and Encryption (COSE, {{-cose}}) envelope. COSE provides - amongst other things - the integrity protection mandated by RFC 8392 and optional encryption for CWTs. Under the right circumstances, though, a signature providing proof for authenticity and integrity can be omitted from the information in a CWT without compromising the intended goal of authenticity and integrity. If a secure channel is established in an appropriate fashion between two remote peers, and if that secure channel provides the correct properties, it is possible to omit the protection provided by COSE, creating a use case for unprotected CWT Claims Sets.

This specification allocates a CBOR tag to mark Unprotected CWT Claims Sets (UCCS) as such and discusses conditions for its proper use.

This specification does not change {{-cwt}}: A true CWT does not make use of the tag allocated here; the UCCS tag is an alternative to using COSE protection and a CWT tag.

## Terminology

The terms Claim and Claims Set are used as in {{-cwt}}.

UCCS:
: Unprotected CWT Claims Set

{::boilerplate bcp14}

# Characteristics of a Secure Channel {#secchan}

A Secure Channel for the conveyance of UCCS needs to provide the security properties that would otherwise be provided by COSE for a CWT.

Secure Channels are often set up in a handshake protocol that agrees a session key, where the handshake protocol establishes the authenticity of one of both ends of the communication as well as confidentiality.  The session key can then be used to protect confidentiality and integrity of the transfer of information inside the secure channel.  A well-known example of a such a secure channel setup protocol is the TLS {{?RFC8446}} handshake; the TLS record protocol can then be used for secure conveyance.

If only authenticity/integrity is required, the secure channel needs to be set up with authentication of the side that is providing the UCCS.  If confidentiality is also required, the receiving side also needs to be authenticated.

IANA Considerations
============

In the registry {{-tags}},
IANA is requested to allocate the tag in {{tab-tag-values}} from the
FCFS space, with the present document as the specification reference.

| Tag    | Data Item | Semantics                            |
| TBD601 | map       | Unprotected CWT Claims Set [RFCthis] |
{: #tab-tag-values cols='r l l' title="Values for Tags"}


Security Considerations
============

The security considerations of {{-cbor}} and {{-cwt}} apply.

{#secchan} discusses security considerations for secure channels, in which UCCS might be used.

--- back

# Example

The example CWT Claims Set from Appendix A.1 of {{-cwt}} can be turned into an UCCS by enclosing it with a tag number TBD601:

~~~~
 <TBD601>(
   {
     / iss / 1: "coap://as.example.com",
     / sub / 2: "erikw",
     / aud / 3: "coap://light.example.com",
     / exp / 4: 1444064944,
     / nbf / 5: 1443944944,
     / iat / 6: 1443944944,
     / cti / 7: h'0b71'
   }
 )
~~~~
