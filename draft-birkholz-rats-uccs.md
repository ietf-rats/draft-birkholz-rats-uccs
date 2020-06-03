---
title: A CBOR Tag for Unprotected CWT Claims Sets
abbrev: Unprotected CWT Claims Sets
docname: draft-birkholz-rats-uccs-latest
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
  country: Geramy
- ins: J. O'Donoghue
  name: "Jeremy O'Donoghue"
  org: Qualcomm Technologies Inc.
  abbrev: Qualcomm Technologies Inc.
  email: jodonogh@qti.qualcomm.com
  street: 279 Farnborough Road
  code: "GU14 7LS"
  city: Farnborough
  country:  United Kingdom
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
  RFC8725: jwt
  RFC8392: cwt
  RFC8446: tls
  IANA.cbor-tags: tags
  TPM2:
    title: >
      Trusted Platform Module Library Specification, Family “2.0”, Level 00, Revision 01.59 ed.,
      Trusted Computing Group
    date: 2019

informative:
  I-D.ietf-rats-architecture: rats
  I-D.ietf-teep-architecture: teep
  I-D.ietf-rats-eat: eat

--- abstract

CBOR Web Token (CWT, RFC 8392) Claims Sets sometimes do not need the
protection afforded by wrapping them into COSE, as is required for a true
CWT.  This specification defines a CBOR tag for such unprotected CWT
Claims Sets (UCCS) and discusses conditions for its proper use.

--- middle

# Introduction

A CBOR Web Token (CWT) as specified by {{-cwt}} is always wrapped in a
CBOR Object Signing and Encryption (COSE, {{-cose}}) envelope.  COSE
provides -- amongst other things -- the integrity protection mandated by
RFC 8392 and optional encryption for CWTs.  Under the right circumstances,
though, a signature providing proof for authenticity and integrity can be
provided through the transfer protocol and thus omitted from the
information in a CWT without compromising the intended goal of authenticity
and integrity.  If a mutually Secured Channel is established between two
remote peers, and if that Secure Channel provides the correct properties, it
is possible to omit the protection provided by COSE, creating a use case for
unprotected CWT Claims Sets.
Similarly, if there is one-way authentication, the party that did not
authenticate may be in a position to send authentication information through
this channel that allows the already authenticated party to authenticate the
other party.

This specification allocates a CBOR tag to mark Unprotected CWT Claims Sets
(UCCS) as such and discusses conditions for its proper use in the scope of
Remote ATtestation procedureS (RATS).

This specification does not change {{-cwt}}: A true CWT does not make use of
the tag allocated here; the UCCS tag is an alternative to using COSE
protection and a CWT tag.  Consequently, in a well-defined scope, it might
be acceptable to strip a CWT of its COSE container and replace the CWT Claims
Set's CWT CBOR tag with a UCCS CBOR tag for further processing -- or vice
versa.

## Terminology

The term Claim is used as in {{-jwt}}.

The terms Claim Key, Claim Value, and CWT Claims Set are used as in
{{-cwt}}.

UCCS:
: Unprotected CWT Claims Set(s); CBOR map(s) of Claims as defined by the CWT
Claims Registry that are composed of pairs of Claim Keys and Claim Values.

{::boilerplate bcp14}

# Motivation and Requirements
Use cases involving the conveyance of claims, in particular, remote attestations
{{-rats}} require a standardized data definition and encoding format that can be transferred
and transported using different communication channels.  As these are Claims, {{-cwt}} is
a suitable format. However, the way these Claims are secured depends on the deployment, the security
capabilities of the device, as well as their software stack.  For example, a Claim may be securely
stored and conveyed using the device's trusted execution environment or especially in some
resource constrained environments the same process that provides the secure communication
transport is also the delegate to compose the Claim to be conveyed.  Whether it is a transfer
or transport, a Secure Channel is presumed to be used for conveying such UCCS.  The following section
further describes the requirements and scenarios in which UCCS can be used.

