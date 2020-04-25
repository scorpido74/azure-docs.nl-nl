---
title: DNS-configuratie voor het persoonlijke eind punt van Azure
description: Meer informatie over de DNS-configuratie voor het persoonlijke eind punt
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 46ecf1bf2f5bd7900677cd115f8571ec3551a874
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82134271"
---
# <a name="azure-private-endpoint-dns-configuration"></a>DNS-configuratie voor het persoonlijke eind punt van Azure


Wanneer u verbinding maakt met een persoonlijke koppelings bron met behulp van een Fully Qualified Domain Name (FQDN) als onderdeel van de connection string, is het belang rijk om uw DNS-instellingen correct te configureren om te worden omgezet in het toegewezen privé-IP-adres. Bestaande Azure-Services hebben mogelijk al een DNS-configuratie die kan worden gebruikt om verbinding te maken via een openbaar eind punt. Deze moet worden overschreven om verbinding te maken via uw persoonlijke eind punt. 
 
De netwerk interface die aan het persoonlijke eind punt is gekoppeld, bevat de volledige set informatie die nodig is voor het configureren van uw DNS, inclusief FQDN-en privé IP-adressen die zijn toegewezen voor een bepaalde persoonlijke koppelings bron. 
 
U kunt de volgende opties gebruiken om uw DNS-instellingen voor privé-eind punten te configureren: 
- **Het hostbestand gebruiken (alleen aanbevolen voor testen)**. U kunt het hostbestand op een virtuele machine gebruiken om de DNS te vervangen.  
- **Gebruik een privé-DNS-zone**. U kunt [privé-DNS-zones](../dns/private-dns-privatednszone.md) gebruiken om de DNS-omzetting voor een bepaald persoonlijk eind punt te negeren. Een persoonlijke DNS-zone kan worden gekoppeld aan uw virtuele netwerk om specifieke domeinen op te lossen.
- **Gebruik uw aangepaste DNS-server**. U kunt uw eigen DNS-server gebruiken om de DNS-omzetting voor een bepaalde persoonlijke koppelings bron te negeren. Als uw [DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wordt gehost op een virtueel netwerk, kunt u een DNS-doorstuur regel maken voor het gebruik van een privé-DNS-zone om de configuratie voor alle persoonlijke koppelings bronnen te vereenvoudigen.
 
> [!IMPORTANT]
> Het is niet raadzaam om een zone die actief is in gebruik te onderdrukken om open bare eind punten op te lossen. Verbindingen met bronnen kunnen niet correct worden omgezet zonder dat DNS naar de open bare DNS doorstuurt. Als u problemen wilt voor komen, maakt u een andere domein naam of volgt u de voorgestelde naam voor elke service hieronder. 

## <a name="azure-services-dns-zone-configuration"></a>Configuratie van DNS-zone voor Azure-Services
Azure-Services maken een canonieke naam DNS-record (CNAME) op de open bare DNS om de oplossing om te leiden naar de voorgestelde persoonlijke domein namen. U kunt de oplossing overschrijven met het privé-IP-adres van uw privé-eind punten. 
 
Uw toepassingen hoeven de verbindings-URL niet te wijzigen. Bij het oplossen van het gebruik van een open bare DNS-server wordt nu omgezet naar uw privé-eind punten. Het proces heeft geen invloed op uw bestaande toepassingen. 

Voor Azure-Services gebruikt u de aanbevolen zone namen zoals beschreven in de volgende tabel:

|Resource type voor persoonlijke koppelingen   |Subbron  |Zone naam  |
|---------|---------|---------|
|SQL-data base (micro soft. SQL/servers)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (micro soft. SQL/servers)    |  SQL Server (sqlServer)        | privatelink.database.windows.net |
|Opslag account (micro soft. Storage/Storage accounts)    |  BLOB (BLOB, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Opslag account (micro soft. Storage/Storage accounts)    |    Tabel (tabel, table_secondary)      |   privatelink.table.core.windows.net       |
|Opslag account (micro soft. Storage/Storage accounts)    |    Wachtrij (wachtrij, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Opslag account (micro soft. Storage/Storage accounts)   |    Bestand (bestand, file_secondary)      |    privatelink.file.core.windows.net      |
|Opslag account (micro soft. Storage/Storage accounts)     |  Web (Web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake File System Gen2 (micro soft. Storage/Storage accounts)  |  Data Lake File System Gen2 (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (micro soft. AzureCosmosDB/databaseAccounts)|Tabel|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL-één server (micro soft. DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (micro soft. DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (micro soft. DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (micro soft.-sleutel kluis/kluizen)|kluis|privatelink.vaultcore.azure.net|
|Azure Kubernetes-service-Kubernetes-API (micro soft. container service/managedClusters)    | managedCluster | {GUID}. privatelink. <region>. azmk8s.io|
|Azure Search (micro soft. Search/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (micro soft. ContainerRegistry/registers) | registry | privatelink.azurecr.io |
|Azure-app configuratie (micro soft. Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Azure Backup (micro soft. Recovery Services/kluizen)| kluis |privatelink. {Region}. backup. windowsazure. com|
|Azure Event hub (micro soft. EventHub/naam ruimten)| naamruimte |privatelink.servicebus.windows.net|
|Azure Service Bus (micro soft. ServiceBus/naam ruimten) | naamruimte |privatelink.servicebus.windows.net|
|Azure Relay (micro soft. relay/naam ruimten) | naamruimte |privatelink.servicebus.windows.net|
|Azure Event Grid (micro soft. EventGrid/topics)     | onderwerp | thematische. {Region}. privatelink. eventgrid. Azure. net|
|Azure Event Grid (micro soft. EventGrid/Domains) | domein | domeinen. {Region}. privatelink. eventgrid. Azure. net |
|Azure webapps (micro soft. web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning (micro soft. MachineLearningServices/werk ruimten)    | werkruimte | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>DNS-configuratie scenario's

Met de FQDN-namen van de Services wordt een openbaar IP-adres omgezet, moet u de DNS-configuratie wijzigen om het privé-IP-adres van het privé-eind punt op te lossen.

DNS is een essentieel onderdeel om de toepassing correct te laten werken door op een juiste manier het IP-adres van het particuliere eind punt op te lossen.

Op basis van uw voor keuren zijn de volgende scenario's beschikbaar voor geïntegreerde omzetting van DNS:

- [Virtual Network workloads zonder aangepaste DNS-server](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtual Network workloads zonder aangepaste DNS-server

Deze configuratie is geschikt voor werk belastingen voor virtuele netwerken zonder aangepaste DNS-server. In dit scenario vraagt de client om het IP-adres van het privé-eind punt naar Azure DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS is verantwoordelijk voor de DNS-omzetting van de privé-DNS-zones.


 > [!NOTE]
> In dit scenario wordt gebruikgemaakt van Azure SQL database aanbevolen Privé-DNS zone. Voor andere services kunt u het model aanpassen met behulp van de volgende Naslag informatie over de configuratie van de [DNS-zone van Azure Services](#azure-services-dns-zone-configuration).

Als u de juiste configuratie wilt configureren, hebt u de volgende resources nodig:

- Virtueel netwerk van de client

- Privé-DNS zone [privatelink.database.Windows.net](../dns/private-dns-privatednszone.md) met [type A-record](../dns/dns-zones-records.md#record-types)

- Informatie over privé-eind punt (FQDN-record naam en privé-IP-adres)

In het volgende diagram ziet u de DNS-omzettings volgorde van virtuele netwerk werkbelastingen met behulp van een privé-DNS-zone

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Eén virtueel netwerk en Azure DNS-server":::

Dit model kan worden uitgebreid naar meerdere peered virtuele netwerken die zijn gekoppeld aan hetzelfde persoonlijke eind punt. U kunt dit doen door [nieuwe virtuele netwerk koppelingen toe te voegen](../dns/private-dns-virtual-network-links.md) aan de privé-DNS-zone voor alle gekoppelde virtuele netwerken.

 > [!IMPORTANT]
>  Er is één privé-DNS-zone vereist voor deze configuratie; het maken van meerdere zones met dezelfde naam voor verschillende virtuele netwerken zou hand matige bewerkingen nodig hebben om de DNS-records samen te voegen

In dit scenario is er sprake van een [hub & spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) -netwerk topologie met de spoke-netwerken die een gemeen schappelijk persoonlijk eind punt delen en alle spoke-virtuele netwerken zijn gekoppeld aan dezelfde privé-DNS-zone. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="hub en spoke met door Azure verschafte DNS":::


## <a name="next-steps"></a>Volgende stappen
- [Meer informatie over privé-eind punten](private-endpoint-overview.md)
