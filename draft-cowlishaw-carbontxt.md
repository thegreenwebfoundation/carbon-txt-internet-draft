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
    SemVer:
        title: "Semantic Versioning 2.0.0 (text from June 19, 2020)"
        target: https://github.com/semver/semver/blob/8b2e8eec394948632957639dfa99fc7ec6286911/semver.md

informative:
    CarbonTxt:
        title: "carbon.txt syntax reference"
        target: https://carbontxt.org/syntax
    CarbonTxtValidator:
        title: "carbon.txt validator API"
        target: https://carbon-txt-api.greenweb.org/api/docs
    CarbonTxtPython:
        title: "carbon.txt python package"
        target: https://pypi.org/project/carbon-txt/
    CarbonTxtGithub:
        title: "carbon.txt Github repository"
        target: https://github.com/thegreenwebfoundation/carbon-txt-validator
    CSRD:
        title: "Corporate Sustainability Reporting"
        target: https://finance.ec.europa.eu/financial-markets/company-reporting-and-auditing/company-reporting/corporate-sustainability-reporting_en
    AiModelCard:
        title: "AI Model Cards"
        target: https://deepmind.google/models/model-cards/
    AiModelCardCO2:
        title: "Displaying Carbon Emissions for your Model"
        target: https://huggingface.co/docs/hub/main/en/model-cards-co2
    ISO14001:
        title: "Environmental management systems — Requirements with guidance for use"
        author:
            org: ISO
        date: 2015
        seriesinfo:
            ISO: "14001:2015"
        target: https://www.iso.org/standard/60857.html

...

--- abstract

This document specifies the carbon.txt convention, a machine-readable format for publishing links to disclosures concerning the environmental impact of an organization's digital / ICT operations at a predictable location on the web. Carbon.txt is a TOML document that acts as an index: it declares where an origin's impact disclosures (Annual reports, Sustainability policies, Third party certifications, Quantitative measurements or estimations) may be found, and optionally lists upstream service providers whose own disclosures may be relevant to the origin's environmental impact. Carbon.txt does not carry numeric impact metrics or specific claims about impacts itself; it links to documents that do. In addition it defines a discovery mechanism by which certain origins may delegate their carbon.txt implementation  to another.


--- middle


# Introduction {#intro}

Organisations increasingly publish information about the environmental impact of their digital operations — Impact reports, energy mix disclosures and guarantees of origin, regulatory filings under frameworks such as the EU Corporate Sustainability Reporting Directive (CSRD), and measurements of CO2 impact;  but this information is scattered across PDFs, web pages, and third-party registries with no standard way for a client to discover it programmatically.

The carbon.txt convention addresses this by providing a single, predictable location on an origin where a machine-readable index of those disclosures can be found. It is analogous to robots.txt {{?RFC9309}} for crawler instructions or security.txt {{?RFC9116}} for security contact information: a small file at a well-known path that tells a client where to look for structured information about the origin.

The convention described in this document is already deployed and implemented by the Green Web Foundation as an HTTP API {{CarbonTxtValidator}}, and python library {{CarbonTxtPython}}, and released as open source {{CarbonTxtGithub}} .

## Design principles:

- carbon.txt is an index, not a repository. It links to documents; it does not itself contain any disclosures..
- It is human-readable (TOML {{TOML}} with comments) as well as machine-parsable.
- It is designed for incremental deployment. A minimal valid file can be as simple as a link to a single disclosure.
- It does not in itself verify or guarantee the veracity of claims or disclosures, but provides mechanisms for third-party validation.

## Relationship to other work

This document is complementary to the sustainability-data well-known URI proposal made by {{?I-D.besleaga-sustainability-wellknown}}. The sustainability-data draft outlines a mechanism for declaring the energy consumption, carbon footprint, and related environmental and sustainability metrics of an origin for different reporting periods, while this proposal explicitly considers the sharing of such metrics to be out of scope, concentrating instead on providing a mechanism for indexing sustainability disclosures (such as a sustainability-data declaration) related to an origin at a predictable location. The two proposals compose cleanly: An implementation of carbon.txt may link to their sustainability-data as a disclosure, and a sustainability-data file may link back to carbon-txt via the disclosure-uri field.

## Compatibility with earlier versions.

