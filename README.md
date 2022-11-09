# Azure Private Link - Regional failover Services behaviour

A list of all Azure PaaS services that utilise Private Link, and a categorisation of how they behave in respect to Multi-Region failover and DNS integration. Click the links to official MS Docs, as well as Video links to more detailed tutorials.

## Context

The main article is [here](https://github.com/adstuart/azure-privatelink-multiregion), please consider this an addendum of links to further Azure service-specific guides.

_This page is a living document, and I will add more services as I find time to assess them, thanks!_

# Service List(s)

## Azure PaaS that can utilise a single Global Azure DNS Private Zone and failover without user-intervention of DNS records

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure SQL      |        | https://youtu.be/weZ-SPO-tIc | Uses Failover Groups and additional FQDN CNAME|
| Azure Service Bus | [Link](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-geo-dr#private-endpoints) | https://youtu.be/qukADwfihZY | Namespace pairing requires Premium SKU |
| Azure Event Hub | [Link](https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-geo-dr?tabs=portal#private-endpoints) | https://youtu.be/qukADwfihZY | Namespace paring requires Standard SKU or above |

## Azure PaaS services that do require user-intevention of DNS records upon failover (or use of regional specific Azure DNS Private Zones)

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure Storage |  | https://youtu.be/bmFMNQkBf2A |  |
| Azure Site Recovery |  | https://youtu.be/_S5dA36SgsI | Largely built on Azure Storage, same pattern for Recovery Services vault and Storage account cache |
| Azure Key Vault | [Link](https://learn.microsoft.com/en-us/azure/key-vault/general/private-link-diagnostics#dns-resolution-for-more-than-one-virtual-network) | https://youtu.be/vlGK27D3bPg | Not possible to simulate regional failover |
| Azure Cosmos DB | [See comments here](https://youtu.be/FbynO1fM9Ag) | https://youtu.be/_WrJT7pLRv4 | Clients can use regional FQDN, but this happens [after endpoint discovery](https://learn.microsoft.com/en-us/azure/cosmos-db/nosql/tutorial-global-distribution?tabs=dotnetv2%2Capi-async#rest) via the Global FQDN |
| Azure Static Web Apps | [Link](https://learn.microsoft.com/en-us/azure/static-web-apps/private-endpoint) | | N/a to staging environment, front-end only |

## Azure PaaS services that do not have service-level regional failover (I.e. you must handle this as the application level)

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure Database for Postgres (Single Server) | [Link](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-data-access-and-security-private-link) | | [1] Only applies to Single Server, Flexible Server uses VNet injection instead. <br> [2] Uses concept of [read replicas](https://learn.microsoft.com/en-us/azure/postgresql/single-server/how-to-read-replicas-portal) for regional DR. See options for regional replication [here](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-read-replicas#cross-region-replication). Requires General Purpose tier or above for multi-region replication. <br> [3] User is responsible for regional failover, you must [repoint](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-read-replicas#failover-to-replica:~:text=Point%20your%20application%20to%20the%20(former)%20replica) your API or connection string at the replica FQDN. |

