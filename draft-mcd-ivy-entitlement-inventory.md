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

Here we go...


--- back

# Acknowledgments
{:numbered="false"}

TODO acknowledge.
