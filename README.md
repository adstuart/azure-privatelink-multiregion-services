# Azure Private Link - Regional failover Services behaviour

A list of all Azure PaaS services that utilise Private Link, and a categorisation of how they behave in respect to Multi-Region failover and DNS integration

## Context

The main article is [here](https://github.com/adstuart/azure-privatelink-multiregion), please consider this an addendum of links to further Azure service-specific guides

## Azure PaaS that can utilise a single Global Azure DNS Private Zone and failover without user-intervention of DNS records

[Azure SQL](https://youtu.be/weZ-SPO-tIc)

## Azure PaaS services that do require user-intevention of DNS records upon failover (or use of regional specific Azure DNS Private Zones)

[Azure Storage](https://youtu.be/bmFMNQkBf2A)
[Azure Site Recovery](https://youtu.be/_S5dA36SgsI)
[Azure Key Vault](https://learn.microsoft.com/en-us/azure/key-vault/general/private-link-diagnostics#dns-resolution-for-more-than-one-virtual-network)
