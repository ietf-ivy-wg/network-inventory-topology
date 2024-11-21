---
title: "A Network Inventory Topology Model"
abbrev: "Network Inventory Topology"
category: std

docname: draft-ietf-ivy-network-inventory-topology-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "Network Inventory YANG"
keyword:

 - Automation
 - Network Digital Map
 - Network Inventory
 - Network Operation
 - Network Topology

author:
 -
    fullname: Bo Wu
    organization: Huawei
    role: editor
    email: lana.wubo@huawei.com

 -
    fullname: Cheng Zhou
    organization: China Mobile
    email: zhouchengyjy@chinamobile.com

 -
    fullname: Qin Wu
    organization: Huawei
    email: bill.wu@huawei.com

 -
    fullname: Mohamed Boucadair
    organization: Orange
    email: mohamed.boucadair@orange.com

contributor:
-
   fullname: Chaode Yu
   organization: Huawei
   email: yuchaode@huawei.com

normative:

informative:

--- abstract

  This document defines a YANG model for network inventory topology to
   correlate the network inventory data with the general topology model
   to form a base underlay network, which can facilitate the mapping and
   correlation of the layer (e.g.  Layer 2, Layer 3) topology information
   above to the inventory data of the underlay network for agile service
   provisioning and network maintenance analysis.

--- middle

# Introduction

 {{!I-D.ietf-ivy-network-inventory-yang}} defines the base Network Inventory
   (NI) model to aggregate the inventory data of Network Elements
   (NEs) in a network. This data includes identification of these NEs and their hardware
   , firmware, and software components.  Examples
   of inventory hardware components could be rack, shelf, slot, board,
   or physical port.  Examples of inventory software components could
   be platform Operating System (OS), software-patches, bios, or boot-
   loader.

In order to ease navigation from/to inventory and network topologies,
this document extends the network topology model {{!RFC8345}} for network
   inventory mapping: "ietf-network-inventory-topology" ({{sec-module}}).  This model aims to facilitate the correlation with existing
   network and topology models, such as SAP {{!RFC9408}}, Layer 2 topology
   {{?RFC8944}}, and Layer 3 topology {{?RFC8346}}.

The network inventory topology ("ietf-network-inventory-topology") also provides anchor
   points to mount specific device configuration and state information,
   e.g.,  Quality of Service (QoS) and Access Control List (ACL) policies, to support configuration
   verification of policies at the network level.

The YANG data model in this document conforms to the Network
Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

# Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

  * XXXX --> the assigned RFC number for this I-D
  * AAAA --> the assigned RFC number for {{!I-D.ietf-ivy-network-inventory-yang}}

# Conventions and Definitions

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses terms defined in {{!I-D.ietf-ivy-network-inventory-yang}}.

# Sample Use Cases of the Data Model

## Determine Available Resources of Service Attachment Points (SAPs)

The inventory topology model can be used as a base to correlate
   underlay information, such as physical port components.  {{nwi-topology-usage}} exemplifies this usage.

 During service provisioning, to check available physical port
   resources, the Service Attachment Points (SAPs) information can be
   associated with the underlay inventory information and interface
   information associated with the inventory topology, e.g.,
   "parent-termination-point" of SAP Model can be associated with the
   "port-component-ref" and "interface-name" of the inventory topology model,
   which can be used to check the availability and capacity of physical
   ports.

~~~~ aasvg
                        +-----------------+
                        |     Customer    |
                        +--------+--------+
        Customer Service Models  |
           (e.g., L3SM, L2SM)    |
                        +--------+--------+
                        |    Service      |
                        |  Orchestration  |
                        +------+---+------+
                               |   |
             SAP Network Model |   | Inventory Topology Model
                        +------+---+------+
                        |     Network     |
                        |   Controller    |
                        +--------+--------+
                                 |
           +---------------------+---------------------+
           |                  Network                  |
           +-------------------------------------------+
~~~~
{: #nwi-topology-usage title="An Example Usage of Network Inventory Topology" artwork-align="center"}

## "What-if" Scenarios

 {{?I-D.irtf-nmrg-network-digital-twin-arch}} defines Network Digital Twin (NDT)
   as a virtual representation of the physical network.  Such
    representation is meant to be used to analyze,
   diagnose, emulate, and then manage the physical network based on
   data, models, and interfaces.

The management system can use NDT to build
   multi-layer topology maps for networks and endpoints with
   relationship types and dependencies, and identify potential impacts
   on configuration management information from incidents, problems, and
   changes.

The inventory topology model can, for example, be used to emulate
   several "what-if" scenarios such as the impact of End of Life (EOL) or depletion of a
   hardware component (chipset) on the network resilience and service
   availability.

# Module Tree Structure

 An overview of the data model for "ietf-network-inventory-topology" module are shown in {{tree}}.

~~~~~~~~~~
{::include-fold ./yang/full-trees/ietf-network-inventory-topology.tree}
~~~~~~~~~~
{: #tree title="The Structure of the Network Inventory Mapping Data Model" artwork-align="center"}

The module augments the "ietf-network- topology" module as
   follows:

* A new network topology type: "network-inventory-mapping".  The
        corresponding container augments the "network-types" of the "ietf-
        network" module.

* Inventory mapping attributes for nodes, links, and termination
        points: The corresponding containers augments the topology module
        with the references to the base network inventory, references to
        interface management, and policy mount points

   The inventory topology
   model associates inventory data with overlay topologies.  It can be
   used as the "supporting-networks" of SAP, Layer 2, or Layer 3
   topologies.

# Network Inventory Topology YANG Module {#sec-module}

This module augments the Network Topology {{!RFC8345}}.

This module imports the base network inventory {{!I-D.ietf-ivy-network-inventory-yang}}.

~~~~ yang
{::include-fold ./yang/ietf-network-inventory-topology.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-network-inventory-topology@2024-04-29.yang"}

# Security Considerations

This section is modeled after the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

The "ietf-network-inventory-topology" YANG module defines a data model that is
designed to be accessed via YANG-based management protocols, such as
NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}. These protocols have to
use a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and
QUIC {{?RFC9000]) and have to use mutual authentication.

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are
writable/creatable/deletable (i.e., "config true", which is the
default).  All writable data nodes are likely to be reasonably
sensitive or vulnerable in some network environments.  Write
operations (e.g., edit-config) and delete operations to these data
nodes without proper protection or authentication can have a negative
effect on network operations.  The following subtrees and data nodes
have particular sensitivities/vulnerabilities:

Some of the readable data nodes in this YANG module may be considered
   sensitive or vulnerable in some network environments.  It is thus
   important to control read access (e.g., via get, get-config, or
   notification) to these data nodes.  Specifically, the following
subtrees and data nodes have particular sensitivities/vulnerabilities:

   'ne-ref':
   :  The reference may be used to track the set of network elements.

# IANA Considerations

   IANA is requested to register the following URI in the "ns" subregistry within
   the "IETF XML Registry" {{!RFC3688}}:

~~~~
   URI:  urn:ietf:params:xml:ns:yang:ietf-network-inventory-topology
   Registrant Contact:  The IESG.
   XML:  N/A; the requested URI is an XML namespace.
~~~~

   IANA is requested to register the following YANG module in the "YANG Module
   Names" registry {{!RFC6020}} within the "YANG Parameters" registry group:

~~~~
   Name:  ietf-network-inventory-topology
   Namespace:  urn:ietf:params:xml:ns:yang:ietf-network-inventory-topology
   Prefix:  nwit
   Maintained by IANA?  N
   Reference:  RFC XXXX
~~~~

--- back

# Acknowledgments
{:numbered="false"}

The authors wish to thank Italo Busi, Olga Havel, Aihua Guo, Oscar
   Gonzalez de Dios, and many others for their helpful comments and
   suggestions.
