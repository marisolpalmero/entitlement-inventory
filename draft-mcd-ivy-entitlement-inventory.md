---
title: "A YANG Module for Entitlement Inventory"
abbrev: "entitlement-inventory"
category: std
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
    organization: Independent
    email: "marisol.ietf@gmail.com"
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

This document proposes a YANG module for incorporating entitlements in a network inventory, encompassing both virtual and physical network elements. Entitlements define the rights for their holder to use specific capabilities in a network element(s). The model is rooted by the concept of the capabilities offered by an element, enabled by the held entitlements, and considers entitlement scope, how they are assigned, and when they expire. The model introduces a descriptive definition of capabilities and the entitlement use restrictions, supporting entitlement administration and the understanding of the capabilities available through the network.

--- middle

# Introduction

The purpose of any network elements included as assets in the inventory of any network operator is to leverage their capabilities to build network services. Many of these capabilities are not automatically enabled upon acquisition; their use may require specific rights—typically provided via entitlements or licenses from the vendor.

The primary intent of this draft is to support three key operational use cases in managing software entitlements and network capabilities:

- Listing entitlements (e.g., licenses) available across the operator organization, their holders, and applicable scope.

- Modeling the capabilities that entitlements permit or enable, representing what a network element may do when properly licensed.

- Representing the actual use of capabilities, including any active restrictions or limits defined by the associated entitlements.

Together, these use cases enable administrators to answer essential questions such as: What can this device do? What is it currently allowed to do? And what is it actively doing within the bounds of licensing or entitlement constraints? This approach supports not only entitlement tracking but also intent-aware control of device behavior and resource exposure.

As network technology evolves toward modular, software-defined, and virtualized architectures, managing the rights to activate specific functions becomes increasingly complex. These rights granted via entitlements or licenses must be tracked, aggregated, and matched to assets to ensure that services can be delivered using available capabilities. This complexity calls for structured, machine-readable models that represent which capabilities are available, permitted, and in use.

To address this, the model relies on two core concepts: capability and entitlement. A capability represents what a system or component may do; an entitlement grants permission to use one or more of those capabilities, possibly under constraints such as time, scope, or usage limits. Being able to represent and exchange this information across systems helps automate entitlement administration and simplify operational decisions.

This draft provides a foundational YANG structure for representing these relationships as standards, complementing the network inventory module.

## Scope of the Entitlement Model

The entitlement model aims to provide an inventory of entitlements. This includes the entitled holders and the capabilities to which they are entitled. Additionally, it offers information into the restrictions of the operation of the different assets (network entities and components). In general, this model seeks to address the following questions:

* What entitlements are administered/owned by the organization?
* How are entitlements restricted to some assets and holders?
* What entitlements are assigned or granted on each asset(s)?
* What constraints do the current entitlements impose on the assets' functionality?
* Does the entitlement impose any kind of global restrictions? What are they?
* What are the restrictions that each asset due to the entitlements it holds?

The model is designed with flexibility in mind, allowing for expansion through the utilization of tools provided by YANG.

The realm of entitlements and licensing is inherently complex, presenting challenges in creating a model that can comprehensively encompass all scenarios without ambiguity. While we attempt to address various situations through examples and use cases, we acknowledge that the model might not be able to cover all corner cases without ambiguity. In such cases, we recommend that implementations provide additional documentation to clarify those potential ambiguities. The current model does not aim to serve as a catalog of licenses. While it may accommodate basic scenarios, it does not aim to cover the full spectrum of license characteristics, which can vary significantly. Instead, our focus is on providing a general framework for describing relationships and answering the questions posed above.

With the aim of clarifying the model scope, here are some questions that our model does not attempt to answer:

* What are the implications of purchasing a specific entitlement?
* Which entitlement is needed to obtain a specific capability?
* Is license migration feasible?
* What capabilities are permitted when an entitlement is installed in a specific device?
* Features or restrictions that depend on each user. We are not covering this in the current version of this document, but it could be done if we expand the holders' identification.

This model focuses on the ability to use capabilities, not on access control mechanisms. For example, if a router cannot enable MPLS due to entitlement restrictions, it means the organization lacks the rights to use that capability—even if access to the device itself is available. This distinction is separate from, for instance, the ability of a specific user to configure MPLS due to access control limitations.

## Pre-Provisioned vs. Discovered Entitlements

This model is not intended for automatic discovery of entitlements or capabilities through the network elements themselves. Instead, it assumes that entitlements and their associations are either:

- Provisioned in a license server or asset database;
- Installed on individual devices and reported through management interfaces; or
- Manually configured as part of an inventory process.

Future augmentations may explore capability discovery or telemetry driven models, but they are out of scope for the current version.



# Conventions and Definitions

{::boilerplate bcp14-tagged}

* ToBeUpdated(TBU) Open Issue for the IVY WG, to include:

