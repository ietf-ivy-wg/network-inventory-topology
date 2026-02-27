---
title: "A YANG Network Data Model for Inventory Topology Mapping"
abbrev: "Inventory Topology Mapping"
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
    fullname: Mohamed Boucadair
    organization: Orange
    email: mohamed.boucadair@orange.com

 -
    fullname: Cheng Zhou
    organization: China Mobile
    email: zhouchengyjy@chinamobile.com

 -
    fullname: Qin Wu
    organization: Huawei
    email: bill.wu@huawei.com

contributor:
-
   fullname: Chaode Yu
   organization: Huawei
   email: yuchaode@huawei.com

normative:

informative:

--- abstract

   This document defines a YANG data model to
   map the network inventory data with the topology data
   to form a base underlay network. The data model facilitates the
   correlation between the layer (e.g.,  Layer 2 or Layer 3) topology information
   and the inventory data of the underlay network for better service
   provisioning, network maintenance operations, and other assessment scenarios.

--- middle

# Introduction

 {{!I-D.ietf-ivy-network-inventory-yang}} defines the base network inventory
  model to aggregate the inventory data of Network Elements (NEs). This data includes identification of these NEs and their hardware,
  firmware, and software components.  Examples
   of inventory hardware components could be rack, shelf, slot, board,
   or physical port.  Examples of inventory software components could
   be platform Operating System (OS), software-modules, bios, or boot-loader {{?I-D.ietf-ivy-network-inventory-software}}.

In order to ease navigation from (or to) inventory and network topologies,
this document extends the network topology data model {{!RFC8345}} for network
inventory mapping: "ietf-network-inventory-topology" ({{sec-module}}).  This data model provides a mechanism for the correlation with existing
network and topology data models, such as "A YANG Network Data Model for Service Attachment Points (SAPs)" {{!RFC9408}}, "A YANG Data Model for Layer 2 Network Topologies" {{?RFC8944}}, and "A YANG Data Model for Layer 3 Topologies" {{?RFC8346}}.

Similar to the base inventory data model  {{!I-D.ietf-ivy-network-inventory-yang}}, the network inventory topology
does not make any assumption about involved NEs and their roles in topologies. As such, the mapping
model can be applied independent of the network type (optical local loops, access network, core network, etc.) and application.

## Editorial Note (To be removed by RFC Editor)

> Note to the RFC Editor: This section is to be removed prior to publication.

This document contains placeholder values that need to be replaced with finalized values at the time of publication. This note summarizes all of the substitutions that are needed.

Please apply the following replacements:

  * XXXX --> the assigned RFC number for this I-D
  * AAAA --> the assigned RFC number for {{!I-D.ietf-ivy-network-inventory-yang}}

# Conventions and Definitions

The meanings of the symbols in the YANG tree diagrams are defined in {{?RFC8340}}.

This document uses terms defined in {{!I-D.ietf-ivy-network-inventory-yang}}.

# Sample Use Cases of the Data Model {#sample}

## Determine Available Resources of Service Attachment Points (SAPs)

The inventory topology data model can be used as a basis for correlating
   underlay information, such as physical port components.  {{nwi-topology-usage}} exemplifies this usage.

 During service provisioning, to check available physical port
   resources, the SAPs information can be
   associated with the underlay inventory information and interface
   information associated with the inventory topology, e.g.,
   "parent-termination-point" of SAP Model can be associated with the
   "port-component-ref" of the inventory topology data model,
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

 {{?I-D.ietf-nmop-simap-concept}} defines Service and Infrastructure Maps (SIMAP)
 as an abstraction model that provides a unified view of both service and
 infrastructure information, enabling correlation between service requirements
 and underlying resource capabilities.

Both architectures require accurate mapping between logical network topology
 and physical inventory as a foundational data layer. This model provides
 the essential physical resource information to such systems, enabling
 them to perform accurate "what-if" analysis (e.g., impact prediction
 of hardware EOL, path re-optimization under resource constraints, service
 availability assessment).

# Module Tree Structure

 An overview of the structure of the "ietf-network-inventory-topology" module is shown in {{tree}}.

