---
layout: post
author: Lucas
title: Azure Networking for Virtual Networks
subtitle: Service Endpoint, Private Endpoint, Private Link, and more
categories: [networking, azure]
tags: [azure, cloud, vnet, app service, dns, private endpoint, service endpoint, private link, networking]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## Basics & Concepts

### VNet & DNS

- The VNet uses Azure DNS by default.
  - NIC's inside a VNet use the VNet DNS configuration by default.
- An internal DNS private zone called IDNS is created automatically inside every VNet.
  - Any NIC added to the VNet gets registered within IDNS
  - The domain name for the private zone is `internal.cloudapp.net`

### Private DNS Zone

- Exists outside of VNets (global resource) and can be attached to them.
- Can hold any time of record for any name. Full-range.
- Private DNS zones can be used solely for DNS resolution or for registration:
  - Like IDNS, all NIC's added to the VNet gets registered within the private zone.
  - A VNet can only connect to one private zone for registration.
  - A private DNS zone can connect to 100 VNets for registration and 1000 for resolution.

### Network Security Groups

- Can only be attached to a subnet or a network interface (eg. a VM).
- App Security Group: works like a managed tag that can be referenced as source/destination in NSG rules.

### The Azure "Backbone"

- Microsoft has a network infrastructure for Azure called the "Azure backbone".
- Azure can "magically" route things through this backbone.
- This backbone is how service endpoint, private endpoint, and VNet integration can keep requests needing to reach the internet.
  - Service endpoints use that magic by forwarding all requests to public IPs of storage accounts to a `VirtualNetworkServiceEndpoint`, which then redirects the requests through the backbone and is fully managed by Azure, the customer can't see it.

### Service Endpoints

![Service Endpoint Request Flow](/assets/images/2022-07-10-azure-networking-vnet-dns/service_endpoint.jpg)

- Adds a record to a subnet's route table pointing public IP addresses of services to `VirtualNetworkServiceEndpoint`, which chains records redirecting the request through the Azure backbone all the way to the service's public endpoint.
- The source IP address will switch from public to private as the request will be internal to the VNet, so the service's firewall rules must reflect that.
- The request's final hop is still the public endpoint of the target *service*, even if the source IP is private.
- Services such as Azure Storage, Key Vault, Cosmos DB, etc offer service endpoint.

### Private Endpoints

![Private Endpoint Request Flow with Private Link](/assets/images/2022-07-10-azure-networking-vnet-dns/private_endpoint_private_link.png)

- Adds a NIC to a subnet and attaches said NIC to a specific *resource* (eg. a blob storage).
- It is usually coupled with Private Link, which creates a private DNS zone to map all resource requests to their correct Private Endpoint private IP addresses.

## Example

### `nslookup test.blob.core.windows.net` from a VM in Azure

- without any integrations:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🔵 blob.a1b2c3.store.core.windows.net
  - 🔵 blob.a1b2c3.store.core.windows.net -> A record -> 🔴 52.240.48.36 (public IP)
- with Service Endpoint:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🔵 blob.a1b2c3.store.core.windows.net
  - 🔵 blob.a1b2c3.store.core.windows.net -> A record -> 🔴 52.240.48.36 (public IP)
  - 🔴 52.240.48.36 (public IP) -> Subnet Route Table Entry -> 🟠 VirtualNetworkServiceEndpoint
  - 🟠 VirtualNetworkServiceEndpoint -> ???? -> ⚫ Azure Backbone
  - ⚫ Azure Backbone -> ???? -> 🔴 52.240.48.36 (????)
- with Private Link:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🟡 test.privatelink.blob.core.windows.net
  - 🟡 test.privatelink.blob.core.windows.net -> CNAME -> 🔵 blob.a1b2c3.store.core.windows.net
  - 🔵 blob.a1b2c3.store.core.windows.net -> A record -> 🔴 52.240.48.36 (public IP)
- with Private Link and Private Endpoint:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🟡 test.privatelink.blob.core.windows.net
  - 🟡 test.privatelink.blob.core.windows.net -> A record (Azure Private DNS) -> 🟢 10.0.1.4

### The reason for PrivateLink

From the results above, it may seem that if you have a Private Endpoint, you can bypass PrivateLink and just create an Azure Private DNS that points ⚪ test.blob.core.windows.net to 🟢 10.0.1.4. It would work, but for that to happen the Private DNS zone would have to be at the `blob.core.windows.net`, and you'd then be expected to create a record for each Blob Storage that the VNet attached to the Private DNS zone would like to connect to. For example, if a machine also wanted to connect to `second-test.blob.core.windows.net`, it would be directed to resolve with the Private DNS zone, which would need to be manually configured. It is often more sensible to create a Private DNS zone at the `privatelink.blob.core.windows.net` level.

### On-premises considerations

- Azure Private DNS zones are available globally, you can connect any VNet in the world to it for link resolution, but you cannot connect to it from on-premises.
- You can deploy a DNS forwarder in a VNet, then create a record in the local DNS (on-prem) to point any `privatelink.blob.core.windows.net` requests to the DNS forwarder, which can then talk to the Private DNS zone.
- Optionally, the records in the Private DNS zone can be recreated manually on local DNS.

### PrivateLink Service

- Useful in cases virtual networks cannot be peered (eg. 3rd party services, CIDR block overlap, etc).
- The host VNet deploys PLS internally in its own subnet. PLS will receive external requests and forward them to a Standard Load Balancer.
- The PLS deployment can have up to 8 IP addresses in the VNet, and it connects to a Front End IP address of the Load Balancer.

## Questions

- Is the VirtualNetworkServiceEndpoint a NIC with private IP? Is there a DNS record for VirtualNetworkServiceEndpoint? What does it look like?
- What does a traceroute of a request to the public IP of a service looks like compared a request to VirtualNetworkServiceEndpoint?
- Does the Private Endpoint NIC get attached directly to the resource server?
- How does Private Endpoint DNS work without Private Link?
- How does a private DNS zone get linked to a VNet? VNet's route table?
