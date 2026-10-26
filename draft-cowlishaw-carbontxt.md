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
    RFC2119:
    RFC8174:
    RFC3339:
    RFC3986:
    RFC5234:
    TOML:
        title: "TOML: Tom's Obvious, Minimal Language, Version 1.0.0"
        target: https://toml.io/en/v1.0.0
        date: 2021-11-01
        author:
          - ins: T. Preston-Werner
            name: Tom Preston-Werner
          - ins: P. Gedam
            name: Pradyun Gedam
    SemVer:
        title: "Semantic Versioning 2.0.0 (text from June 19, 2020)"
        target: https://github.com/semver/semver/blob/8b2e8eec394948632957639dfa99fc7ec6286911/semver.md

    KyotoProtocol:
        title: "Kyoto Protocol to the United Nations Framework Convention on Climate Change"
        target: https://unfccc.int/process-and-meetings/the-kyoto-protocol
        date: 1997-12-11

    GHGProtocol:
        title: "The GHG Protocol Corporate Accounting and Reporting Standard"
        target: https://ghgprotocol.org/corporate-standard

informative:
   CarbonTxt:
        title: "carbon.txt syntax reference"
        target: https://carbontxt.org/syntax
...

--- abstract

This document specifies the carbon.txt convention, a machine-readable format for publishing links to disclosures concerning the environmental impact of an organization's digital / ICT operations at a predictable location on the web. Carbon.txt is a TOML document that acts as an index: it declares where an origin's impact disclosures (Annual reports, Sustainability policies, Third party certifications, Quantitative measurements or estimations) may be found, and optionally lists upstream service providers whose own disclosures may be relevant to the origin's environmental impact. Carbon.txt does not carry numeric impact metrics or specific claims about impacts itself; it links to documents that do. In addition it defines a discovery mechanism by which certain origins may delegate their carbon.txt implementation  to another.


--- middle


# Introduction {#intro}

Organisations increasingly publish information about the greenhouse gas emissions of their digital operations — Impact reports, energy mix disclosures and guarantees of origin, regulatory filings under frameworks such as the EU Corporate Sustainability Reporting Directive (CSRD), and measurements of CO2 impact;  but this information is scattered across PDFs, web pages, and third-party registries with no standard way for a client to discover it programmatically.

The carbon.txt convention addresses this by providing a single, predictable location on an origin where a machine-readable index of those disclosures can be found. It is analogous to robots.txt {{?RFC9309}} for crawler instructions or security.txt {{?RFC9116}} for security contact information: a small file at a well-known path that tells a client where to look for structured information about the origin.

## Design principles:

- carbon.txt is an index, not a repository. It links to documents; it does not itself contain any disclosures..
- It is human-readable (TOML {{TOML}} with comments) as well as machine-parsable.
- It is designed for incremental deployment. A minimal valid file can be as simple as a link to a single disclosure.
- It does not in itself verify or guarantee the veracity of claims or disclosures, but provides mechanisms for third-party validation.

## Relationship to other work

This document is complementary to the sustainability-data well-known URI proposal made by {{?I-D.besleaga-sustainability-wellknown}}. The sustainability-data draft outlines a mechanism for declaring the energy consumption, carbon footprint, and related environmental and sustainability metrics of an origin for different reporting periods, while this proposal explicitly considers the sharing of such metrics to be out of scope, concentrating instead on providing a mechanism for indexing sustainability disclosures (such we a sustainability-data declaration) related to an origin at a predictable location. The two proposals compose cleanly: An implementation of carbon.txt may link to their sustainability-data as a disclosure, and a sustainability-data file may link back to carbon-txt via the disclosure-uri field.

## Compatibility with earlier versions.

This document describes version 1.0.0-RC1 of the carbon.txt syntax, the first which we have attempted to standardise as an Internet Draft. It builds upon earlier versions of the carbon.txt project {{CarbonTxt}}, but radically simplifies various aspects, in particular the discovery mechanism outlined in section 3. As a result, this version does not maintain strict backwards compatibility with previous versions of carbon.txt.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