<<Update Glossary under  Network Inventory draft, {{!I.D.draft-ietf-ivy-network-inventory-yang}}. We need at least formal definitions of "capability" and "entitlement".>>

- Capability: A function or resource that a network element can support or execute.
- Entitlement: A right granted to a holder (organization or user) to access or activate specific capabilities under defined conditions.

# Modeling Capabilities and Entitlements

The model describes how to represent capabilities and the entitlements that enable them across inventoried assets. Capabilities describe what a device may do. Entitlements indicate whether those capabilities are allowed and under what conditions.

In deployments where entitlements are directly associated with specific network elements, the devices themselves may expose entitlement information. Alternatively, some environments may rely on a centralized license server that maintains the entitlements of an organization. By querying the list of capabilities and entitlements, along with their associated metadata, a NETCONF or RESTCONF client can retrieve essential inventory details about what capabilities are available and which entitlements are currently in place.

Note that the model uses lists based on classes on multiple parts to be able to extend functionality.

(TBD: Provide examples of how this can be done in future releases of this document)

Capabilities and entitlements may be listed without explicitly identifying the assets (network elements or components) they apply to. To support assignment, the network inventory {{BaseInventory}} should be augmented with two new containers referred as capabilities and entitlements. These containers hold information of the state of capabilities and entitlements in the asset(s).

## Capabilities

Capabilities are modeled by augmenting "network-element" in the "ietf-network-inventory" module in {{BaseInventory}} according to the following tree:

~~~
+--ro capabilities
   +--ro capability-class* [capability-class]
      +--ro capability-class                     identityref
      +--ro capability* [capability-id]
         +--ro capability-id                     string
         +--ro extended-capability-description?     string
         +--ro resource-description?             string
         +--ro resource-units?                   string
         +--ro resource-amount?                  int32
         +--ro supporting-entitlements
            +--ro entitlement* [entitlement-id]
            +--ro entitlement-id                 -> ../../../../../entitlements/entitlement/entitlement-id
            +--ro allowed?                       boolean
            +--ro in-use?                        boolean
            +--ro capability-restriction* [capability-restriction-id]
               +--ro capability-restriction-id   string
               +--ro component-id?               -> ../../../../../components/component/component-id
               +--ro description?                string
               +--ro resource-name?              string
               +--ro units?                      string
               +--ro max-value?                  int32
               +--ro current-value?              int32
~~~

For any given element, the capabilities list MAY include all potential capabilities advertised by the vendor, and MUST include those for which the network operator holds a valid entitlement—whether active or not.

The capabilities of an inventoried asset may be restricted based on the availability of proper entitlements. An entitlement manager might be interested in the capabilities available to be use on the assets, and the capabilities that are available. The model includes this information by means of the "supporting entitlements" list, that includes potential restrictions related to the status of the entitlement. This allows organizations to monitor entitlement usage and avoid misconfigurations or exceeding permitted capability limits.

## Entitlements

As in the case of capabilities, entitlement modeling augments "network-element" in the ietf-network-inventory module in {{BaseInventory}} according to the following tree:

~~~
+--ro entitlements
   +--ro entitlement* [eid]
   +--ro eid                                  string
   +--ro product-id?                          string
   +--ro state?                               entitlement-state-t
   +--ro renewal-profile
   |  +--ro activation-date?                  yang:date-and-time
   |  +--ro start-date?                       yang:date-and-time
   |  +--ro expiration-date?                  yang:date-and-time
   +--ro restrictions
   |  +--ro restriction* [restriction-id]
   |  +--ro restriction-id                    string
   |  +--ro description?                      string
   |  +--ro units?                            string
   |  +--ro max-value?                        int32
   |  +--ro current-value?                    int32
   +--ro parent-entitlement-uid?              -> ../entitlement/eid
   +--ro entitlement-attachment
      +--ro universal-access?   boolean
      +--ro holders!
         |  +--ro organizations_names
         |  |  +--ro organizations*           string
         |  +--ro users_names
         |     +--ro users*                   string
         +--ro assets
            +--ro elements
               +--ro network-elements*        string
               +--ro components
                  +--ro component* [network-element component-id]
                     +--ro network-element    string
                     +--ro component-id       string
~~~

Entitlements and assets are linked in the model in two ways. Entitlements might be attached to assets, and assets include (or have granted) entitlements. The former way addresses the case of a license server, while the latter considers an entitlement directly associated with the network element. An entitlement that is not included by any asset means that it is not being used.

Entitlements may be listed in multiple assets. For instance, a license server, functioning as an asset, might list an entitlement, while the network element entitled by that license might also list it. Proper identification of entitlements is imperative to ensure consistency across systems, enabling monitoring systems to recognize when multiple assets list the same entitlement. Furthermore, there are cases where an authorized asset might not be aware of the covering license. Consider the scenario of a site license, wherein any device under the site may utilize a feature without explicit knowledge of the covering license. In such cases, asset awareness relies on management controls or a service license capable of listing it.

