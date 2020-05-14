# Platform Specific Architecture for AWS (Amazon Web Services)

Description

- [Platform Specific Architecture for AWS (Amazon Web Services)](#platform-specific-architecture-for-aws-amazon-web-services)
  - [AWS: Overall Architecture](#aws-overall-architecture)
  - [AWS: Basic Architecture](#aws-basic-architecture)
    - [AWS: Storage Configurations](#aws-storage-configurations)
  - [AWS: Virtual Hostname/IP](#aws-virtual-hostnameip)
    - [Generic implementation steps](#generic-implementation-steps)
    - [Additional comments](#additional-comments)
  - [AWS: High Availability](#aws-high-availability)
  - [AWS: Disaster Recovery](#aws-disaster-recovery)
  - [AWS: Data Tiering Options](#aws-data-tiering-options)
  - [AWS: XSA](#aws-xsa)

## AWS: Overall Architecture

![AWS: Overall Architecture](../../images/arch-aws-overall.png)

- some general text
  - some basic links to AWS reference architectures and documentation

## AWS: Basic Architecture

Link to generic content: [Module: Basic Architecture](pages/generic_architecture/module_basic_architecture.md#module-basic-architecture)

- supported instance types
- description of single node implementation (storage) + picture
- description of scale-out implementations (storage) + picture
- mention that each AZ is its own subnet
- links to AWS documentation

### AWS: Storage Configurations

- visualization of storage for AWS

## AWS: Virtual Hostname/IP

Link to generic content: [Module: Virtual Hostname/IP](pages/generic_architecture/module_virtual_hostname.md#module-virtual-hostnameip)

This chapter describes recommended implementation of Virtual Hostname and Virtual IP for an SAP HANA installation on the AWS cloud.

The implementation is based on assigning a _Secondary private IP address_ to an existing network interface of the AWS instance. A _Secondary private IP address_ can be easily reassigned to another AWS instance and so it can follow SAP HANA instance relocation. For more details see [AWS: Multiple IP Addresses](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html). This _Secondary private IP address_ is associated with the Virtual Hostname which is used during SAP HANA instance installation.

### Generic implementation steps 

- define Virtual IP (in the same subnet as the network interface) and Virtual Hostname for SAP HANA Instance
- assign _Virtual IP_ as _Secondary private IP address_ to existing network interface (see [AWS: assign secondary private IP](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html#assignIP-existing))
- configure OS to use _Virtual IP Address_ (e.g. [SUSE: Administration Guide - Configuring Multiple Addresses](https://documentation.suse.com/sles/12-SP4/single-html/SLES-admin/index.html#sec-basicnet-yast-configure-addresses))
- make Virtual Hostname resolvable (e.g. updating `/etc/hosts`)
- install SAP HANA instance with the _Virtual Hostname_ (see [SAP: Administration Guide - Default Host Names and Virtual Host Names](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/aa7e697ccf214852a283a75126c34370.html))

**Note:** _Virtual IP_ can be be reassigned to another AWS instance thanks to option "_Allow reassignment_" of the network interface (see [AWS: assign a secondary private IPv4 address](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/MultipleIP.html#assignIP-existing))

### Additional comments

**SAP HANA inbound network communication**

A network communication initiated from a remote system to the Virtual IP of SAP HANA instance is established and takes place between remote system IP and the Virtual IP (_Secondary private IP address_). 
The _Primary private IP address_ on the same interface is not involved in this communication.

**SAP HANA outbound network communication**

In contrast to an inbound connection, when SAP HANA instance initiates a network connection to a remote system the _Primary private IP address_ is used as source IP instead of Virtual IP (_Secondary private IP address_).  
If there is requirement to use Virtual IP as the source IP, it could be achieved by means of linux routing. The core of the idea is to add route specifying source address like `ip route add <network/mask> via <gateway> src <virtual_ip>` (see [Routing for multiple uplinks/providers](https://www.tldp.org/HOWTO/Adv-Routing-HOWTO/lartc.rpdb.multiple-links.html#AEN258)).

## AWS: High Availability

Link to generic content: [Module: High Availability](pages/generic_architecture/module_high_availability.md#module-high-availability)

- link to list of Availability Zones in AWS
- comment that it is important to measure AZ latency via niping (I will add this as new section in general part)
- fencing mechanism (options, recommendation)
- how to implement cluster IP (also referred as overlay IP)
  - relation to different subnets per AZ
  - entry in VPC routing table
  - it is managed by cluster (need to assign IAM roles to VM)
  - need to disable source/destination check on interface
- links to AWS/SUSE/RHEL documentation
- how to modify cluster to have active/active
- how to modify cluster to have tenant specific cluster IPs
- anything else?

## AWS: Disaster Recovery

Link to generic content: [Module: Disaster Recovery](pages/generic_architecture/module_disaster_recovery.md#module-disaster-recovery)

- anything to consider? bandwidth?

## AWS: Data Tiering Options

Link to generic content: [Module: Data Tiering Options](pages/generic_architecture/module_data_tiering.md#module-data-tiering-options)

- what is supported what is not (matrix)
- links to AWS documentation
- modified pictures of storage setup (if required)

## AWS: XSA

Link to generic content: [Module: SAP XSA](pages/generic_architecture/module_xsa.md#module-sap-xsa)

- I think there is nothing infrastructure specific
