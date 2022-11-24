# Azure Private Link - Regional failover Services behaviour

A list of all Azure PaaS services that utilise Private Link, and a categorisation of how they behave in respect to Multi-Region failover and DNS integration. Click the links to official MS Docs, as well as Video links to more detailed tutorials.

:warning: This does not include _all_ Azure PaaS services, only those that utilise Private Link. Those that follow the VNet integration model are n/a in relation to this guidance.

## Context

The main article is [here](https://github.com/adstuart/azure-privatelink-multiregion), please consider this an addendum of links to further Azure service-specific guides.

_This page is a living document, and I will add more services as I find time to assess them, thanks!_

# Service List(s)

## Azure PaaS that can utilise a single Global Azure DNS Private Zone and failover without user-intervention of DNS records

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure SQL      |        | https://youtu.be/weZ-SPO-tIc | Uses Failover Groups and additional FQDN CNAME|
| Azure SQL Managed Instance | [Link](https://learn.microsoft.com/en-us/azure/azure-sql/managed-instance/auto-failover-group-sql-mi?view=azuresql&tabs=azure-powershell) | | [1] Requires Layer-3 IP connectivity between SQL MI subnets for data replication (Unlike Azure SQL which replicates out of band <br> [2] Value prop of Private Endpoint for SQL MI is unclear, why is a PE needed for a service that is already VNet-injected? |
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
| Azure Container Registry | [1 - Geo replication doc](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-geo-replication) <br> [2 - PL for ACR](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-private-link) | | [1] - requires premium SKU for geo-replication <br> [2] - Specifically calls out [in docs](https://learn.microsoft.com/en-us/azure/container-registry/container-registry-private-link#:~:text=DNS%20configuration.-,Important,-If%20for%20high) that single global Azure DNS Private Zone will be problematic. <br> [3] - Use of Private Link for geo-dispersed replicas of ACR remove your ability to leverage Traffic Manager to route user/client to nearest endpoint, therefore this is now the function of the customer's internal DNS infrastructure |
| Power BI | [Link](https://learn.microsoft.com/en-us/power-bi/enterprise/service-security-private-links#enable-private-endpoints-for-power-bi) | | Private Link CNAME happens before regional re-direction, so user DNS intervention required if the region within which your primary PE are located goes down. E.g. [here](https://www.digwebinterface.com/?hostnames=app.powerbi.com&type=&ns=resolver&useresolver=8.8.4.4&nameservers=) |

## Azure PaaS services that do not have service-level regional failover (I.e. you must handle this as the application level)

| Service      | Microsoft Docs | Video | Notes |
| ----------- | ----------- | ----------- | ----------- |
| Azure Database for Postgres (Single Server) | [Link](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-data-access-and-security-private-link) | https://youtu.be/Tzr2QRYSyRQ | [1] Only applies to Single Server, Flexible Server uses VNet injection instead. <br> [2] Uses concept of [read replicas](https://learn.microsoft.com/en-us/azure/postgresql/single-server/how-to-read-replicas-portal) for regional DR. See options for regional replication [here](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-read-replicas#cross-region-replication). Requires General Purpose tier or above for multi-region replication. <br> [3] User is responsible for regional failover, you must [repoint](https://learn.microsoft.com/en-us/azure/postgresql/single-server/concepts-read-replicas#failover-to-replica:~:text=Point%20your%20application%20to%20the%20(former)%20replica) your API or connection string at the replica FQDN. |
| Azure Database for MariaDB | | | Same approach as Postgres Single Server, user has to repoint to replica in region-down event - [Link](https://learn.microsoft.com/en-us/azure/mariadb/concepts-read-replicas#failover) |
| Azure Database for MySQL (Single Server) |  |  | [1] Only applies to Single Server, Flexible Server uses VNet injection instead. <br> [2] MySQL Single Server is being retired in 2024 - [link](https://learn.microsoft.com/en-us/azure/mysql/single-server/whats-happening-to-mysql-single-server) |
| Azure Automation | [1 - Link for DR docs](https://learn.microsoft.com/en-us/azure/automation/automation-disaster-recovery?tabs=win-hrw%2Cps-script%2Coption-one) <br> [2 - Link for PL doc](https://learn.microsoft.com/en-us/azure/automation/how-to/private-link-security) | | User is required to self-replicate contents of automation account and associate dependencies **and** manually re-deploy agents etc to use the region-B automation assets in a region down scenario |
| Azure Batch | [1 - Private Link for Batch](https://learn.microsoft.com/en-us/azure/batch/private-connectivity) <br> [2 - DR for Batch](https://learn.microsoft.com/en-us/azure/batch/high-availability-disaster-recovery) <br> [3 - Account migration for Batch](https://learn.microsoft.com/en-us/azure/batch/account-move) | | Azure Batch has not concept of regional failover, it is all user driven, and independant accounts are always used in each region, each with their own FQDN. |
| Azure Cognitive Search | [1 - PL for Search](https://learn.microsoft.com/en-us/azure/search/service-create-private-endpoint) <br> [2 - HA and BCDR for Search](https://learn.microsoft.com/en-us/azure/search/search-performance-optimization#multiple-services-in-separate-geographic-regions) | | [Azure Cognitive Search doesn't provide an automated method of replicating search indexes across geographic regions](https://learn.microsoft.com/en-us/azure/search/search-performance-optimization#multiple-services-in-separate-geographic-regions:~:text=Azure%20Cognitive%20Search%20doesn%27t%20provide%20an%20automated%20method%20of%20replicating%20search%20indexes%20across%20geographic%20regions). I.e. this is another example wherein the user has to build the abstraction on top of multiple search replicas, the linked documentaiton gives some examples including indexing and Traffic Manager. |
| Azure Monitor | | | Regional failover is n/a - each region has its own components, e.g. Regional Log Analytics Workspaces |
| Azure Kubernetes Service (AKS) | | | Regional failover is n/a - each region has its node:master relationship that uses regional FQDN, this is not common across regions |