This document describes version 1.0.0-RC1 of the carbon.txt syntax, the first which we have attempted to standardise as an Internet Draft. It builds upon earlier versions of the carbon.txt project {{CarbonTxt}}, but radically simplifies various aspects, in particular the discovery mechanism outlined in section 3. As a result, this version does not maintain strict backwards compatibility with previous versions of carbon.txt.

# Conventions and Definitions

{::boilerplate bcp14-tagged}

Origin:
:  As defined in {{!RFC6454}}. A tuple of scheme, host, and port.

Owner organization:
:  The organization or other entity ultimately responsible for the content or services provided by the Origin.

Digital operations:
:  Any processes, systems or infrastructure used by the owner organization related to their use of digital technologies or platforms

Environmental impact:
:  Any change to the environment, whether adverse or beneficial, resulting wholly or partly from the activities, products, or services of an organization or system. Adapted from {{ISO14001}}. This document uses the term to cover both direct impacts (e.g., energy use and the manufacture of devices) and indirect impacts (e.g., effects arising from how Internet technologies are used). See {{?RFC9547}} for discussion.

Disclosure:
:  A published document or resource that contains either: evidence about direct and indirect environmental impacts of the digital operations of the owner organization, or organizational policy around reduction or mitigation of adverse impacts.

Certification scheme:
:  A set of specific commitments or standards around environmental impact or governance, set, and evaluated for compliance by a third-party organization.

Upstream service:
:  A third-party provider of digital technology services used by the owner organization (e.g., hosting, CDN, analytics).

carbon.txt file:
:  A TOML {{TOML}} document conforming to the schema defined in Section 5 of this specification.

# Discovery mechanism

A client MUST attempt to locate a carbon.txt file using the following mechanisms, in order. The client MUST stop at the first location that returns an HTTP 200 response with a parsable TOML body.

## DNS TXT Record

An origin MAY advertise the location of its carbon.txt file via a DNS TXT record in the format `carbon-txt-location=<URL>` on the domain, pointing to an HTTPS URL of a carbon.txt file. If this file is present, it MUST return an HTTP 200 response with a parsable TOML body. If the TXT record is present and well-formed, the client MUST attempt to fetch the referenced carbon.txt file at that URL before continuing with the discovery process. The client MUST NOT transitively perform a DNS TXT record lookup on this target domain - if no file exists at the target URL, then the discovery process fails and terminates.

## Well-Known URI {{!RFC8615}}

An origin SHOULD publish a carbon.txt file at: the path `/.well-known/carbon.txt` relative to the origin root. This section requires an IANA registration request for the "carbon.txt" well-known URI - see {{iana}}. If this file is present, it MUST return an HTTP 200 response with a parsable TOML body.

## carbon.txt file at the domain root

An origin MAY serve its carbon.txt file at the root of its domain, at the path `/carbon.txt`, relative to the origin root. If the `.well-known/carbon.txt` file is absent but this file is present, and it returns a HTTP 200 response with a parsable TOML body, it SHOULD be used as the canonical carbon.txt for the origin's owner organization. This path is retained for backwards compatibility with earlier deployments, the Well-Known URI in the preceding section is the preferred location.

## Transport Requirements

The carbon.txt file MUST be served over HTTPS. A client MUST NOT follow any HTTP redirects when requesting a carbon.txt file. The response body MUST be served with a Content-Type of application/toml or text/plain.

## Design considerations and rationale.

The primary mechanism for discovering a carbon.txt file is via the Well-Known URI, in line with existing standards [RFC8615] on well-known resource discovery. In parallel, we fall back to the carbon.txt location at the domain root, for reasons of backwards compatibility with our existing installed user base, and for consistency with existing conventions such as robots.txt {{?RFC9309}}and security.txt {{?RFC9116}}. The DNS TXT record alternative is provided to allow organizations which operate more than one domain to maintain a canonical carbon.txt file on a single domain, and to point to it from other domains they control. This approach allows for an explicit, single-step delegation from one domain to another, avoiding transitive lookups of undetermined length. We disallow the following of standard HTTP redirects in order to ensure that only one step of delegation is permitted, and to avoid the case where an origin might fraudulently and transparently redirect to the carbon.txt file of an unrelated organization.

# File format

