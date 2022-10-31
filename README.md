# Azure Private Link - Regional failover Services behaviour

A list of all Azure PaaS services that utilise Private Link, and a categorisation of how they behave in respect to Multi-Region failover and DNS integration. Click the links to official MS Docs, as well as Video links to more detailed tutorials.

## Context

The main article is [here](https://github.com/adstuart/azure-privatelink-multiregion), please consider this an addendum of links to further Azure service-specific guides

# Service List(s)

## Azure PaaS that can utilise a single Global Azure DNS Private Zone and failover without user-intervention of DNS records

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure SQL      |        | https://youtu.be/weZ-SPO-tIc | Uses Failover Groups and additional FQDN CNAME|
| Azure Service Bus | [Link](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-geo-dr#private-endpoints) | | Namespace pairing requires Premium SKU |
| Azure Event Hub | [Link](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-geo-dr?tabs=portal#private-endpoints) | | Namespace paring requires Standard SKU or above |

## Azure PaaS services that do require user-intevention of DNS records upon failover (or use of regional specific Azure DNS Private Zones)

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure Storage |  | https://youtu.be/bmFMNQkBf2A |  |
| Azure Site Recovery |  | https://youtu.be/_S5dA36SgsI | Largely built on Azure Storage, same pattern for Recovery Services vault and Storage account cache |
| Azure Key Vault | [Link](https://learn.microsoft.com/en-us/azure/key-vault/general/private-link-diagnostics#dns-resolution-for-more-than-one-virtual-network) |  | Not possible to simulate regional failover |
| Azure Cosmos DB | [See comments here](https://youtu.be/FbynO1fM9Ag) | | Clients can use regional FQDN, but this happens [after endpoint discovery](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/tutorial-global-distribution?tabs=dotnetv2%2Capi-async#rest) via the Global FQDN |


