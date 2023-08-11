---
###
# Internet-Draft Markdown Template
#
# Rename this file from draft-todo-yourname-protocol.md to get started.
# Draft name format is "draft-<yourname>-<workgroup>-<name>.md".
#
# For initial setup, you only need to edit the first block of fields.
# Only "title" needs to be changed; delete "abbrev" if your title is short.
# Any other content can be edited, but be careful not to introduce errors.
# Some fields will be set automatically during setup if they are unchanged.
#
# Don't include "-00" or "-latest" in the filename.
# Labels in the form draft-<yourname>-<workgroup>-<name>-latest are used by
# the tools to refer to the current version; see "docname" for example.
#
# This template uses kramdown-rfc: https://github.com/cabo/kramdown-rfc
# You can replace the entire file if you prefer a different format.
# Change the file extension to match the format (.xml for XML, etc...)
#
###
title: Credential Exchange Protocol
abbrev: CREEP
category: info

docname: draft-nsteele-cpsig-creep-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: AREA
workgroup: WG Working Group
keyword:
 - identity
 - credentials
 - protocol
venue:
  group: WG
  type: Working Group
  mail: WG@example.com
  arch: https://example.com/WG
  github: Credential-Provider-SIG/cred-migration
  latest: https://example.com/LATEST

author:
 -
    fullname: Nicholas Steele
    organization: 1Password
    email: nick.steele@1password.com

normative:
  RFC5280:

informative:
  RFC6749:

--- abstract

This document defines a protocol to securely move one or more credentials between two credential providing applications same or separate devices, is is an internet standards track protocol and requests discussion and suggestions for improvements. Distribution is unlimited.


--- middle

# Introduction
## Motivation
Individuals and organizations use credential providers to create and manage credentials on their behalf as a means to use stronger authentication factors. These credential providers can be used in browsers, on network servers, and mobile and desktop platforms, and often sharing or synchronizing credentials between different instances of the same provider is an easy and common task.
However, transfer of credentials between two different providers has traditionally been an infrequent occurence, such as when a user or organization is attempting to migrate credentials from one provider to another. As it becomes more common for users to have multiple credential providers that they use to create a manage credentials, it becomes important to address some of the security concerns with regards to migration currently:

- Credential provider applications often export credentials to be imported in an insecure format, such as CSV, that undermines the security of the provider and potentially opens the credential owner to vulnerability.
- Credential providers have no standard structure for the exported credential CSV, which can sometimes result in failure to properly migrate one or more credentials into a new provider.
- Some credentials might be unallowed to be migrate, due to device policy or lack of algorithmic capability by the importing credential provider.
- Because organizations lack a secure means of migrating user credentials, often they will apply device policy that prevents the export of credentials to a new provider under any circumstances, opting to create multiple credentials for a service.

In order to support credential provider interoporability and provide more secure means of credential transfer between providers, this document outlines a protocol for the import and export of one or more credentials between two credential providers on behalf of a user or organization in both an offline or online context. Using Diffie-Hellman key exchange, this protocol allows the creation of a secure channel or data payload between two providers.

## Scope
This protocol is scoped to describing the secure transmission of one or more credentials between two credential providers on the same or different devices managed by the same credential owner, capable of function in both online and offline contexts. This protocol does not make any assumptions about the channels in which credential data is passed from the source provider to the destination provider. The destruction of credentials after migration by the credential provider source is out of scope as well.

## Terminology

{::boilerplate bcp14-tagged}
Certain security-related terms are to be understood in the sense defined in [RFC4949].  These terms include, but are not limited to, "attack", "authentication" "authorization", "certificate", "credential", "encryption", "identity", "sign", "signature", "trust", "validate", and "verify".

### Specific Terms
[Outline words outside of the terminology section]

# Protocol Overview

~~~~~~~~~~
       ┌──────────────────────────────────────────────────────┐
       │                  Authorizing Party                   │
       └─────┬──────────────────────────────────────────┬─────┘

             │   (2) Determine        (1) Create Export │
                  Migration Key              Request
┌────────────┴──┐                                  ┌────┴──────────┐
│               ◀─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ┤               │
│               │                                  │               │
│               │                                  │               │
│               │                                  │               │
│┌─────────────┐│                                  │               │
││ (3)Encrypt  ││                                  │               │
││ Credential  ││                                  │               │
││    Data     ││                                  │               │
│└─────────────┘│                                  │               │
│               │       (4) Send Export Response   │               │
│               │─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ▶┌─────────────┐│
│               │                                  ││ (5) Decrypt ││
│               │                                  ││   Bundle    ││
│               │                                  │└─────────────┘│
│               │                                  │               │
│               │                                  │               │
│               │                                  │               │
│               │                                  │               │
│Credential Data│                                  │Credential Data│
│    Source     │                                  │  Destination  │
└───────────────┘                                  └───────────────┘
~~~~~~~~~~
{: #fig-migration-flow title="Migration Flow"}

The flow illustrated in {{fig-migration-flow}} shows the following:
1. The destination credential provider initiates the flow by creating an export request for the source. The import request includes a challenge, the scope of the export request, and a public key that has been generated by the destination credential provider or an authorizing party.
2. If an end-user and/or authorizing party authorize the request, the source credential provider uses the export request to derive the migration key, a symmetric key created from the export request public key and a private key controlled by the source credential provider, or a key can be provided by the authorizing party.
3. The source collects and encrypts the requested credential data for export and signs the challenge provided by the destination
4. The export response is sent to the destination provider and includes the encrypted credential data, the signed challenge response, and public key of the source credential provider's migration key.
5. The destination provider validates the challenge and rederives the migration key, decrypting the credential data which is then stored.

## Participants
### Credential Owner
An entity that is able to authenticate, authorize, access one or more credentials from within a credential provider. The credential owner is in charge of authorizing or delegating authorization of the migration between the source credential provider and destination provider. In the case of a credential owner being an individual, they are referred to as the end-user, an individual or service being delegated by an organization are considered credential owner agents.

### Credential Provider
Hardware or software capable of securely storing and managing credentials on behalf of their owner. There are two credential providers when discussing migration: the source provider and the destination provider. The destination provider, which can also be referred to as the importing provider, initiates the export request and is where the credential data should arrive after migration, while the source (or export provider) encrypts and transfers the credential data to the destination provider. These two credential providers will generally be two distinct pieces of hardware or software.

### Authorizing Party
An OPTIONAL certificate authority that can grant and attest certificates on behalf of a credential owner. These certificates are used to authenticate the credential agent and MAY be used to create the migration key used on behalf of the source and destination credential providers.

## Credential Types
Credential Types are defined through the Credential Data Interchange Format (CDIF)

# Supporting Different Network Conditions
CRIMP is designed to be operational in settings where network access may be limited between the credential providers and their platforms. This protocol can work in both online and offline scenarios, as well as in airgapped networks where one or both devices might not have access to the internet. In these s

## Export Request
The export request intitiates the protocol and contains a credential alongside set of request parameters for the source credential provider to create a response with. This request or elements of it MAY be created by the authorizing server if one is present, or created by the destination credential provider with or without input from the credential owner.

## Credential Payload
The credential payload includes both


# IANA Considerations

This document has no IANA actions.

# Implementation Requirements
This section defines which algorithms and features of this specification are mandatory to implement. Applications using this specification can impose additional requirements upon implementations that they use.

# Security Considerations

TODO Security

--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