A carbon.txt file MUST be valid TOML as specified in {{TOML}}, encoded as UTF-8 text. Comments (lines beginning with #) are permitted and SHOULD be ignored by parsers.

## Design considerations & rationale

TOML was chosen as a serialization format for its combination of human and machine readability, and its stability and wide adoption. In addition, it allows for commenting, something not supported in eg. JSON files, which aids with human-readability. This spec makes use of a subset of the TOML 1.0.0 specification, concretely the constructs: "table", "array of tables", "inline table", "string", and "local date".

# Schema

This section defines version 1.0.0-RC1 of the carbon.txt syntax.

The syntax is expressed in terms of TOML {{TOML}}. The terms "table", "array of tables", "inline table", "string", and "local date" are used as defined by the TOML specification. In the field descriptions below, the notation `[table]` denotes a TOML table, `[[array]]` denotes an array of tables, and `[string]` denotes an array of strings.

The document consists of the following top-level keys and tables:

| Key            | Type         | Presence |
|:---------------|:-------------|:---------|
| `version`      | string       | REQUIRED |
| `last_updated` | date         | OPTIONAL |
| `org`          | `[table]`    | REQUIRED |
| `upstream`     | `[table]`    | OPTIONAL |

## Common value types {#types}

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
of the syntax, the value MUST be `"1.0.0-RC1"`. Future version numbers MUST conform to the {{SemVer}} convention.
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

The `org` table holds information about the organization's environmental impact disclosures and the certification schemes it adheres to. It contains the
following keys:

| Key                     | Type        | Presence |
|:------------------------|:------------|:---------|
| `disclosures`           | `[[array]]` | REQUIRED |
| `certification_schemes` | `[[array]]` | OPTIONAL |

### Disclosures

Type: `[[array]]`. REQUIRED.

An array of tables, each linking to a document that shows the organization's impact disclosures. Each element has the following keys:

| Key                     | Type        | Presence |
|:------------------------|:------------|:---------|
| `doc_type`              | string      | REQUIRED |
| `url`                   | url         | REQUIRED |
| `title`                 | string      | OPTIONAL |
| `description`           | string      | OPTIONAL |
| `valid_until`           | date        | OPTIONAL |
| `certification_schemes` | `[string]`  | OPTIONAL |

`doc_type`:
: A string identifying the type of document being linked to. The value MUST be one of the following:

  `annual-report`
  : An annual report.

  `sustainability-page`
  : A web page presenting the organization's sustainability policy.

  `certificate`
  : A certificate - for instance, of conformance to an Ecolabel, or a renewable energy certificate certifying provenance of energy supply.

  `csrd-report`
  : A report prepared under the EU Corporate Sustainability Reporting
    Directive (CSRD) {{CSRD}}.

  `ai-model-card`
  : A model card {{AiModelCard}} describing an AI model, which includes co2 emissions information as described in {{AiModelCardCO2}}.

  `measurement-data`
  : A document containing directly measured or estimated impacts data.

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
    certification_schemes = ["blauer-engel"],
    description = "This company's sustainability policy." },
  { doc_type = "annual-report",
    url = "https://mycompany.com/carbon-emissions-2026.pdf",
    valid_until = 2026-12-31,
    title = "Emissions Report 2026" },
  { doc_type = "measurement-data",
    url = "https://mycompany.com/dist.json",
    title = "Quarterly emissions figures",
    description = "Estimated carbon impact data for this company's digital operations, in DIST format" }
]
~~~

### Certification Schemes {#certification-schemes}

Type: `[[array]]`. OPTIONAL.

An array of tables, each linking to a certification scheme (such as a voluntary standard or ecolabel) to which the organization adheres. Each
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

The `upstream` table holds information linking the organization to the
upstream providers it uses. It contains the following key:

| Key        | Type        | Presence |
|:-----------|:------------|:---------|
| `services` | `[[array]]` | OPTIONAL |

### Services

Type: `[[array]]`. OPTIONAL.

An array of tables, each describing an upstream provider used by the
organization. Each element has the following keys:

| Key            | Type                      | Presence |
|:---------------|:--------------------------|:---------|
| `domain`       | domain                    | OPTIONAL |
| `service_type` | string or `[string]`      | OPTIONAL |

`domain`:
: The domain of the organization providing the upstream service.

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
    certification_schemes = ["blauer-engel"],
    description = "This company's sustainability policy." },
  { doc_type = "annual-report",
    url = "https://mycompany.com/carbon-emissions-2026.pdf",
    valid_until = 2026-12-31,
    title = "Emissions Report 2026" },
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

