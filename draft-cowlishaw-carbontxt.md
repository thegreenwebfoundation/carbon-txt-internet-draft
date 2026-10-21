---
title: "carbon.txt: A transparency protocol for public sustainability data"
abbrev: "carbon.txt"
category: info

docname: draft-cowlishaw-carbontxt-latest
submissiontype: IETF # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
# area: AREA
# workgroup: WG Working Group
keyword:
 - sustainability
 - transparency
 - web
venue:
  github: "thegreenwebfoundation/carbon-txt-internet-draft"
  latest: "https://thegreenwebfoundation.github.io/carbon-txt-internet-draft/draft-cowlishaw-carbontxt.html"
#  group: WG
#  type: Working Group
#  mail: WG@example.com
#  arch: https://example.com/WG

author:
 -
   fullname: Tim Cowlishaw
   organization: The Green Web Foundation
   email: tim@greenweb.org
 -
   fullname: Chris Adams
   organization: The Green Web Foundation
   email: chris@greenweb.org
 -
   fullname: Hannah Smith
   organization: The Green Web Foundation
   email: hannah@greenweb.org
 -
   fullname: Fershad Irani
   organization: The Green Web Foundation
   email: fershad@greenweb.org

normative:
    TOML:
        title: "TOML: Tom's Obvious, Minimal Language, Version 1.0.0"
        target: https://toml.io/en/v1.0.0
        date: 2021-11-01
        author:
          - ins: T. Preston-Werner
            name: Tom Preston-Werner
          - ins: P. Gedam
            name: Pradyun Gedam
informative:


...

--- abstract

This document specifies the carbon.txt convention, a machine-readable format for publishing links to an origin's sustainability disclosures at a predictable location on the web. Carbon.txt is a TOML document that acts as an index: it declares where an origin's sustainability information lives (Annual reports, Sustainability policies, Third party certifications, Quantitative measurements or estimations) and optionally lists upstream service providers whose own disclosures may be relevant to the origin's environmental impact. Carbon.txt does not carry numeric impact metrics or specific sustainability claims itself; it links to documents that do. In addition it defines discovery mechanism by which certain origins may delegate their sustainability disclosures to another.


--- middle

# Questions to resolve.

- Is this about origins, servers, hosts or organizatiosn?
- Is .well-known the new preferred location?
-

# Introduction {#intro}

Organisations increasingly publish sustainability information relevant to their digital operations — Impact reports, energy mix disclosures and guarantees of origin, regulatory filings under frameworks such as the EU Corporate Sustainability Reporting Directive (CSRD), and measurements of CO2 impact;  but this information is scattered across PDFs, web pages, and third-party registries with no standard way for a client to discover it programmatically.

The carbon.txt convention addresses this by providing a single, predictable location on an origin where a machine-readable index of those disclosures can be found. It is analogous to robots.txt {{?RFC9309}} for crawler instructions or security.txt {{?RFC9116}} for security contact information: a small file at a well-known path that tells a client where to look for structured information about the origin.

## Design principles:

- carbon.txt is an index, not a data store. It links to documents; it does not embed specific claims or measurements.
- It is human-readable (TOML with comments) as well as machine-parsable.
- It is designed for incremental deployment. A minimal valid file can be as simple as a link to a single disclosure.
- It does not in itself verify or guarantee the veracity of claims or disclosures, but provides mechanisms for third-party validation.

## Relationship to other work

This document is complementary to the sustainability-data well-known URI proposal made by {{?I-D.besleaga-sustainability-wellknown}}. The sustainability-data draft outlines a mechanism for declaring the energy consumption, carbon footprint, and related environmental and sustainability metrics of an origin for different reporting periods, while this proposal explicitly considers the sharing of such metrics to be out of scope, concentrating instead on providing a mechanism for indexing sustainability disclosures (such we a sustainability-data declaration) related to an origin at a predictable location. The two proposals compose cleanly: An implementation of carbon.txt may link to their sustainability-data as a disclosure, and a sustainability-data file may link back to carbon-txt via the disclosure-uri field.

## Compatibility with earlier versions.

TODO This version is 0.6, summry of changes and process that led here, importance of maintaining compatibility.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

Origin: As defined in [RFC6454]. A tuple of scheme, host, and port.

Disclosure: A published document or resource that contains sustainability-related information about an organisation.

Upstream service: A third-party provider whose services are used by the origin and whose sustainability disclosures may be relevant to the origin's environmental impact (e.g., hosting, CDN, analytics).

carbon.txt file: A TOML {{TOML}} document conforming to the schema defined in Section 5 of this specification.

# Discovery mechanism

A client MUST attempt to locate a carbon.txt file using the following mechanisms, in order. The client MUST stop at the first location that returns an HTTP 200 response with a parsable TOML body.

## DNS TXT Record

An origin MAY advertise the location of its carbon.txt file via a DNS TXT record in the format `carbon-txt-location=<FQDN_OR_URL>` on the domain, pointing either to another fully-qualified domain name, or an HTTPS URL of a carbon.txt file.
If the DNS TXT record points to a fully-qualified domain name, the client MUST repeat this discovery process recursively for the linked domain. If the TXT record points to an HTTPS url, the client MUST attempt to fetch the carbon.txt file at that URL before continuing with the discovery process.

## carbon.txt file at the domain root

An origin MAY serve its carbon.txt file at the root of its domain, at the path `/carbon.txt`, relative to the origin root. If this file is present, and it returns a HTTP 200 response with a parsable TOML body, it SHOULD be used as the canonical carbon.txt for the origin. This path is retained for backwards compatibility with earlier deployments, the Well-Known URI in the following section is the preferred location.

## Well-Known URI

An origin SHOULD publish a carbon.txt file at: the path `/.well-known/carbon.txt` relative to the origin root. [NOTE: This section requires an IANA registration request for the "carbon.txt" well-known URI. See Section 7.]. If this file is present, it MUST return an HTTP 200 response with a parsable TOML body.

## HTTP Response Header

An origin MAY advertise the location of its carbon.txt file via the following response header on any HTTP response for the origin:

An origin MAY advertise the location of its carbon.txt file via the following response header on any HTTP response for the origin: `CarbonTxt-Location=<FQDN_OR_URL>`, pointing either to another fully-qualified domain name, or an HTTPS URL of a carbon.txt file.
If the DNS TXT record points to a fully-qualified domain name, the client MUST repeat this discovery process recursively for the linked domain. If the TXT record points to an HTTPS url, the client MUST attempt to fetch the carbon.txt file at that URL. If no carbon.txt file is found at that url then the discovery process terminates unsuccesfully.

## 3.5. Transport Requirements

The carbon.txt file MUST be served over HTTPS. A client MUST NOT follow a redirect to a non-HTTPS location. The response body MUST be served with a Content-Type of application/toml or text/plain. The response body MUST NOT exceed 256 KiB.

## Design considerations and rationale




# File format

A carbon.txt file MUST be valid TOML as specified in {{TOML}}. Comments (lines beginning with #) are permitted and SHOULD be ignored by parsers.

- TODO, version, subset of features.

# Schema

TODO schema

## Version

## Last updated date

## Disclosures

## Certification Schemes

## Services

## Version-specific schema rules

# Security Considerations {#security}

TODO Security


# IANA Considerations {#iana}

TODO IANA considerations - well known URI, possibly HTTP header?

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
