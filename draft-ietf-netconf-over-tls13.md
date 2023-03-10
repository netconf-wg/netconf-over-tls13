---
title: NETCONF over TLS 1.3
abbrev: NETCONF over TLS 1.3
category: std

docname: draft-ietf-netconf-over-tls13-latest
submissiontype: IETF
consensus: true
v: 3
updates: 7589
area: "Operations and Management"
workgroup: "Network Configuration"
keyword:
 - NETCONF
 - TLS 1.3
venue:
  group: "Network Configuration"
  type: "Working Group"
  mail: "netconf@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/netconf/"
  github: "netconf-wg/netconf-over-tls13"
  latest: "https://netconf-wg.github.io/netconf-over-tls13/draft-ietf-netconf-over-tls13.html"

author:
 -
    ins: S. Turner
    name: Sean Turner
    organization: sn3rd
    email: sean@sn3rd.com
 -
    name: Russ Housley
    org: Vigil Security, LLC
    abbrev: Vigil Security
    street: 516 Dranesville Road
    city: Herndon, VA
    code: 20170
    country: US
    email: housley@vigilsec.com


--- abstract

RFC 7589 defines how to protect NETCONF messages with TLS 1.2. This
document describes how to protect NETCONF messages with TLS 1.3. This
document also updates RFC 7589 to address support requirements for
the mandatory to implement TLS 1.2 cipher suite and TLS 1.3.

--- middle

# Introduction

{{!RFC7589}} defines how to protect NETCONF messages {{!RFC6241}} with
TLS 1.2 {{!RFC5246}}. This document defines how to protect
NETCONF messages with TLS 1.3 {{!I-D.ietf-tls-rfc8446bis}}. This
document also updates {{RFC7589}} to address support requirements for
TLS 1.3 {{!I-D.ietf-tls-rfc8446bis}}.

This document addresses cipher suites and the use of early data, which is also
known as 0-RTT data. It also updates the "netconf-tls" IANA Registered Port
Number entry to refer to this document. All other provisions set forth
in {{RFC7589}} are unchanged, including connection initiation, message framing,
connection closure, certificate validation, server identity, and client
identity.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

# Early Data

Early data (aka 0-RTT data) is a mechanism defined in TLS 1.3
{{I-D.ietf-tls-rfc8446bis}} that allows a client to send data ("early data")
as part of the first flight of messages to a server. Note that TLS 1.3 can
be used without early data as per {{Section F.5 of I-D.ietf-tls-rfc8446bis}}.
In fact, early data is permitted by TLS 1.3 only when the client and server
share a Pre-Shared Key (PSK), either obtained externally or via a previous
handshake. The client uses the PSK to authenticate the server and to encrypt
the early data.

As noted in {{Section 2.3 of I-D.ietf-tls-rfc8446bis}}, the security
properties for early data are weaker than those for subsequent TLS-protected
data. In particular, early data is not forward secret, and there is no
protection against the replay of early data between connections.
{{Appendix E.5 of I-D.ietf-tls-rfc8446bis}} requires applications not
use early data without a profile that defines its use. This document
specifies that NETCONF implementations that support TLS 1.3 MUST NOT use early
data.

# Cipher Suites

Implementations MUST support TLS 1.2 {{RFC5246}} and are REQUIRED to
support the TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 cipher suite {{RFC9325}}.

Implementations MAY implement additional TLS 1.2 cipher suites that provide
mutual authentication {{RFC5246}} and confidentiality as required by
NETCONF {{RFC6241}}.

Implementations SHOULD support TLS 1.3 {{I-D.ietf-tls-rfc8446bis}} and,
if implemented, MUST prefer to negotiate TLS 1.3 over earlier versions
of TLS.

Implementations that support TLS 1.3 {{I-D.ietf-tls-rfc8446bis}} are
REQUIRED to support the mandatory-to-implement cipher suites listed in
{{Section 9.1 of I-D.ietf-tls-rfc8446bis}}.

Implementations that support TLS 1.3 MAY implement additional TLS cipher
suites that provide mutual authentication and confidentiality, which are
required for NETCONF {{RFC6241}}.

NETCONF implementations SHOULD follow the recommendations given in
{{!I-D.ietf-uta-rfc7525bis}}.

~~~
So, this is what {{Section 9.1 of I-D.ietf-tls-rfc8446bis}} says:

  A TLS-compliant application MUST implement the TLS_AES_128_GCM_SHA256
  [GCM] cipher suite and SHOULD implement the TLS_AES_256_GCM_SHA384
  [GCM] and TLS_CHACHA20_POLY1305_SHA256 [RFC8439] cipher suites (see
  Appendix B.4).

  A TLS-compliant application MUST support digital signatures with
  rsa_pkcs1_sha256 (for certificates), rsa_pss_rsae_sha256 (for
  CertificateVerify and certificates), and ecdsa_secp256r1_sha256.  A
  TLS-compliant application MUST support key exchange with secp256r1
  (NIST P-256) and SHOULD support key exchange with X25519 [RFC7748].

Is there any reason to narrow the algorithm choices?

My guess is not.  These ought to be available in all TLS libraries.
~~~

# Security Considerations

Please review the Security Considerations in TLS 1.3 {{I-D.ietf-tls-rfc8446bis}}.

Please review the recommendations regarding Diffie-Hellman exponent reuse
in {{Section 7.4 of I-D.ietf-uta-rfc7525bis}}.

Please review the Security Considerations in NETCONF {{!RFC6241}}.

NETCONF is used to access configuration and state information and to
modify configuration information. TLS 1.3 mutual authentication is used
to ensure that only authorized users and systems are able to view the
NETCONF server's configuration and state or to modify the NETCONF
server's configuration. To this end, neither the client nor the server
should establish a NETCONF over TLS 1.3 connection with an unknown,
unexpected, or incorrectly identified peer; see {{Section 7 of RFC7589}}. If
deployments make use of a trusted list of Certification Authority (CA)
certificates {{!RFC5280}}, then the listed CAs should only issue certificates
to parties that are authorized to access the NETCONF servers. Doing otherwise
will allow certificates that were issued for other purposes to be
inappropriately accepted by a NETCONF server.

Please review {{?RFC6125}} for further details on generic host name
validation in the TLS context.

Please review the recommendations regarding certificate revocation checking
in {{Section 7.5 of I-D.ietf-uta-rfc7525bis}}.

# IANA Considerations

IANA is requested to add a reference to this document in the
"netconf-tls" entry in the "Registered Port Numbers". The updated
registry entry would appear as follows:

     Service Name:           netconf-tls
     Transport Protocol(s):  TCP
     Assignee:               IESG <iesg@ietf.org>
     Contact:                IETF Chair <chair@ietf.org>
     Description:            NETCONF over TLS
     Reference:              RFC 7589, [THIS RFC]
     Port Number:            6513


--- back

# Acknowledgments
{:numbered="false"}

We would like to thank the following people TBD.
