# VPC – Virtual Private Cloud


Think of a VPC as a Virtual Data Center in the cloud. Amazon Virtual Private Cloud (Amazon VPC) lets us provision a logically isolated section of the AWS Cloud where we can launch AWS resources in a virtual network that we define. We have complete control over our virtual networking environment, including selection of our own **IP address range, creation of subnets, and configuration of route tables and network gateways**.

## VPC Consists of:

- An Internet Gateways or Virtual Private Gateways  - A VPC can have only one internet gateway
- Route Tables
- NACLs (Network Access Control lists) Security Groups
- Security Groups

## VPC key concepts:
![IMG_147F4C5671BC-1](https://user-images.githubusercontent.com/16858570/124670870-758c6680-de69-11eb-95c4-188bac3c2345.jpeg)

* **A Virtual Private Cloud:**
    - A logically isolated virtual network in the AWS cloud. We define a VPC’s IP address space from ranges we select. 

* **Internet Gateway:**
    - The Amazon VPC side of a connection to the public Internet. A VPC can have only one internet gateway.
    - Creating a VPC also creates a route table but it doesn’t create a subnet or internet gateway by default.
    - For a VPC route table point to an internet gateway, we must first attach the internet gateway to the VPC.
    - We can attach only one internet gateway to a VPC at a time; if we’re getting an error when trying to attach an Internet Gateway to a VPC, it could be that an Internet Gateway is already attached to the VPC.
    - Before deleting an IGW, we must first detach it from the VPC it’s attached to.

* **Subnet:** A segment of a VPC’s IP address range where we can place groups of isolated resources.
    - Use public facing subnets for public facing web servers
    - Use private subnets for backend services, databases, etc.
    - Each subnet is always mapped to an AZ (Availability Zone); subnets are strongly bound
to an AZ - it's not possible to span subnets across multiple AZs. However, security groups, NACLs, and Route Tables can span multiple subnets and AZs. Remember:
    - **1 subnet == 1 AZ**
    - Only one internet gateway can be attached to a subnet.
    - For multiple layers of security, it’s recommended you use a VPC in addition to security groups and NACLs (Network Access Control Lists).
    - Security groups (first layer of defense) exist at the instance level.
    - NACLs (second layer of defense) exist at the subnet level.
    - It’s possible to implement a private cloud (i.e. a corporate data center) using VPCs.

* **NAT Gateway:**

    - A highly available, managed Network Address Translation (NAT) service for our resources in a private subnet to access the Internet.
    - NAT is used for traffic routing
    - It’s best practice to always enable HTTP and HTTPs traffic.
    - Must be provisioned into a public subnet, and it must be part of the private subnet’s route table in order for your instances in the private subnet to communicate with the outside internet.
    - Instances within a private subnet cannot communicate with the outside internet by default. In order for your instances within the private subnet to communicate with the internet (i.e. to run “yum update”), you’ll need to add 0.0.0.0/0 with the target pointing to your NAT gateway/instance to the route table in the private subnet

* **Virtual Private Gateway:** 
    - The Amazon VPC side of a VPN connection.

* **Peering Connection:** 
    - A peering connection enables us to route traffic via private IP addresses between two peered VPCs.

* **VPC Endpoints:** 
    - Enables private connectivity to services hosted in AWS, from within our VPC without using an Internet Gateway, VPN, Network Address Translation (NAT) devices, or firewall proxies.
    
* **Egress-only Internet Gateway:** 
    - A stateful gateway to provide egress only access for IPv6 traffic from the VPC to the Internet.

## What can we do with a VPC? 

![IMG_AF737D4795B7-1](https://user-images.githubusercontent.com/16858570/124671083-cd2ad200-de69-11eb-8393-406fffa85123.jpeg)

- we can have multiple VPC in a region (default up to 5).
- we can have 1 internet gateway per VPC.
- 1 Subnet = 1 AZ
- Security groups are Stateful, instead, Network ACLs are stateless.

* **Default VPC**

    - Amazon provides a default VPC to immediately deploy instances.
    - All Subnets in default VPC have a route out to the internet.

* **VPC Peering**
    - we can peer one VPC to another VPC using private IP subnets.
    - we can peer VPC's with other AWS accounts as well as with other VPC's in the same account.
    
* **How to VPC Peering** 
Overlapping CIDR Blocks is not supported: We can't connect two VPC's that have the same CIDR.
Transitive Peering is not supported:
We have a VPC peering connection between VPC A and VPC B (pcx-aaaabbbb), and between VPC A and VPC C (pcx-aaaacccc). There is no VPC peering connection between VPC B and VPC C. We cannot route packets directly from VPC B to VPC C through VPC A.

![vpc_peering](https://camo.githubusercontent.com/126abfb98b8a1548b5eb79a8b2e2f42326907518/68747470733a2f2f646f63732e6177732e616d617a6f6e2e636f6d2f7670632f6c61746573742f70656572696e672f696d616765732f7472616e7369746976652d70656572696e672d6469616772616d2e706e67)

**Build Custom VPC**

- VPC and Subnet Sizing The first four IP addresses and the last IP address in each subnet CIDR block are not available for us to use and cannot be assigned to an instance.
- For Nat Instances we have to disable the Source/Destination Checks.
- Use Nat Gateways instead of Nat Instances

**Network Access Control Lists vs Security Groups**

- NACL cannot be deployed in multiple VPCs.
- NACL cannot be attached to multiple subnets, only one at the time.
- Each subnet must be associated with a network ACL, if we don't, default NACL will be used.
- By default, when we create one NACL, everything is denied.
- Rules are applied in numerical order (starting from the lowest), so when we should create the first rule having number 100 and add others on incremental of 100
- NACL are stateless (opposite of Security Groups)
- Remember to open ephemeral ports on our outbound rules only.
- If we have to block specific IP addresses, use network ACL not Security Groups

**Custom VPC's and ELB's**

- Design tip: Remember that ELB needs at least two public AZ's, so when designing our network,
remember to create at least two public subnets in two AZ.

**VPC Flow Logs**

VPC Flow Logs is a feature that enables us to capture information about the IP traffic going to and from network interfaces in our VPC. Flow log data can be published to Amazon CloudWatch Logs and Amazon S3. After we've created a flow log, we can retrieve and view its data in the chosen destination. 

**VPC Endpoints**

A VPC endpoint enables we to privately connect our VPC to supported AWS services and VPC endpoint services powered by Private Link without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection.