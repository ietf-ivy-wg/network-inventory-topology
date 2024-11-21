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

  This document defines a YANG model to
   map the network inventory data with the topology model
   to form a base underlay network. The model facilitates the
   correlation between the layer (e.g.,  Layer 2 and Layer 3) topology information
   and the inventory data of the underlay network for better service
   provisioning and network maintenance operations.

--- middle

# Introduction

 {{!I-D.ietf-ivy-network-inventory-yang}} defines base Network Inventory
   (NI) model to aggregate the inventory data of the Network Elements
   (NEs) on the network, which includes NEs and their hardware
   components, firmware components, and software components.  Examples
   of inventory hardware components could be rack, shelf, slot, board
   and physical port.  Examples of inventory software components could
   be platform operating system (OS), software-patch, bios, and boot-
   loader.

This document extends the {{!RFC8345}} network topology model for network
   inventory mapping, which facilitates the correlation with existing
   network and topology models, such as SAP {{!RFC9408}}, L2 topology
   {{!RFC8944}}, and L3 topology {{!RFC8346}}, to support agile service
   provisioning and network maintenance.

In addition, the network inventory topology can also provide anchor
   points to mount specific device configuration and state information,
   e.g.  QoS policies, ACL policies, to support configuration
   verification of cross-domain policies.

The YANG data model in this document conforms to the Network
Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Editorial Note (To be removed by RFC Editor)

Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

  * XXXX --> the assigned RFC number for this I-D
  * AAAA --> the assigned RFC number for {{!I-D.ietf-ivy-network-inventory-yang}}

# Conventions and Definitions

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses terms defined in {{!I-D.ietf-ivy-network-inventory-yang}}.

# Sample Use Cases of the Data Model

## Determine Available Resource of Service Attachment Points (SAPs)

The inventory topology model can be used as a base to correlate
   underlay information, such as physical port components.  The figure
   belows gives an example of the usage.

 During service provisioning, to check available physical port
   resources, the Service Attachment Points (SAPs) information can be
   associated with the underlay inventory information and interface
   information associated with the inventory topology, e.g.  "parent-
   termination-point" of SAP Model can be associated with the "port-
   component-ref" and "interface-name" of the inventory topology model,
   which can be used to check the availability and capacity of physical
   ports.



~~~~ ascii-art
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



## The Example Digital Twin Network

 {{!I-D.irtf-nmrg-network-digital-twin-arch}} defines "digital twin
   network" as a virtual representation of the physical network.  Such
   virtual representation of the network is meant to be used to analyze,
   diagnose, emulate, and then manage the physical network based on
   data, models, and interfaces.

The management system can use digital twin technology to build visual
   multi-layer topology maps for networks and endpoints with
   relationship types and dependencies, and identify potential impacts
   on configuration management information from incidents, problems, and
   changes.

The inventory topology model can, for example, be used to emulate
   several what-if scenario such as the impact of EOL or depletion of a
   hardware component on the network resilience and service
   availability.

# Module Tree Structure

 An overview of the data model for "ietf-network-inventory-topology" module are shown in {{tree}}.

~~~~~~~~~~
{::include-fold ./yang/full-trees/ietf-network-inventory-topology.tree}
~~~~~~~~~~
{: #tree title="The Structure of the Network Inventory Mapping Data Model" artwork-align="center"}

The module augments the original "ietf-network- topology" modules as
   follows:

* A new network topology type: "network-inventory-mapping".  The
        corresponding container augments the network-types of the "ietf-
        network" module.

* Inventory mapping attributes for nodes, links, and termination
        points: The corresponding containers augments the topology module
        with the references to the base network inventory, references to
        interface management, and system mount points

   Being an independent underlaying topology, the inventory topology
   model associates inventory data with overlay topologies.  It can be
   used as the "supporting-networks" of SAP, Layer 2, or Layer 3
   topologies.

# Network Inventory Topology YANG Module {#sec-module}

This modules augments the Network Topology {{!RFC8345}}.

This module import the base network inventory {{!I-D.ietf-ivy-network-inventory-yang}}.

~~~~ yang
{::include-fold ./yang/ietf-network-inventory-topology.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-network-inventory-topology@2024-04-29.yang"}

# Security Considerations

This section uses the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

   The YANG module specified in this document defines schema for data
   that is designed to be accessed via network management protocols such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}.  The lowest NETCONF layer
   is the secure transport layer, and the mandatory-to-implement secure
   transport is Secure Shell (SSH) {{!RFC6242}}.  The lowest RESTCONF layer
   is HTTPS, and the mandatory-to-implement secure transport is TLS
   {{!RFC8446}}.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   There are a number of data nodes defined in this YANG module that are
   writable/creatable/deletable (i.e., config true, which is the
   default).  These data nodes may be considered sensitive or vulnerable
   in some network environments.  Write operations (e.g., edit-config)
   and delete operations to these data nodes without proper protection
   or authentication can have a negative effect on network operations.
   Specifically, the following subtrees and data nodes have particular
   sensitivities/vulnerabilities:

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