Origin: As defined in [RFC6454]. A tuple of scheme, host, and port.

Owner organization: The organization or other entity ultimately responsible for the content or services provided by the Origin.

Digital operations: Any processes, systems or infrastructure used by the owner organization related to their use of digital technologies or platforms

Greenhouse gas emissions: - Emissions of any of the greenhouse gases as defined in the Kyoto Protocol {{KyotoProtocol}} caused by the digital operations of the owner organization, over their entire lifecycle, emissions from purchased electricity (GHG Scope 2) {{GHGProtocol}}, and upstream and downstream indirect emissions (GHG Scope 3) {{GHGProtocol}}

Disclosure: A published document or resource that contains either: evidence about direct and indirect environmental impact of the digital operations of the owner organization, or organizational policy around reduction or mitigation of those impacts.

Upstream service: A third-party provider of digital technology services used by the owner organization (e.g., hosting, CDN, analytics).

carbon.txt file: A TOML {{TOML}} document conforming to the schema defined in Section 5 of this specification.

# Discovery mechanism

A client MUST attempt to locate a carbon.txt file using the following mechanisms, in order. The client MUST stop at the first location that returns an HTTP 200 response with a parsable TOML body.

## DNS TXT Record

An origin MAY advertise the location of its carbon.txt file via a DNS TXT record in the format `carbon-txt-location=<URL>` on the domain, pointing to an HTTPS URL of a carbon.txt file. If this file is present, it MUST return an HTTP 200 response with a parsable TOML body. If the TXT record is present and well-formed, the client MUST attempt to fetch the referenced carbon.txt file at that URL before continuing with the discovery process. This option allows an organization which maintains hosts at multiple origins to maintain a single carbon.txt file in a canonical location, and redirect to it from

## Well-Known URI [RFC8615]

An origin SHOULD publish a carbon.txt file at: the path `/.well-known/carbon.txt` relative to the origin root. [NOTE: This section requires an IANA registration request for the "carbon.txt" well-known URI. See Section 7.]. If this file is present, it MUST return an HTTP 200 response with a parsable TOML body.

## carbon.txt file at the domain root

An origin MAY serve its carbon.txt file at the root of its domain, at the path `/carbon.txt`, relative to the origin root. If this file is present, and it returns a HTTP 200 response with a parsable TOML body, it SHOULD be used as the canonical carbon.txt for the origin's owner organization. This path is retained for backwards compatibility with earlier deployments, the Well-Known URI in the following section is the preferred location.

## Transport Requirements

The carbon.txt file MUST be served over HTTPS. A client MUST NOT follow any HTTP redirects when requesting a carbon.txt file. The response body MUST be served with a Content-Type of application/toml or text/plain..

## Design considerations and rationale.

TODO

- Rationale for welll-known refefencing RFC

- RAtionale for root (widespread convention, existing deployed user base)

- Rationale for not following HTTP redirects (no impersonation)

- Rationale for DNS (allowing for explicit, single step delegation. from origin to file location.)


# File format