The carbon.txt syntax is designed for simplicity and flexibility - A minimal carbon.txt file requires just a single disclosure, denoted by a document type and URL, along with the version number of the carbon.txt syntax, while the optional syntax allows for more complex or detailed disclosures to be made as required. We outline below the rationale behind each element in the syntax.

### Version number

The inclusion of an explicit version number allows for flexibility in future updates to the syntax, without breaking backwards compatibility. For instance, if a future version makes a currently-optional field required, the version field ensures that the correct validation semantics are applied.

### Last-updated date

The last-updated date allows clients to quickly verify if new information has been added since the last time the file was checked, and some indication of whether the linked disclosures are up to date or not, in the case of disclosures with a particular validity period, such as annual reports.

### Disclosures

Each disclosure, at  a minimum, must include a document type, and the URL where the document can be found, ensuring that a minimal valid carbon.txt file requires very little additional information about the disclosure itself. Additional optional descriptive fields (`title` and `description` are provided for convenience for downstream users, or to provide carbon.txt publishers with the ability to provide additional context around why a given disclosure has been included. (For instance, in the case of an annual report, a section of which details emissions reduction efforts, the description field can be used to highlight the relevant section, or to give a summary of the relevance of the overall document). The optional `valid_until` field can be used to clearly signal the period of validity of a disclosure (in the case that it is time-limited, such as an annual report or a certificate) to downstream users. The `certification_schemes` property allows for a disclosure to be marked as relevant to a particular certification scheme, described in detail in the following section.

### Certification schemes {#certification-schemes-rationale}

carbon.txt allows an organization to declare membership of certification schemes - standards operated and overseen by third parties, such as ecolabels or voluntary standards. These are listed in the `certification_schemes` block, and must, at a minimum, include a URL pointing to a web page representing the certification scheme, operated by the overseeing third party organization, as well as a unique, file-local ID, which can be used to refer to this certification scheme in a disclosure block. In this way, where an organization's environmental impacts are verified by an external certification scheme, the carbon.txt file can make it clear which disclosures are so verified. As with disclosures, `certification_schemes` may also be given an optional title and description, allowing carbon.txt publishers to provide additional information about the relevance of a given scheme.

### Upstreams

The upstream service block allows an organization to optionally specify the upstream providers of digital services and infrastructure on which they depend, allowing downstream users to transitively discover the carbon.txt disclosures made by these providers. Each upstream provider must include a `domain` representing the provider organization, and an `service_type` string representing the particular service they provide.

# Security Considerations {#security}

Because of the use of URIs and well-known resources, security considerations of {{!RFC3986}} and {{!RFC8615}} apply here, in addition to the considerations outlined below.

To protect against attacks against their system, implementers of carbon.txt parsing and matching logic should take the following considerations into account:

Memory management:
:    carbon.txt publishers MUST ensure that their carbon.txt files are less than 32MB in size, and implementers MUST ensure that  at least 32MB of memory is allocated to contain the contents of any carbon.txt file, in order to mitigate against buffer overflow attacks.

Invalid characters:
:    carbon.txt publishers MUST only use valid UTF-8 characters in their carbon.txt files, and implementers MUST discard any byte sequence which does not correspond to a valid UTF-8 character, and reject the corresponding carbon.txt file as invalid.

Untrusted content:
:    Implementers should treat the content of a carbon.txt file as untrusted content, as defined by the specification of the application layer used. In the context of HTTP, implementers should follow the Security Considerations section of {{!RFC9110}}.


# IANA Considerations {#iana}

## Well-Known URI Registration

This document requests registration of the following URI in the "Well-Known URIs" registry {{!RFC8615}}:

URI suffix:
:  carbon.txt

Change controller:
:   IETF

Specification document:
: this document

Status:
:   active

Related information:
:   none


# Acknowledgments
{:numbered="false"}

Many thanks to carbon.txt early adopters and user testing participants Andri Johnson, Owen Rogers, Tim Frick, Siddhesh Wagle, Tin Geber, Célien Camus, TJ Andrews, Frank Lämmer, Cyberfusion and The Ethical Agency, along with many others, whose expertise and feedback helped shape this convention. Thanks also to Andrei Nicolae Besleaga, author of the sustainability-data well-known URI Internet Draft {{?I-D.besleaga-sustainability-wellknown}}.

The development of carbon.txt was funded in part by ICANN and NGI Search grants.

--- back
