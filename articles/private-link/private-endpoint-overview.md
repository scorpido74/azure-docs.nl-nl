---
title: Wat is een Azure Private Endpoint?
description: Meer informatie over Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: b8f55b0693b7ffdf10516cfb19b67ccc5a94dc5a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742910"
---
# <a name="what-is-azure-private-endpoint"></a>Wat is Azure Private Endpoint?

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Private Endpoint maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. De service kan een Azure-service zijn, zoals Azure Storage, Azure Cosmos DB, SQL, etc. of uw eigen [Private Link-service.](private-link-service-overview.md)
  
## <a name="private-endpoint-properties"></a>Eigenschappen privéeindpunt 
 Een privéeindpunt geeft de volgende eigenschappen op: 


|Eigenschap  |Beschrijving |
|---------|---------|
|Name    |    Een unieke naam binnen de resourcegroep.      |
|Subnet    |  Het subnet voor het implementeren en toewijzen van privé-IP-adressen van een virtueel netwerk. Zie de sectie Beperkingen in dit artikel voor subnetvereisten.         |
|Private Link-bron    |   De bron voor privékoppelingen die verbinding maakt met resource-id of alias, in de lijst met beschikbare typen. Er wordt een unieke netwerk-id gegenereerd voor al het verkeer dat naar deze bron wordt verzonden.       |
|Subresource target   |      De subbron om verbinding te maken. Elk resourcetype privékoppeling heeft verschillende opties om te selecteren op basis van voorkeur.    |
|Goedkeuringsmethode voor verbinding    |  Automatisch of handmatig. Op basis van RBAC-machtigingen (Role-based access control) kan uw privéeindpunt automatisch worden goedgekeurd. Als u probeert verbinding te maken met een bron voor privékoppelingen zonder RBAC, gebruikt u de handmatige methode om de eigenaar van de bron de verbinding te laten goedkeuren.        |
|Bericht aanvragen     |  U een bericht opgeven dat gevraagde verbindingen handmatig moeten worden goedgekeurd. Dit bericht kan worden gebruikt om een specifiek verzoek te identificeren.        |
|Verbindingsstatus   |   Een alleen-lezen eigenschap die aangeeft of het privéeindpunt actief is. Alleen privéeindpunten in een goedgekeurde status kunnen worden gebruikt om verkeer te verzenden. Aanvullende staten beschikbaar: <br>-**Goedgekeurd**: Verbinding is automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt.</br><br>-**In behandeling**: Verbinding is handmatig gemaakt en wordt in afwachting van goedkeuring door de eigenaar van de privékoppelingsbron.</br><br>-**Afgewezen:** verbinding is geweigerd door de eigenaar van de private link-bron.</br><br>-**Losgekoppeld:** de verbinding is verwijderd door de eigenaar van de privékoppelingsbron. Het privéeindpunt wordt informatief en moet worden verwijderd voor het opruimen. </br>|

