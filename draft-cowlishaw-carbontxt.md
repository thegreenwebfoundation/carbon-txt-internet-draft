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

informative:

...

--- abstract

This document specifies the carbon.txt convention, a machine-readable format for publishing links to an origin's sustainability disclosures at a predictable location on the web. Carbon.txt is a TOML document that acts as an index: it declares where an origin's sustainability information lives (Annual reports, Sustainability policies, Third party certifications, Quantitative measurements or estimations) and optionally lists upstream service providers whose own disclosures may be relevant to the origin's environmental impact. Carbon.txt does not carry numeric impact metrics or specific sustainability claims itself; it links to documents that do. In addition it defines discovery mechanism by which certain origins may delegate their sustainability disclosures to another.


--- middle

# Introduction {#intro}

Organisations increasingly publish sustainability information relevant to their digital operations — Impact reports, energy mix disclosures and guarantees of origin, regulatory filings under frameworks such as the EU Corporate Sustainability Reporting Directive (CSRD), and measurements of CO2 impact;  but this information is scattered across PDFs, web pages, and third-party registries with no standard way for a client to discover it programmatically.

The carbon.txt convention addresses this by providing a single, predictable location on an origin where a machine-readable index of those disclosures can be found. It is analogous to robots.txt for crawler instructions or security.txt for security contact information: a small file at a well-known path that tells a client where to look for structured information about the origin.


## Design principles:

- carbon.txt is an index, not a data store. It links to documents; it does not embed specific claims or measurements.
- It is human-readable (TOML with comments) as well as machine-parseable.
- It is designed for incremental deployment. A minimal valid file can be as simple as a link to a single disclosure.
- It does not in itself verify or guarantee the veracity of claims or disclosures, but provides mechanisms for third-party validation.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

Origin: As defined in [RFC6454]. A tuple of scheme, host, and port.

Disclosure: A published document or resource that contains sustainability-related information about an organisation.

Upstream service: A third-party provider whose services are used by the origin and whose sustainability disclosures may be relevant to the origin's environmental impact (e.g., hosting, CDN, analytics).

carbon.txt file: A TOML document conforming to the schema defined in Section 5 of this specification.

# Discovery mechanism

TODO discovery mechanism

# File format

TODO file format

# Schema

TODO schema

# Security Considerations {#security}

TODO Security


# IANA Considerations {#iana}

TODO IANA considerations

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