### Reverse Mapping from Entitlements to Capabilities

While the model includes links from capabilities to supporting entitlements, some inventory operators may need to evaluate entitlements independently and identify the capabilities they enable.

To support this, implementers may use the `product-id` or `capability-class` metadata along with external references or catalogs. A reverse mapping structure may be introduced in a future version of the model, once a reliable binding syntax for entitlement to capability is standardized.

## Entitlement Attachment

The "entitlement" container holds a container called "entitlement-attachment" which relates how the entitlement is operationally linked to holders or assets. Note that there is a difference between an entitlement being attached to an asset and an entitlement being installed in the asset. In the former, the license was explicitly associated with one or more assets. Some licenses actually can be open but have a limited number of installation. Other licenses might be openly constrained to a geographic location. We are not dealing with these complex cases now, but the container can be expanded for this in the future.

The model accommodates listing entitlements acquired by the organization but not yet applied or utilized by any actor/asset. For these pending entitlements, logistical constraints may arise in informing their existence, as there must be at least one element exporting the model that is aware of their existence.

Some entitlements are inherently associated with a holder, such as organization or a user. For example, a software license might be directly attached to a user. Also, the use of a network device might come with a basic license provided solely to an organization. Some entitlements could be assigned to a more abstract description of holders, such as people under a jurisdiction or a geographical area. The model contains basic information about this, but it can be extended in the future to be more descriptive.

While attachment is optional, the model should be capable of expressing attachment in various scenarios. The model can be expanded to list to which assets an entitlement is aimed for, when this link is more vague, such as a site license (e.g., assets located in a specific site), or more open licenses (e.g., free software for all users subscribed to a streaming platform).

It is important to note that the current model does not provide information on whether an entitlement can be reassigned to other devices. Such scenarios fall under the "what if" category, which is not covered by this model.

## Model Definition

TBP


# Use cases and Examples

This section describes use cases, provide an example of how they could be modeled by the model, and show how each of the questions that we have explored in this draft can be answered by the model.

## MPLS Capability License on a Network OS
An operator installs a software license (entitlement) enabling MPLS routing on a NOS. The license is attached to a specific device and activates the "mpls-routing" capability class.


```json
"entitlement": {
  "eid": "mpls-license-001",
  "product-id": "mpls-software-lic-v2",
  "renewal-profile": {
    "activation-date": "2025-01-01T00:00:00Z",
    "expiration-date": "2026-01-01T00:00:00Z"
  },
  "entitlement-attachment": {
    "holders": {
      "organizations_names": {
        "organizations": ["ACME Corp"]
      },
      "assets": {
        "elements": {
          "network-elements": ["router-5"]
        }
      }
    }
  }
}

"capability": {
  "capability-id": "mpls-routing",
  "capability-class": "routing",
  "supporting-entitlements": {
    "entitlement": [
      {
        "entitlement-id": "mpls-license-001",
        "allowed": true,
        "in-use": true
      }
    ]
  }
}
```

## Bandwidth Upgrade via License

A vendor-N device uses a capacity license to expand throughput.

```json
"entitlement": {
  "eid": "vendorN-bw-10g",
  "product-id": "vendorN-bw-upgrade",
  "restrictions": {
    "restriction": [
      {
        "restriction-id": "cap",
        "description": "Bandwidth cap",
        "units": "Gbps",
        "max-value": 10,
        "current-value": 6
      }
    ]
  }
}

"capability": {
  "capability-id": "bw-capability",
  "resource-description": "Licensed bandwidth",
  "resource-units": "Gbps",
  "resource-amount": 10,
  "supporting-entitlements": {
    "entitlement": [
      {
        "entitlement-id": "vendorN-bw-10g",
        "allowed": true,
        "in-use": true
      }
    ]
  }
}
```

## Floating License Managed by License Server

A shared entitlement is held by a license server and consumed dynamically by multiple switches.

```json
"entitlement": {
  "eid": "shared-switch-license-1",
  "entitlement-attachment": {
    "universal-access": true,
    "holders": {
      "organizations_names": {
        "organizations": ["NTT"]
      }
    },
    "assets": {
      "elements": {
        "network-elements": ["switch-1", "switch-2", "switch-3"]
      }
    }
  }
}
```

This entitlement may be tracked across devices using a `license server` asset that records usage or seat count (future extension).


# IANA Considerations

(TBP)


# Security Considerations

(TBP)


--- back

# Acknowledgments
{:numbered="false"}

This document is based on work partially funded by the EU Horizon Europe projects ACROSS (grant 101097122), ROBUST-6G (grant 101139068), iTrust6G (grant 101139198), MARE (grant 101191436), and CYBERNEMO (grant 101168182).
