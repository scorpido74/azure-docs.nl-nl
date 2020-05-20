---
title: DNS-configuratie voor het persoonlijke eind punt van Azure
description: Meer informatie over de DNS-configuratie voor het persoonlijke eind punt
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: af7d47c98e4716df3a6cbd222c7d3c8def48e5fc
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83701637"
---
# <a name="azure-private-endpoint-dns-configuration"></a>DNS-configuratie voor het persoonlijke eind punt van Azure


Wanneer u verbinding maakt met een persoonlijke koppelings bron met behulp van een Fully Qualified Domain Name (FQDN) als onderdeel van de connection string, is het belang rijk om uw DNS-instellingen correct te configureren om te worden omgezet in het toegewezen privé-IP-adres. Bestaande Azure-Services hebben mogelijk al een DNS-configuratie die kan worden gebruikt om verbinding te maken via een openbaar eind punt. Deze configuratie moet worden overschreven om verbinding te maken via uw persoonlijke eind punt. 
 
De netwerk interface die aan het persoonlijke eind punt is gekoppeld, bevat de volledige set informatie die nodig is voor het configureren van uw DNS, inclusief FQDN-en privé IP-adressen die zijn toegewezen voor een bepaalde persoonlijke koppelings bron. 
 
U kunt de volgende opties gebruiken om uw DNS-instellingen voor privé-eind punten te configureren: 
- **Het hostbestand gebruiken (alleen aanbevolen voor testen)**. U kunt het hostbestand op een virtuele machine gebruiken om de DNS te vervangen.  
- **Gebruik een privé-DNS-zone**. U kunt [privé-DNS-zones](../dns/private-dns-privatednszone.md) gebruiken om de DNS-omzetting voor een bepaald persoonlijk eind punt te negeren. Een persoonlijke DNS-zone kan worden gekoppeld aan uw virtuele netwerk om specifieke domeinen op te lossen.
- **Gebruik uw DNS-doorstuur server (optioneel)**. U kunt de DNS-doorstuur server gebruiken om de DNS-omzetting voor een bepaalde persoonlijke koppelings bron te negeren. Als uw [DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wordt gehost op een virtueel netwerk, kunt u een DNS-doorstuur regel maken voor het gebruik van een privé-DNS-zone om de configuratie voor alle persoonlijke koppelings bronnen te vereenvoudigen.
 
> [!IMPORTANT]
> Het is niet raadzaam om een zone die actief is in gebruik te onderdrukken om open bare eind punten op te lossen. Verbindingen met bronnen kunnen niet correct worden omgezet zonder dat DNS naar de open bare DNS doorstuurt. Als u problemen wilt voor komen, maakt u een andere domein naam of volgt u de voorgestelde naam voor elke service hieronder. 

## <a name="azure-services-dns-zone-configuration"></a>Configuratie van DNS-zone voor Azure-Services
Azure-Services maken een canonieke naam DNS-record (CNAME) op de open bare DNS om de oplossing om te leiden naar de voorgestelde persoonlijke domein naam. U kunt de oplossing overschrijven met het privé-IP-adres van uw privé-eind punten. 
 
Uw toepassingen hoeven de verbindings-URL niet te wijzigen. Bij het oplossen van het gebruik van een open bare DNS-server wordt nu omgezet naar uw privé-eind punten. Het proces heeft geen invloed op uw bestaande toepassingen. 

Voor Azure-Services gebruikt u de aanbevolen zone namen zoals beschreven in de volgende tabel:

| Resource type voor persoonlijke koppelingen/subresource |Privé-DNS zone naam | Doorstuur servers voor open bare DNS-zone |
|---|---|---|---|
| SQL-data base (micro soft. SQL/servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (micro soft. SQL/servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/tabel (tabel, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/wachtrij (wachtrij, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/bestand (bestand, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Opslag account (micro soft. Storage/Storage accounts)/Web (Web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake File System Gen2 (micro soft. Storage/Storage accounts)/Data Lake File System Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)/Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL-één server (micro soft. DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (micro soft. DBforMySQL/servers)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (micro soft. DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (micro soft. de sleutel kluis/kluizen)/kluis | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes service-Kubernetes-API (micro soft. container service/managedClusters)/managedCluster | privatelink. {Region}. azmk8s. io | {Region}. azmk8s. io |
| Azure Search (micro soft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (micro soft. ContainerRegistry/registers)/REGI ster | privatelink.azurecr.io | azurecr.io |
| Azure-app configuratie (micro soft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (micro soft. Recovery Services/kluizen)/kluis | privatelink. {Region}. backup. windowsazure. com | {Region}. backup. windowsazure. com |
| Azure Event hub (micro soft. EventHub/naam ruimten)/naam ruimte | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (micro soft. ServiceBus/namespaces)/naam ruimte | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Relay (micro soft. relay/naam ruimten)/naam ruimte | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (micro soft. EventGrid/topics)/topic | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (micro soft. EventGrid/domeinen)/domein | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure webapps (micro soft. web/sites)/site | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (micro soft. MachineLearningServices/werk ruimten)/werk ruimte | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>DNS-configuratie scenario's

De FQDN-namen van de services worden automatisch omgezet in een openbaar IP-adres, dus als u wilt omzetten naar het privé-IP-adres van het privé-eind punt, moet u de DNS-configuratie dienovereenkomstig wijzigen.

DNS is een essentieel onderdeel om de toepassing correct te laten werken door op een juiste manier het IP-adres van het particuliere eind punt op te lossen.

Op basis van uw voor keuren zijn de volgende scenario's beschikbaar voor geïntegreerde omzetting van DNS:

- [Virtuele netwerk werkbelastingen zonder aangepaste DNS-server](#virtual-network-workloads-without-custom-dns-server)
- [On-premises workloads met behulp van een DNS-doorstuur server](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtuele netwerk werkbelastingen zonder aangepaste DNS-server

Deze configuratie is geschikt voor werk belastingen voor virtuele netwerken zonder aangepaste DNS-server. In dit scenario vraagt de client om het IP-adres van het privé-eind punt naar Azure DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS is verantwoordelijk voor de DNS-omzetting van de privé-DNS-zones.


> [!NOTE]
> In dit scenario wordt gebruikgemaakt van Azure SQL database aanbevolen Privé-DNS zone. Voor andere services kunt u het model aanpassen met behulp van de volgende Naslag informatie over de configuratie van de [DNS-zone van Azure Services](#azure-services-dns-zone-configuration).

U hebt de volgende resources nodig om correct te configureren:

- Virtueel netwerk van de client

- Privé-DNS zone [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) met [type A-record](../dns/dns-zones-records.md#record-types)

- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van virtuele netwerk werkbelastingen met behulp van een privé-DNS-zone

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Eén virtueel netwerk en door Azure verschafte DNS":::

Dit model kan worden uitgebreid naar meerdere peered virtuele netwerken die zijn gekoppeld aan hetzelfde persoonlijke eind punt. U kunt dit doen door [nieuwe virtuele netwerk koppelingen toe te voegen](../dns/private-dns-virtual-network-links.md) aan de privé-DNS-zone voor alle gekoppelde virtuele netwerken.

> [!IMPORTANT]
> Er is één privé-DNS-zone vereist voor deze configuratie; het maken van meerdere zones met dezelfde naam voor verschillende virtuele netwerken zou hand matige bewerkingen nodig hebben om de DNS-records samen te voegen

In dit scenario is er sprake van een [hub & spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) -netwerk topologie met de spoke-netwerken die een gemeen schappelijk persoonlijk eind punt delen en alle spoke-virtuele netwerken zijn gekoppeld aan dezelfde privé-DNS-zone. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hub en spoke met door Azure verschafte DNS":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>On-premises workloads met behulp van een DNS-doorstuur server

Voor on-premises workloads om een FQDN-naam van een persoonlijk eind punt in het privé-IP-adres op te lossen, moet u een DNS-doorstuur server gebruiken om de oplossing te maken van de [open bare DNS-zone](#azure-services-dns-zone-configuration) van de Azure-service die in Azure is geïmplementeerd.


Het volgende scenario is geschikt voor een on-premises netwerk dat een DNS-doorstuur server heeft in azure, dat op zijn beurt verantwoordelijk is voor het omzetten van alle DNS-query's via een doorstuur server van het niveau van de [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) naar de door Azure verschafte DNS-service 

> [!NOTE]
> In dit scenario wordt gebruikgemaakt van Azure SQL database aanbevolen Privé-DNS zone.Voor andere services kunt u het model aanpassen met behulp van de volgende Naslag informatie over de configuratie van de [DNS-zone van Azure Services](#azure-services-dns-zone-configuration).

U hebt de volgende resources nodig om correct te configureren:

- On-premises netwerk
- Virtueel netwerk [verbonden met on-premises](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-doorstuur server geïmplementeerd in azure 
- Privé-DNS zones [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)   met [een record typen](../dns/dns-zones-records.md#record-types)
- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van een on-premises netwerk dat gebruikmaakt van een DNS-doorstuur server die is geïmplementeerd in azure, waarbij de oplossing wordt gemaakt door een privé-DNS-zone die is [gekoppeld aan een virtueel netwerk.](../dns/private-dns-virtual-network-links.md)

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="On-premises met Azure DNS":::

Deze configuratie kan worden uitgebreid voor een on-premises netwerk waarin al een DNS-oplossing aanwezig is. 
De on-premises DNS-oplossing moet worden geconfigureerd om DNS-verkeer door te sturen naar de Azure DNS via een [voorwaardelijke doorstuur server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) die verwijst naar de DNS-doorstuur server die is geïmplementeerd in Azure.

> [!NOTE]
> In dit scenario wordt gebruikgemaakt van Azure SQL database aanbevolen Privé-DNS zone.Voor andere services kunt u het model aanpassen met behulp van de volgende Naslag informatie over de configuratie van de [DNS-zone van Azure Services](#azure-services-dns-zone-configuration).

U hebt de volgende resources nodig om correct te configureren:

- On-premises netwerk met een aangepaste DNS-oplossing 
- Virtueel netwerk [verbonden met on-premises](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-doorstuur server geïmplementeerd in azure
- Privé-DNS zones [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md)    met [een record typen](../dns/dns-zones-records.md#record-types)
- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van een on-premises netwerk dat DNS-verkeer voorwaardelijk doorstuurt naar Azure, waarbij de oplossing wordt gemaakt door een privé-DNS-zone die is [gekoppeld aan een virtueel netwerk.](../dns/private-dns-virtual-network-links.md)

> [!IMPORTANT]
> Het voorwaardelijk door sturen moet worden uitgevoerd naar de aanbevolen [**doorstuur server voor de open bare DNS-zone**](#azure-services-dns-zone-configuration)   :  `database.windows.net`   in plaats van **privatelink**. database.Windows.net

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="On-premises door sturen naar Azure DNS":::


## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over privé-eind punten](private-endpoint-overview.md)
