---
layout: post
author: Lucas
title: Azure Networking for Virtual Networks
subtitle: Service Endpoint, Private Endpoint, Private Link, and more
categories: [networking, azure]
tags: [azure, cloud, vnet, "app services", dns, "private endpoint", "service endpoint", "private link"]
---
<!--🔴 🟠 ⚫ ⚪ 🟣 🟢 🟡 🔵-->

## `nslookup test.blob.core.windows.net` from a VM in a VNet

- without any integration:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🔵 blob.a1b2c3.store.core.windows.net
  - 🔵 blob.a1b2c3.store.core.windows.net -> A record -> 🔴 52.240.48.36 (public IP)
- with PrivateLink:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🟡 test.privatelink.blob.core.windows.net
  - 🟡 test.privatelink.blob.core.windows.net -> CNAME -> 🔵 blob.a1b2c3.store.core.windows.net
  - 🔵 blob.a1b2c3.store.core.windows.net -> A record -> 🔴 52.240.48.36 (public IP)
- with PrivateLink and Private Endpoint:
  - ⚪ test.blob.core.windows.net -> CNAME -> 🟡 test.privatelink.blob.core.windows.net
  - 🟡 test.privatelink.blob.core.windows.net -> A record (Azure Private DNS) -> 🟢 10.0.1.4

## DNS Configuration

- The VNet uses Azure DNS by default.
- NIC's inside a VNet use the VNet DNS configuration by default.
- An internal DNS private zone called IDNS is created automatically inside every VNet.
  - Any NIC added to the VNet gets registered within IDNS
  - The domain name for the private zone is `internal.cloudapp.net`

## Private DNS Zone

- Can hold any time of record for any name. Full-range.
- Exists outside of VNets (global resource) and can be attached to them.
- Private DNS zones can be used solely for DNS resolution or for registration:
  - Like IDNS, all NIC's added to the VNet gets registered within the private zone.
  - A VNet can only connect to one private zone for registration.
  - A private DNS zone can connect to 100 VNets for registration and 1000 for resolution.

## Network Security Groups

- Can only be attached to a subnet or a network interface (eg. a VM).
- App Security Group: works like a managed tag that can be referenced as source/destination in NSG rules.

## Service Endpoints

- Services such as Azure Storage, Key Vault, Cosmos DB, etc offer service endpoint.
- It creates a record in the route table of a subnet listing all the multiple public IP CIDR blocks of an Azure service and redirect them to the VirtualNetworkServiceEndpoint and remains internal to Azure.
- The source IP address will switch from public to private, so the service's firewall rules must reflect that.

## Private Endpoints

- Adds a NIC to a subnet with private IP (within the VNet CIDR block) pointing to a storage account and service.

## The reason for PrivateLink

From the results above, it may seem that if you have a Private Endpoint, you can bypass PrivateLink and just create an Azure Private DNS that points ⚪ test.blob.core.windows.net to 🟢 10.0.1.4. It would work, but for that to happen the Private DNS zone would have to be at the `blob.core.windows.net`, and you'd then be expected to create a record for each Blob Storage that the VNet attached to the Private DNS zone would like to connect to. For example, if a machine also wanted to connect to `second-test.blob.core.windows.net`, it would be directed to resolve with the Private DNS zone, which would need to be manually configured. It is often more sensible to create a Private DNS zone at the `privatelink.blob.core.windows.net` level.

## On-premises considerations

- Azure Private DNS zones are available globally, you can connect any VNet in the world to it for link resolution, but you cannot connect to it from on-premises.
- You can deploy a DNS forwarder in a VNet, then create a record in the local DNS (on-prem) to point any `privatelink.blob.core.windows.net` requests to the DNS forwarder, which can then talk to the Private DNS zone.
- Optionally, the records in the Private DNS zone can be recreated manually on local DNS.

## PrivateLink Service

- Useful in cases virtual networks cannot be peered (eg. 3rd party services, CIDR block overlap, etc).
- The host VNet deploys PLS internally in its own subnet. PLS will receive external requests and forward them to a Standard Load Balancer.
- The PLS deployment can have up to 8 IP addresses in the VNet, and it connects to a Front End IP address of the Load Balancer.

## Questions

- Is the VirtualNetworkServiceEndpoint a NIC with private IP?
- How does a Private Endpoint, which is a NIC with a private IP inside the VNet, talk to a Blob Storage instance without using the internet or Public IP?
- How does a private DNS zone get linked to a VNet? VNet's route table?