Hier zijn enkele belangrijke details over privé eindpunten: 
- Private endpoint maakt connectiviteit tussen de consumenten van dezelfde VNet, regionaal getouwde VNets, wereldwijd peered VNets en on premises met behulp van [VPN](https://azure.microsoft.com/services/vpn-gateway/) of [Express Route](https://azure.microsoft.com/services/expressroute/) en diensten aangedreven door Private Link.
 
- Bij het maken van een privéeindpunt wordt ook een netwerkinterface gemaakt voor de levenscyclus van de bron. Aan de interface wordt een privé-IP-adres toegewezen van het subnet dat wordt toegewezen aan de Private Link Service.
 
- Het privéeindpunt moet worden geïmplementeerd in dezelfde regio als het virtuele netwerk. 
 
- De private link-bron kan worden geïmplementeerd in een andere regio dan het virtuele netwerk en het privéeindpunt.
 
- Er kunnen meerdere privéeindpunten worden gemaakt met dezelfde private link-bron. Voor één netwerk met een gemeenschappelijke DNS-serverconfiguratie is de aanbevolen praktijk het gebruik van één privéeindpunt voor een bepaalde privékoppelingsbron om dubbele vermeldingen of conflicten in DNS-resolutie te voorkomen. 
 
- Meerdere privéeindpunten kunnen worden gemaakt op dezelfde of verschillende subnetten binnen hetzelfde virtuele netwerk. Er zijn grenzen aan het aantal privéeindpunten dat u in een abonnement maken. Zie [Azure-limieten voor](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)meer informatie.


 
## <a name="private-link-resource"></a>Private link-bron 
Een private link resource is het doeldoel van een bepaald privéeindpunt. Hieronder volgt een lijst met beschikbare bronnen voor privékoppelingen: 
 
|Naam van bron van privékoppeling  |Resourcetype   |Subbronnen  |
|---------|---------|---------|
|**Private Link Service** (Uw eigen service)   |  Microsoft.Network/privateLinkServices       | leeg |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabel (tabel, table_secondary)<BR> Wachtrij (wachtrij, queue_secondary)<BR> Bestand (bestand, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Gegevensmeerbestandssysteem Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Tafel|
|**Azure Database voor PostgreSQL -Single server** | Microsoft.DBforPostgreSQL/servers   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | mariadbServer |
|**Azure Key Vault** | Microsoft.KeyVault/kluizen    | kluis |
|**Azure Kubernetes-service - Kubernetes API** | Microsoft.ContainerService/managedClusters | managedCluster |
|**Azure Search** | Microsoft.Search/searchService| zoekenService|  
|**Azure-containerregister** | Microsoft.ContainerRegistry/registers  | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores   | configurationStore |
|**Azure Backup** | Microsoft.RecoveryServices/kluizen   | kluis |
|**Azure Event Hub** | Microsoft.EventHub/naamruimten    | naamruimte |
|**Azure Service Bus** | Microsoft.ServiceBus/naamruimten | naamruimte |
|**Azure Relay** | Microsoft.Relay/naamruimten | naamruimte |
|**Azure Event Grid** | Microsoft.EventGrid/onderwerpen  | onderwerp |
|**Azure Event Grid** | Microsoft.EventGrid/domeinen | domein |
|**Azure WebApps** | Microsoft.Web/sites    | site |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/werkruimten  | werkruimte |
  
 
## <a name="network-security-of-private-endpoints"></a>Netwerkbeveiliging van privéeindpunten 
Wanneer u privéeindpunten voor Azure-services gebruikt, wordt het verkeer beveiligd naar een specifieke privékoppelingsbron. Het platform voert een toegangscontrole uit om netwerkverbindingen te valideren die alleen de opgegeven privékoppelingsbron bereiken. Om toegang te krijgen tot extra bronnen binnen dezelfde Azure-service zijn extra privéeindpunten vereist. 
 
U uw workloads volledig vergrendelen vanaf toegang tot openbare eindpunten om verbinding te maken met een ondersteunde Azure-service. Dit besturingselement biedt een extra netwerkbeveiligingslaag voor uw resources door een ingebouwde exfiltratiebeveiliging te bieden die toegang voorkomt tot andere bronnen die op dezelfde Azure-service worden gehost. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Toegang tot een privékoppelingsbron via goedkeuringswerkstroom 
U verbinding maken met een private link-bron met behulp van de volgende goedkeuringsmethoden voor verbindingen:
- **Automatisch** goedgekeurd wanneer u eigenaar bent van of toestemming hebt voor de specifieke private link-bron. De vereiste machtiging is gebaseerd op het type privékoppelingsbron in de volgende indeling: Microsoft. \<Provider>/<resource_type>/privateEndpointConnectionApproval/action
- **Handmatig verzoek** wanneer u niet over de vereiste toestemming beschikt en toegang wilt aanvragen. Er wordt een goedkeuringswerkstroom gestart. Het privé-eindpunt en de erop volgende verbinding met het privé-eindpunt worden in de status In behandeling gemaakt. De eigenaar van de Private Link-resource moet de verbinding goedkeuren. Nadat het is goedgekeurd, is het privéeindpunt ingeschakeld om verkeer normaal te verzenden, zoals wordt weergegeven in het volgende goedkeuringswerkstroomdiagram.  

![werkstroomgoedkeuring](media/private-endpoint-overview/private-link-paas-workflow.png)
 
De eigenaar van de private link-bron kan de volgende acties uitvoeren via een privé-eindpuntverbinding: 
- Bekijk alle details van privéeindpuntverbindingen. 
- Een privé-eindpuntverbinding goedkeuren. Het bijbehorende privéeindpunt wordt ingeschakeld om verkeer naar de bron voor privékoppelingen te verzenden. 
- Een privé-eindpuntverbinding weigeren. Het bijbehorende privéeindpunt wordt bijgewerkt om de status weer te geven.
- Een privé-eindpuntverbinding in een bepaalde status verwijderen. Het bijbehorende privéeindpunt wordt bijgewerkt met een losgekoppelde status om de actie weer te geven, de eigenaar van het privéeindpunt kan de bron op dit punt alleen verwijderen. 
 
> [!NOTE]
> Alleen een privéeindpunt in een goedgekeurde status kan verkeer naar een bepaalde bron voor privékoppelingen verzenden. 

### <a name="connecting-using-alias"></a>Verbinding maken met alias
Alias is een unieke bijnaam die wordt gegenereerd wanneer de service-eigenaar de privékoppelingsservice achter een standaard load balancer maakt. De eigenaar van de service kan deze alias offline delen met zijn of haar consumenten. Consumenten kunnen een verbinding met de privékoppelingsservice aanvragen met behulp van de bron URI of de Alias. Als u verbinding wilt maken met Alias, moet u een privéeindpunt maken met behulp van een handmatige goedkeuringsmethode voor verbindingen. Voor het gebruik van handmatige verbindingsgoedkeuringsmethode stelt u de parameter Handmatige aanvraag in op true tijdens de handmatige stroom voor het maken van eindpunten. Kijk naar [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) en [az network private-endpoint create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) voor meer informatie. 

## <a name="dns-configuration"></a>DNS-configuratie 
Wanneer u verbinding maakt met een privékoppelingsbron met een volledig gekwalificeerde domeinnaam (FQDN) als onderdeel van de verbindingstekenreeks, is het belangrijk om uw DNS-instellingen correct te configureren om op te lossen op het toegewezen privé-IP-adres. Bestaande Azure-services hebben mogelijk al een DNS-configuratie die u gebruiken bij het maken via een openbaar eindpunt. Dit moet worden overschreven om verbinding te maken met uw privéeindpunt. 
 
De netwerkinterface die is gekoppeld aan het privéeindpunt bevat de volledige set informatie die nodig is om uw DNS te configureren, inclusief FQDN- en privé-IP-adressen die zijn toegewezen voor een bepaalde private link-bron. 
 
U de volgende opties gebruiken om uw DNS-instellingen voor privéeindpunten te configureren: 
- **Gebruik het hostbestand (alleen aanbevolen om te testen)**. U het hostbestand op een virtuele machine gebruiken om de DNS te overschrijven.  
- **Gebruik een privé-DNS-zone**. U privé-DNS-zones gebruiken om de DNS-resolutie voor een bepaald privéeindpunt te overschrijven. Een privé-DNS-zone kan worden gekoppeld aan uw virtuele netwerk om specifieke domeinen op te lossen.
- **Gebruik uw aangepaste DNS-server.** U uw eigen DNS-server gebruiken om de DNS-resolutie voor een bepaalde privékoppelingsbron te overschrijven. Als uw [DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) wordt gehost op een virtueel netwerk, u een DNS-doorstuurregel maken om een privé-DNS-zone te gebruiken om de configuratie voor alle privékoppelingsbronnen te vereenvoudigen.
 
> [!IMPORTANT]
> Het wordt afgeraden om een zone te overschrijven die actief wordt gebruikt om openbare eindpunten op te lossen. Verbindingen met resources kunnen niet correct worden opgelost zonder DNS door te sturen naar de openbare DNS. Maak een andere domeinnaam om problemen te voorkomen of volg de voorgestelde naam voor elke service hieronder. 
 
Gebruik voor Azure-services de aanbevolen zonenamen zoals beschreven in de volgende tabel:

|Resourcetype Private Link   |Subbron  |Zonenaam  |
|---------|---------|---------|
|SQL DB (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servers)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Opslagaccount (Microsoft.Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Opslagaccount (Microsoft.Storage/storageAccounts)    |    Tabel (tabel, table_secondary)      |   privatelink.table.core.windows.net       |
|Opslagaccount (Microsoft.Storage/storageAccounts)    |    Wachtrij (wachtrij, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Opslagaccount (Microsoft.Storage/storageAccounts)   |    Bestand (bestand, file_secondary)      |    privatelink.file.core.windows.net      |
|Opslagaccount (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Gegevenslake-bestandssysteem Gen2 (Microsoft.Storage/storageAccounts)  |  Gegevensmeerbestandssysteem Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Tabel|privatelink.table.cosmos.azure.com|
|Azure Database voor PostgreSQL - Single server (Microsoft.DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database voor MySQL (Microsoft.DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure-database voor MariaDB (Microsoft.DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft.KeyVault/vaults)|kluis|privatelink.vaultcore.azure.net|
|Azure Kubernetes-service - Kubernetes API (Microsoft.ContainerService/managedClusters) | managedCluster | {guid}.privatelink. <region>.azmk8s.io|
|Azure Search (Microsoft.Search/searchServices)|zoekenService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft.ContainerRegistry/registers) | registry | privatelink.azurecr.io |
|Configuratie van Azure-apps (Microsoft.Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Azure Backup (Microsoft.RecoveryServices/vaults)| kluis |privatelink. {region}.backup.windowsazure.com {region}.backup.windowsazure.com {region}.backup.windowsazure.com {region|
|Azure Event Hub (Microsoft.EventHub/namespaces)| naamruimte |privatelink.servicebus.windows.net|
|Azure-servicebus (Microsoft.ServiceBus/naamruimten) | naamruimte |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/naamruimten) | naamruimte |privatelink.servicebus.windows.net|
|Azure-gebeurtenisraster (Microsoft.EventGrid/onderwerpen)   | onderwerp | Onderwerp. {region}.privatelink.eventgrid.azure.net {region}.privatelink.eventgrid.azure.net {region}.privatelink.eventgrid.azure.net {region|
|Azure-gebeurtenisraster (Microsoft.EventGrid/domains) | domein | Domein. {region}.privatelink.eventgrid.azure.net {region}.privatelink.eventgrid.azure.net {region}.privatelink.eventgrid.azure.net {region |
|Azure WebApps (Microsoft.Web/sites)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning(Microsoft.MachineLearningServices/werkruimten)   | werkruimte | privatelink.api.azureml.ms |
 
Azure maakt een canonieke naam DNS-record (CNAME) op de openbare DNS om de resolutie om te leiden naar de voorgestelde domeinnamen. U de resolutie overschrijven met het privé-IP-adres van uw privéeindpunten. 
 
Uw toepassingen hoeven de url van de verbinding niet te wijzigen. Wanneer u probeert op te lossen met behulp van een openbare DNS, wordt de DNS-server nu opgelost naar uw privéeindpunten. Het proces heeft geen invloed op uw toepassingen. 
 
## <a name="limitations"></a>Beperkingen
 
In de volgende tabel wordt een lijst met bekende beperkingen opgenomen bij het gebruik van privéeindpunten: 


|Beperking |Beschrijving |Oplossing  |
|---------|---------|---------|
|NSG-regels (Network Security Group) en door gebruikers gedefinieerde routes zijn niet van toepassing op privéeindpunt    |NSG wordt niet ondersteund op privé eindpunten. Hoewel subnetten die het privéeindpunt bevatten, NSG eraan kunnen zijn gekoppeld, zijn de regels niet effectief op het verkeer dat door het privéeindpunt wordt verwerkt. U moet [de handhaving van het netwerkbeleid hebben uitgeschakeld](disable-private-endpoint-network-policy.md) om privéeindpunten in een subnet te implementeren. NSG wordt nog steeds afgedwongen op andere workloads die op hetzelfde subnet worden gehost. Routes op een clientsubnet gebruiken een /32-voorvoegsel, waarbij het standaardrouteringsgedrag wordt gewijzigd, een vergelijkbare UDR vereist  | Beheer het verkeer met behulp van NSG-regels voor uitgaand verkeer op bronclients. Implementeer afzonderlijke routes met /32-voorvoegsel om privéeindpuntroutes te overschrijven. NSG Flow-logboeken en bewakingsinformatie voor uitgaande verbindingen worden nog steeds ondersteund en kunnen worden gebruikt        |


## <a name="next-steps"></a>Volgende stappen
- [Een privéeindpunt voor SQL Database Server maken met Portal](create-private-endpoint-portal.md)
- [Een privéeindpunt voor SQL Database Server maken met PowerShell](create-private-endpoint-powershell.md)
- [Een privéeindpunt voor SQL Database Server maken met CLI](create-private-endpoint-cli.md)
- [Een privéeindpunt voor opslagaccount maken met Portal](create-private-endpoint-storage-portal.md)
- [Een privéeindpunt voor Azure Cosmos-account maken met Portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Uw eigen Private Link-service maken met Azure PowerShell](create-private-link-service-powershell.md)
- [Maak uw eigen Private Link voor Azure Database voor PostgreSQL - Single server met Portal](../postgresql/howto-configure-privatelink-portal.md)
- [Maak uw eigen Private Link voor Azure Database voor PostgreSQL - Enkele server met CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Uw eigen private koppeling voor Azure-database voor MySQL maken met Portal](../mysql/howto-configure-privatelink-portal.md)
- [Uw eigen private koppeling voor Azure-database voor MySQL maken met CLI](../mysql/howto-configure-privatelink-cli.md)
- [Uw eigen privékoppeling voor Azure-database voor MariaDB maken met Portal](../mariadb/howto-configure-privatelink-portal.md)
- [Uw eigen privékoppeling voor Azure-database voor MariaDB maken met CLI](../mariadb/howto-configure-privatelink-cli.md)