{: #secchan}
# Characteristics of a Secure Channel

A Secure Channel for the conveyance of UCCS needs to provide the security
properties that would otherwise be provided by COSE for a CWT.
In this regard, UCCS is similar in security considerations to JWTs {{-jwt}}
using the algorithm "none".  RFC 8725 states: "if a JWT is cryptographically
protected end-to-end by a transport layer, such as TLS using
cryptographically current algorithms, there may be no need to apply another
layer of cryptographic protections to the JWT.  In such cases, the use of
the "none" algorithm can be perfectly acceptable.".  Analogously, the
considerations discussed in Sections 2.1, 3.1, and 3.2 of RFC 8725 apply to
the use of UCCS as elaborated on in this document.

Secure Channels are often set up in a handshake protocol that mutually
derives a session key, where the handshake protocol establishes the
authenticity of one of both ends of the communication.  The session key can
then be used to provide confidentiality and integrity of the transfer of
information inside the Secure Channel.  A well-known example of a such a
Secure Channel setup protocol is the TLS {{-tls}} handshake; the
TLS record protocol can then be used for secure conveyance.

As UCCS were initially created for use in Remote ATtestation procedureS
(RATS) Secure Channels, the following subsection provides a discussion of
their use in these channels.  Where other environments are intended to be
used to convey UCCS, similar considerations need to be documented before
UCCS can be used.

## UCCS and Remote ATtestation procedureS (RATS)

Secure Channels can be transient in nature. For the purposes of this
specification, the mechanisms used to establish a Secure Channel are out of
scope.  As a minimum requirement in the scope of RATS Claims, however, the
Verifier must authenticate the Attester as part of the Secure Channel
establishment.

If only authenticity/integrity for a Claim is required, a Secure Channel MUST
be established to, at minimum, provide integrity of the communication.
Further, the provider of the UCCS SHOULD be authenticated by the receiver to
ensure the channel is truly secured and the sender is validated.
If confidentiality is also required, the receiving side SHOULD also
be authenticated.

The extent to which a Secure Channel can provide assurances that UCCS
originate from a trustworthy attesting environment depends on the
characteristics of both the cryptographic mechanisms used to establish the
channel and the characteristics of the attesting environment itself.
A Secure Channel established or maintained using weak cryptography may not
provide the assurance required by a relying party of the authenticity and
integrity of the UCCS.

Where the security assurance required of an attesting environment by a
relying party requires it, the attesting environment may be implemented
using techniques designed to provide enhanced protection from an attacker
wishing to tamper with or forge UCCS.  A possible approach might be to
implement the attesting environment in a hardened environment such as a
TEE {{-teep}} or a TPM {{TPM2}}.

As with EATs nested in other EATs (Section 3.12.1.2 of {{-eat}}), the Secure
Channel does not endorse fully formed CWTs transferred through it.
Effectively, the COSE envelope of a CWT shields the CWT Claims Set from the
endorsement of the Secure Channel.  (Note that EAT might add a nested UCCS
Claim, and this statement does not apply to UCCS nested into UCCS, only to
fully formed CWTs)

## Privacy Preserving Channels

A Secure Channel which preserves the privacy of the Attester may provide
security properties equivalent to COSE, but only inside the life-span of the
session established.  In general, a Verifier cannot correlate UCCS received
in different sessions from the same attesting environment based on the
cryptographic mechanisms used when a privacy preserving Secure Channel is
employed.

In the case of a Remote Attestation, the attester must consider whether any UCCS it returns over a privacy
preserving Secure Channel compromises the privacy in unacceptable ways.  As
an example, the use of the EAT UEID {{-eat}} Claim in UCCS over a privacy
preserving Secure Channel allows a verifier to correlate UCCS from a single
attesting environment across many Secure Channel sessions. This may be
acceptable in some use-cases (e.g. if the attesting environment is a
physical sensor in a factory) and unacceptable in others (e.g. if the
attesting environment is a device belonging to a child).

# IANA Considerations

In the registry {{-tags}},
IANA is requested to allocate the tag in {{tab-tag-values}} from the
FCFS space, with the present document as the specification reference.

| Tag    | Data Item | Semantics                            |
| TBD601 | map       | Unprotected CWT Claims Set [RFCthis] |
{: #tab-tag-values cols='r l l' title="Values for Tags"}


# Security Considerations

The security considerations of {{-cbor}} and {{-cwt}} apply.

{{secchan}} discusses security considerations for Secure Channels, in which
UCCS might be used.  This documents provides the CBOR tag definition for UCCS and a discussion on security consideration for the use of UCCS in
Remote ATtestation procedureS (RATS).  Uses of UCCS outside the scope of
RATS are not covered by this document.  The UCCS specification - and the
use of the UCCS CBOR tag, correspondingly - is not intended for use in a
scope where a scope-specific security consideration discussion has not
been conducted, vetted and approved for that use.

--- back

# Example

The example CWT Claims Set from Appendix A.1 of {{-cwt}} can be turned into
an UCCS by enclosing it with a tag number TBD601:

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
