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
    email: "camilo@gin.ntt.net"
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

It is important to remark that the model primarily addresses the utilization of capabilities, rather than access control. For instance, if a network device cannot be configured to use an arbitrary network protocol (e.g. MPLS) due to licensing restrictions, this implies that the organization owning the router (the holder in this scenario) is not permitted to utilize the MPLS feature. This distinction is separate from, for instance, the ability of a specific user to configure MPLS due to access control limitations.


# Conventions and Definitions

{::boilerplate bcp14-tagged}

(Glossary TBP. We need at least formal definitions of "capability" and "entitlement")


# Modeling Capabilities and Entitlements

The model is intended to facilitate information on all capabilities and entitlements associated with a set of inventoried assets under the same inventory management. In scenarios where entitlements are tied to network elements, the element itself can provide this information. Alternatively, providers may support something similar to a license server, which could house comprehensive information regarding an organization's entitlements. Just by listing capabilities and entitlements, and reading their basic information, a NETCONF/RESTCONF client will be able to retrieve basic inventory information of available capabilities and existing entitlements.

Note that the model uses lists based on classes on multiple parts to be able to extend functionality.

(TBD: Provide examples of how this can be done in posterior releases of this document)

Entitlements and features can be made available by means of  do not specify which the assets (network elments or components) are actually assigned the entitlements, either through an installation or a similar operation. For this, we augment the network elements form the network-inventory [I-D.draft-ietf-ivy-network-inventory-yang-03] model with a new container called entitlement-information. This container hold information of the state of entitlmenets in the asset.

## Capabilities

Capabilities are modeled by augmenting "newtwork-element" in the "ietf-network-inventory" module in {{BaseInventory}} according to the following tree:

~~~
+--rw capabilities
   +--rw capability-class* [capability-class]
      +--rw capability-class                     identityref
      +--rw capability* [capability-id]
         +--rw capability-id                     string
         +--rw extended-feature-description?     string
         +--rw resource-description?             string
         +--rw resource-units?                   string
         +--rw resource-amount?                  int32
         +--rw supporting-entitlements
            +--rw entitlement* [entitlement-id]
            +--rw entitlement-id                 -> ../../../../../entitlements/entitlment/entitlement-id
            +--rw allowed?                       boolean
            +--rw in-use?                        boolean
            +--rw capability-restriction* [capability-restriction-id]
               +--rw capability-restriction-id   string
               +--rw component-id?               -> ../../../../../components/component/component-id
               +--rw description?                string
               +--rw resource-name?              string
               +--rw units?                      string
               +--rw max-value?                  int32
               +--rw current-value?              int32
~~~

The list of capabilities for a given element MAY contain all the associated capabilities provided by the element vendor for such an element, and MUST contain all the capabilities the network service provider operating the network element has acquired an entitlement for, independently of it being active or not.

The capabilities of an inventoried asset may be restricted based on the availability of proper entitlements. An entitlement manager might be interested in the capabilities available to be use on the assets, and the capabilities that are available. The model includes this information by means of the "supporting-entitlements" list, that includes potential restrictions related to the status of the entitlement. This can help organizations stay informed about their entitlement usage and take necessary actions to prevent potential violations or overuse of capabilities.

## Entitlements

As in the case of capabilities, entitlement modeling augments "newtwork-element" in the ietf-network-inventory module in {{BaseInventory}} according to the following tree:

~~~
+--rw entitlements
   +--rw entitlement* [eid]
   +--rw eid                                  string
   +--rw product-id?                          string
   +--rw state?                               entitlement-state-t
   +--rw renewal-profile
   |  +--rw activation-date?                  yang:date-and-time
   |  +--rw expiration-date?                  yang:date-and-time
   +--rw restrictions
   |  +--rw restriction* [restriction-id]
   |  +--rw restriction-id                    string
   |  +--rw description?                      string
   |  +--rw units?                            string
   |  +--rw max-value?                        int32
   |  +--rw current-value?                    int32
   +--rw parent-entitlement-uid?              -> ../entitlement/eid
   +--rw entitlement-attachment
      +--rw universal-access?   boolean
      +--rw holders!
         |  +--rw organizations_names
         |  |  +--rw organizations*           string
         |  +--rw users_names
         |     +--rw users*                   string
         +--rw assets
            +--rw elements
               +--rw network-elements*        string
               +--rw components
                  +--rw component* [network-element component-id]
                     +--rw network-element    string
                     +--rw component-id       string
~~~

Entitlements and assets are linked in the model in two ways. Entitlemenets might be attached to assets, and assets include (or have installed) entitlements. The former way addresses the case of a license server, while the latter considers an entitlement directly associated with the network element. An entitlement that is not inclucded by any asset means that is not being used.

Entitlements may be listed in multiple assets. For instance, a license server, functioning as an asset, might list an entitlement, while the network element entitled by that license might also list it. Proper identification of entitlements is imperative to ensure consistency across systems, enabling monitoring systems to recognize when multiple assets list the same entitlement. Furthermore, there are cases where an authorized asset might not be aware of the covering license. Consider the scenario of a site license, wherein any device under the site may utilize a feature without explicit knowledge of the covering license. In such cases, asset awareness relies on management controls or a service license capable of listing it.

## Entitlement Attachment

The "entitlement" container holds a container called "entitlement-attachement" which relates how the entitlement is operationally linked to holders or assets. Note that there is a difference between an entilement being attached to an asset and an entilement being installed in the asset. In the former, we mean that the license was issued only for one (or more) assets. Some licenses actually can be open but have a limited number of installation. Other licenses might be openly constraint to geography localtion. We are not deailing with these complex cases now, but the container can be expanded for this in the future.

The model accommodates listing entitlements acquired by the organization but not yet applied or utilized by any actor/asset. For these pending entitlements, logistical constraints may arise in informing their existence, as there must be at least one element exporting the model that is aware of their existence.

Some entitlements are inherently associated with a holder, such as organization or an user. For example, a software license might be directly attached to a user. Also, the use of a network device might come with a basic license provided solely to an organization. Some entitlements could be assigned to a more abstract description of holders, such as people under a juristiction a geographical area. The model contains basic information about this, but it can be extended in the future to be more descriptive.

While attachment is optional, the model should be capable of expressing attachment in various scenarios. The model can be expanded to list to which assets an entitlement is aimed for, when this link is more vague, such as a site license (e.g., assets located in a specific site), or more open licenses (e.g., free software for all users subscribed to a streaming platform).

It is important to note that the current model does not provide information on whether an entitlement can be reassigned to other devices (e.g., fixed or floating license). Such scenarios fall under the "what if" category, which is not covered by this model.

## Model Definition

TBP


# Use cases

This section will describe use cases, an example of how they could be modelled by the model, and show how each of the questions that we have explored in this draft can be answered by the model.

(TBP in next versions)


# IANA Considerations

(TBP)


# Security Considerations

(TBP)


--- back

# Acknowledgments
{:numbered="false"}

This document is based on work partially funded by the the EU Horizon Europe projects ACROSS (grant 101097122), ROBUST-6G (grant 101139068), iTrust6G (grant 101139198), MARE (grant 101191436), and CYBERNEMO (grant 101168182).