~~~~~~~~~~
{::include-fold ./yang/full-trees/ietf-network-inventory-topology.tree}
~~~~~~~~~~
{: #tree title="The Structure of the Network Inventory Mapping Data Model" artwork-align="center"}

The module defines two features "inventory-to-topology-navigate" and "topology-to-inventory-navigate"
to control the navigation direction (from topology to inventory and vice versa).

The module augments the "ietf-network-topology" module as follows:

* Inventory mapping attributes for nodes, links, and termination
        points: The corresponding containers augments the topology module
        with the references to the base network inventory

   The inventory topology
   model associates inventory data with overlay topologies.  It can be
   used as the "supporting-networks" of SAP, Layer 2, or Layer 3
   topologies.

## Link Extensions

This document adds a lightweight "link-type" leaf to the topology link mapping to enable basic physical media classification.


- "link-type" – A string indicating the link media type, such as
   "copper", "fiber", or "coax". For wireless media, values such as "microwave", or "wifi" may be used

The "link-type" serves as a lightweight discriminator that guides to the
 appropriate specialized inventory model for detailed resource information.
 For example, wired media (fiber, copper) typically reference a passive
 network inventory model, such as the one defined in {{?I-D.ygb-ivy-passive-network-inventory}}.

## Port-Breakout Capability

High-density Ethernet ports (e.g., 400 Gb/s DR4) can be split into
multiple independent lower-speed channels. The breakout channels
represent the intrinsic capability of the port to be partitioned,
regardless of whether the port is currently configured as a trunk or as
a breakout port.

A trunk port is associated with exactly one physical interface.
A breakout port is a port that is decomposed into two or more physical
interfaces; those interfaces may run at the same or different speeds
and may consume the same or a different number of breakout channels.

The container "port-breakout" is added under the termination-point
augmentation.  It lists the logical channels into which the single
physical port can be divided.  Only termination-points whose parent
port is breakout-capable need to instantiate the container; otherwise
the container is omitted, keeping the topology model minimal for the
common non-breakout case.

Breakout channel is an atomic resource element obtained by partitioning a breakout port.
One physical interface may be associated with one or more breakout
channels, but one breakout channel MUST NOT be associated with more
than one physical interface. Appendix B provides example configurations.

It is assumed that a port which supports breakout can be configured
either as a trunk port or as a breakout port. Interface channelisation (e.g., VLAN sub-interfaces) is outside the scope of this document and is addressed by the Layer 2 network topology model {{?RFC8944}}.

# Network Inventory Topology YANG Module {#sec-module}

This module augments the Network Topology {{!RFC8345}}.

This module imports the base network inventory {{!I-D.ietf-ivy-network-inventory-yang}}.

~~~~ yang
{::include-fold ./yang/ietf-network-inventory-topology.yang}
~~~~
{: sourcecode-markers="true" sourcecode-name="ietf-network-inventory-topology@2025-10-20.yang"}

# Operational Considerations

This model enables a network controller to report discovered network topology and inventory information. Automatic discovery serves as the primary mechanism, with selective configuration capabilities provided for scenarios where discovery is not feasible.

For typical operations such as service provisioning and network planning, the model offers read-only query access to authoritative mappings between logical topology and physical inventory.
The inventory-mapping-attributes containers are defined as read-write (config true) to accommodate cases where automatic discovery is not possible, including:

- Customer-premises equipment (CPE) outside the operator's management domain
- Leased lines and third-party transport resources
- Planned or hypothetical resources for future deployment

In these cases, the operator manually configures the mapping to maintain accurate topology-to-inventory correlation.

The following nodes are read-only (config false) as they represent hardware-determined state:

port-breakout: Hardware capability determined by physical port characteristics

# Security Considerations

This section is modeled after the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

The "ietf-network-inventory-topology" YANG module defines a data model that is
designed to be accessed via YANG-based management protocols, such as
NETCONF {{?RFC6241}} and RESTCONF {{?RFC8040}}. These YANG-based management (1) have to
use a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and
QUIC {{?RFC9000]) and (2) have to use mutual authentication.

The Network Configuration Access Control Model (NACM) {{!RFC8341}}
provides the means to restrict access for particular NETCONF or
RESTCONF users to a preconfigured subset of all available NETCONF or
RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are
   writable/creatable/deletable (i.e., "config true", which is the
   default).  All writable data nodes are likely to be sensitive or
   vulnerable in some network environments.  Write
   operations (e.g., edit-config) and delete operations to these data
   nodes without proper protection or authentication can have a negative
   effect on network operations.  The following subtrees and data nodes
   have particular sensitivities/vulnerabilities:

  'ne-ref', 'port-ref', 'link-type':
  These nodes are sensitive as they establish the mapping
  between logical topology and physical inventory. Unauthorized
  modification could lead to incorrect resource allocation or
  service disruption.

Some of the readable data nodes in this YANG module may be considered
sensitive or vulnerable in some network environments.  It is thus
important to control read access (e.g., via get, get-config, or
notification) to these data nodes. Specifically, the following
subtrees and data nodes have particular sensitivities/
vulnerabilities:

   'ne-ref':
   The references may be used to track the set of network elements. While
   read-only, they may reveal network infrastructure details.

   'port-breakout':
   This node exposes hardware capabilities.

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

# "link-type" Usage Examples

This appendix provides examples illustrating the usage of the
 link-type data node.

Scenario: Device SW-1 and device SW-2 are directly connected by a fiber.

Physical topology:

~~~~ aasvg
+--------+                                    +--------+
|        |                                    |        |
| [SW-1] +========= fiber link ===============+ [SW-2] |
|        |                                    |        |
+--------+                                    +--------+
~~~~

Key parts of the JSON example is as follows:

~~~~ JSON
{
  "ietf-network:networks": {
    "network": [
      {
        "network-id": "campus-topology",
        "node": [
          {
            "node-id": "SW-1",
            "ietf-network-inventory-topology:inventory-mapping-attributes": {
              "ne-ref": "NE-SW1"
            },
            "ietf-network-topology:termination-point": [
              {
                "tp-id": "TP-SW1-P1",
                "ietf-network-inventory-topology:inventory-mapping-attributes": {
                  "ne-ref": "NE-SW1",
                  "port-ref": "/nwi:network-inventory/nwi:network-elements/nwi:network-element[ne-id='NE-SW1']/nwi:components/nwi:component[component-id='eth-port-1']"
                }
              }
            ]
          },
          {
            "node-id": "SW-2",
            "ietf-network-inventory-topology:inventory-mapping-attributes": {
              "ne-ref": "NE-SW2"
            },
            "ietf-network-topology:termination-point": [
              {
                "tp-id": "TP-SW2-P1",
                "ietf-network-inventory-topology:inventory-mapping-attributes": {
                  "ne-ref": "NE-SW2",
                  "port-ref": "/nwi:network-inventory/nwi:network-elements/nwi:network-element[ne-id='NE-SW2']/nwi:components/nwi:component[component-id='eth-port-1']"
                }
              }
            ]
          }
        ],
        "ietf-network-topology:link": [
          {
            "link-id": "Link-SW1-SW2",
            "source": {
              "source-node": "SW-1",
              "source-tp": "TP-SW1-P1"
            },
            "destination": {
              "dest-node": "SW-2",
              "dest-tp": "TP-SW2-P1"
            },
            "ietf-network-inventory-topology:inventory-mapping-attributes": {
              "link-type": "fiber"
            }
          }
        ]
      }
    ]
  }
}
~~~~



# JSON Example of an MPO Breakout-Channel Port

This appendix provides an example of a 400 Gb/s DR4 port that is physically implemented as four independent 100 Gb/s lanes (an MPO breakout). The lanes are exposed as breakout-channel entries so that the port can later be configured as either a single 400G trunk or four 100G breakout interfaces. The instance data below shows the minimal JSON encoding {{?RFC7951}} of the "port-breakout" container for this port.

{::include-fold ./yang/examples/port-breakout-example.json}

# Acknowledgments
{:numbered="false"}

The authors wish to thank Italo Busi, Olga Havel, Aihua Guo, Oscar
   Gonzalez de Dios, and many others for their helpful comments and
   suggestions.
