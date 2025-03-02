---
title: "A YANG Module for Entitlement Inventory"
abbrev: "almo-entitlement-inventory"
category: info
docname: draft-mcd-ivy-entitlement-inventory-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Network Inventory YANG WG"
keyword:
  - inventory
  - capability
  - entitlement
  - licensing
venue:
  group: "Network Inventory YANG WG"
  type: "Working Group"
  mail: "inventory-yang@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/inventory-yang/"
  github: "dr2lopez/ivy-capability-entitlement"
  latest: "https://dr2lopez.github.io/ivy-capability-entitlement/draft-mcd-ivy-entitlement-inventory.html"
author:
  - name: Marisol Palmero
    organization: Cisco Systems
    email: "mpalmero@cisco.com"
  - name: Camilo Cardona
    organization: NTT
    email: "mpalmero@cisco.com"
  - name: Diego Lopez
    organization: Telefonica
    email: "diego.r.lopez@telefonica.com"

normative:
  RFC2119:
  RFC8174:

informative:
  BaseInventory: I-D.ietf-ivy-network-inventory-yang

--- abstract

This document proposes a YANG module for incorporating entitlmements in a network inventory of entitlements. Entitlements define the rights for their holder to use specific feature in a network elements. The model is rooted by the concept of the capabilities offered by an element, enabled by the held entitlements, and considers entitlement scope, how they are assigned, and when they expire. The model introduces a descriptive definition of capabailities and the entitlement use restrictions, supporting entitlement admistration and the understanding of the capabilities available through the network.

--- middle

# Introduction

The goal of any network elements are included as assets in the inventory of any network service provider is to be used to build such services, takind advantage of the features provided by the inventoried assets. The use of many of these features are not necessarily associated with the acquisition of an inventoried network element, as their use required specific permissions, usually in termes of licenses issued by the network element provider explicily authorizing the use of a (number of) feature(s). As the technology evolves, making network elements more modular and software-intensive, and even fully virtualized, the management of these permissions, their agrgeggation and application to enable a concrete network service becomes more relevant. These trends also imply the combination of greater number of these permissions, within a given network element or in a number of then, implying an increasing complexity when trying to assess the feasibility of providing a specific service by integrating the necessary assets. This draft proposes a YANG model for incorporating the management of these permissions, based on two essential concepts: capability and entitlement.

Capability refers to the ability or power to do something, often indicating a skill, talent, or potential to achieve a specific outcome, and is commonly used to describe the features or functions of a system or device that enable it to perform tasks. On its side, an entitlement corresponds to the right that someone has to do or have something. Following this general definitions, the model considers entitlements as the means that grant specific holders the right to access particular capabilities of one or more of the assets in the network inventory. The use of these capabilities may be restricted in various ways, such as by duration, usage limits, or predefined conditions. Being able to exchange information on the state of the entitlements applicable to network elements can save time and facilitate decision making. This document proposes a YANG model that, complementing the network inventory module, can provide the information an asset/entitlement adminstrator needs for this purpose.

## Scope of the Entitlement Model

The entitlement model aims to provide an inventory of entitlements. This includes the entitled holders and the capabilities to which they are entitled. Additionally, it offers information into the restrictions of the operation of the different assets (network entities and components). In general, this model seeks to address the following questions:

* What entitlements are administered/owned by the organization?
* How are entitlements restricted to some assets and holders?
* What entitlements are assigned or installed on each assets?
* What constraints do the current entitlements impose in the assets functionality?
* Does the entitlement imposses any kind of global restrictions? What are they?
* What are the restrictions that each asset due to the entitlements it holds?

The model is designed with flexibility in mind, allowing for expansion through the utilization of tools provided by YANG.

The realm of entitlements and licensing is inherently complex, presenting challenges in creating a model that can comprehensively encompass all scenarios without ambiguity. While we attempt to address various situations through examples and use cases, we acknowledge that the model might not be able to cover all corner cases without ambiguity. In such cases, we recommend that implementations provide additional documentation to clarify thoese potential ambiguities. The current model does not aim to serve as a catalog of licenses. While it may accommodate basic scenarios, it does not aim to cover the full spectrum of license characteristics, which can vary significantly. Instead, our focus is on providing a general framework for describing relationships and answering the questions exposed above.

Withe the aim of clarifying the model scope, here are some questions that our model does not attempt to answer:

* What are the implications of purchasing a specific entitlement?
* Which entitlement should I acquire to get a specific capability?
* Is license migration feasible?
* What capabilities will be allowed if I install an entitlement in a specific device?
* Features or restrictions that depend on each user. We are not covering this in the current version of this document, but it could be done if we expand the holders indentification.

It is important to remark that the model primarily addresses the commercial utilization of capabilities, rather than access control. For instance, if a network device cannot be configured to use an arbitrary network protocol (e.g. MPLS) due to licensing restrictions, this implies that the organization owning the router (the holder in this scenario) is not permitted to utilize the MPLS feature. This distinction is separate from, for instance, the ability of a specific user to configure MPLS due to access control limitations.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

(Glossary TBP. We need at least formal definitions of "capability" and "entitlement")


# Modeling Capabilities and Entitlements


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