A carbon.txt file MUST be valid TOML as specified in {{TOML}}, encoded as UTF-8 text. Comments (lines beginning with #) are permitted and SHOULD be ignored by parsers.

## Design considerations & rationale

- TODO (for using TOML). Easily convertible to JSON, JSON-LD, but has comments, is human readable. Is stable, widely adopted.

- TODO, version, subset of features.

# Schema

This section defines version 1.0.0-RC1 of the carbon.txt syntax.

The syntax is expressed in terms of TOML {{!TOML}}. The terms "table", "array of tables", "inline table", "string", and "local date" are used as defined by the TOML specification. In the field descriptions below, the notation `[table]` denotes a TOML table, `[[array]]` denotes an array of tables, and `[string]` denotes an array of strings.

The document consists of the following top-level keys and tables:

| Key            | Type      | Presence |
|:---------------|:----------|:---------|
| `version`      | string    | REQUIRED |
| `last_updated` | date      | OPTIONAL |
| `org`          | [table]   | REQUIRED |
| `upstream`     | [table]   | OPTIONAL |

## Common value types

string:
: A TOML basic or literal string.

date:
: Either a TOML local date (for example, `2026-12-31`), or a string containing a `full-date` as defined in Section 5.6 of {{!RFC3339}} (for example, `"2026-12-31"`).

url:
: A string containing an absolute URI {{!RFC3986}} whose scheme is either `http` or `https`; that is, the string MUST begin with "http://" or "https://".

domain:
: A string containing a DNS domain name. A domain MAY include subdomains (for example, "www."). A domain MUST NOT include a protocol or scheme (such as "http://" or "https://") and MUST NOT include a path (such as "/news/", "/about", or "news-update-2025").

id:
: A string conforming to the following ABNF {{!RFC5234}}:

~~~ abnf
id = 1*( ALPHA / DIGIT / "-" / "_" )
~~~

## Top-Level Keys

### Version

Type: string. REQUIRED.

The carbon.txt syntax version to which the file conforms. For this version
of the syntax, the value MUST be `"1.0.0-RC1"`.
Example:

~~~ toml
version = "1.0.0-RC1"
~~~

### Last updated date


Type: date. OPTIONAL.

The date on which the file was last updated.

Example:

~~~ toml
last_updated = 2026-10-26
~~~

## The `org` Table

Type: `[table]`. REQUIRED.

The `org` table holds information about the organisation's sustainability disclosures and the certification schemes it adheres to. It contains the
following keys:

| Key                     | Type      | Presence |
|:------------------------|:----------|:---------|
| `disclosures`           | [[array]] | REQUIRED |
| `certification_schemes` | [[array]] | OPTIONAL |

### Disclosures

Type: `[[array]]`. REQUIRED.

An array of tables, each linking to a document that shows the organisation's sustainability data disclosures. Each element has the following keys:

| Key                     | Type      | Presence |
|:------------------------|:----------|:---------|
| `doc_type`              | string    | REQUIRED |
| `url`                   | url       | REQUIRED |
| `title`                 | string    | OPTIONAL |
| `description`           | string    | OPTIONAL |
| `valid_until`           | date      | OPTIONAL |
| `domain`                | domain    | OPTIONAL |
| `certification_schemes` | [string]  | OPTIONAL |

`doc_type`:
: A string identifying the type of document being linked to. The value MUST be one of the following:

  `annual-report`
  : An annual report.

  `sustainability-page`
  : A web page presenting the organisation's sustainability information.

  `certificate`
  : A certificate.

  `csrd-report`
  : A report prepared under the EU Corporate Sustainability Reporting
    Directive (CSRD).

  `ai-model-card`
  : A model card describing an AI model.

  `measurement-data`
  : A machine-readable document of measurement or estimate data.

  `other`
  : Any document not covered by another type.

`url`:
: The URL of the document being linked to.

`title`:
: A meaningful title describing the disclosure, for example "Corporate sustainability report 2026".

`description`:
: A brief summary of the disclosure, or any additional information needed to understand it.

`valid_until`:
: The last date on which the disclosure is valid, if it is time-limited (for example, an annual report or a renewable energy certificate).

`certification_schemes`:
: An array of one or more `id` values, each of which MUST match the `id` of an element of `org.certification_schemes` ({{certification-schemes}})
  in the same file. It identifies the certification schemes to which the disclosure relates.

Example:

~~~ toml
[org]

disclosures = [
  { doc_type = "sustainability-page",
    url = "https://mycompany.com/sustainability",
    domain = "mycompany.com",
    certification_schemes = ["blauer-engel"],
    description = "This company's sustainability policy." },
  { doc_type = "annual-report",
    url = "https://mycompany.com/carbon-emissions-2025.pdf",
    valid_until = 2025-12-31,
    title = "Emissions Report 2025" },
  { doc_type = "measurement-data",
    url = "https://mycompany.com/dist.json",
    title = "Quarterly emissions figures",
    description = "Estimated carbon impact data for this company's digital operations, in DIST format" }
]
~~~

### Certification Schemes {#certification-schemes}

Type: `[[array]]`. OPTIONAL.

An array of tables, each linking to a certification scheme (such as a voluntary standard or ecolabel) to which the organisation adheres. Each
element has the following keys:

| Key           | Type   | Presence |
|:--------------|:-------|:---------|
| `id`          | id     | REQUIRED |
| `url`         | url    | REQUIRED |
| `title`       | string | OPTIONAL |
| `description` | string | OPTIONAL |

`id`:
: An identifier used to refer to this certification scheme from elsewhere in the same file, in particular from the `certification_schemes` key of a disclosure ({{disclosures}}). The value MUST conform to the `id` production in {{types}} and MUST be unique among the `id` values of all certification schemes in the file.

`url`:
: The URL of the certification scheme.

`title`:
: A meaningful title describing the certification scheme, for example "B-corporation", "Blauer Engel", or "SCI for Web".

`description`:
: Any additional information useful for understanding the certification, such as a brief description of the commitments it represents or the guarantees it makes.

Example:

~~~ toml
[org]

certification_schemes = [
  { id = "blauer-engel",
    url = "https://www.blauer-engel.de/en/productworld/software",
    title = "Blauer Engel Resource and Energy-Efficient Software Products",
    description = "The aim of the environmental label for resource and energy-efficient software products is to reduce the total energy consumed by information and communication technology and improve resource efficiency." }
]
~~~


## The `upstream` table

Type: `[table]`. OPTIONAL.

The `upstream` table holds information linking the organisation to the
upstream providers it uses. It contains the following key:

| Key        | Type      | Presence |
|:-----------|:----------|:---------|
| `services` | [[array]] | OPTIONAL |

### Services

Type: `[[array]]`. OPTIONAL.

An array of tables, each describing an upstream provider used by the
organisation. Each element has the following keys:

| Key            | Type                    | Presence |
|:---------------|:------------------------|:---------|
| `domain`       | domain                  | OPTIONAL |
| `service_type` | string or [string]      | OPTIONAL |
{: #service-keys title="Keys of an upstream service"}

`domain`:
: The domain of the organisation providing the upstream service.

`service_type`:
: A slug representing the service provided by the upstream provider, or an
  array of such slugs where the provider supplies more than one type of
  service.

Example:

~~~ toml
[upstream]
services = [
  { domain = "cloud.google.com", service_type = "shared-hosting" },
  { domain = "aws.amazon.com" }
]
~~~

## Complete Example

~~~ toml
version = "1.0.0-RC1"
last_updated = 2026-10-26

[org]

certification_schemes = [
  { id = "blauer-engel",
    url = "https://www.blauer-engel.de/en/productworld/software",
    title = "Blauer Engel Resource and Energy-Efficient Software Products",
    description = "The aim of the environmental label for resource and energy-efficient software products is to reduce the total energy consumed by information and communication technology and improve resource efficiency." }
]

disclosures = [
  { doc_type = "sustainability-page",
    url = "https://mycompany.com/sustainability",
    domain = "mycompany.com",
    certification_schemes = ["blauer-engel"],
    description = "This company's sustainability policy." },
  { doc_type = "annual-report",
    url = "https://mycompany.com/carbon-emissions-2025.pdf",
    valid_until = 2025-12-31,
    title = "Emissions Report 2025" },
  { doc_type = "measurement-data",
    url = "https://mycompany.com/dist.json",
    title = "Quarterly emissions figures",
    description = "Estimated carbon impact data for this company's digital operations, in DIST format" }
]

[upstream]
services = [
  { domain = "cloud.google.com", service_type = "shared-hosting" },
  { domain = "aws.amazon.com" }
]

~~~

## Design considerations and rationale.

TODO
- Inclusion of version number
- Inclusion of last-updated date
- Inclusion of disclosures
- Inclusion of certification schemes
- Inlucusion of upstreams


# Security Considerations {#security}

TODO Security

- HTTP redirects
- Body size
- What else?




# IANA Considerations {#iana}

TODO IANA considerations - well known URI.

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge. - Owen, Tim, Participantsi in user testing. Cyberfusion. Siddhesh, NGI Search, ICANN, Mallory.
