---
title: Wat is een persoonlijk Azure-eind punt?
description: Meer informatie over Azure persoonlijk eind punt
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: a4117acb2fada5c4422e177e9e6b84d7a0a51b69
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129324"
---
# <a name="what-is-azure-private-endpoint"></a>Wat is een persoonlijk Azure-eind punt?

Een privé-eindpunt in Azure is een netwerkinterface waarmee u privé en veilig verbinding maakt met een service die door Azure Private Link mogelijk wordt gemaakt. Privé-eind punt gebruikt een privé-IP-adres uit uw VNet, waardoor de service effectief in uw VNet wordt gezet. De service kan een Azure-service zijn, zoals Azure Storage, Azure Cosmos DB, SQL, etc. of uw eigen [persoonlijke koppelings service](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Eigenschappen van persoonlijk eind punt 
 Een persoonlijk eind punt geeft de volgende eigenschappen aan: 


|Eigenschap  |Beschrijving |
|---------|---------|
|Naam    |    Een unieke naam binnen de resource groep.      |
|Subnet    |  Het subnet voor het implementeren en toewijzen van privé-IP-adressen van een virtueel netwerk. Zie de sectie beperkingen in dit artikel voor meer informatie over het subnet.         |
|Persoonlijke koppelings bron    |   De bron van de persoonlijke koppeling om verbinding te maken met behulp van de resource-ID of alias, uit de lijst met beschik bare typen. Er wordt een unieke netwerk-id gegenereerd voor al het verkeer dat wordt verzonden naar deze bron.       |
|Doel-subresource   |      De subresource waarmee verbinding moet worden gemaakt. Elk resource type voor een persoonlijke koppeling heeft verschillende opties om te selecteren op basis van voor keur.    |
|Goedkeurings methode voor verbinding    |  Automatisch of hand matig. Op basis van RBAC-machtigingen (op rollen gebaseerd toegangs beheer) kan uw persoonlijke eind punt automatisch worden goedgekeurd. Als u probeert verbinding te maken met een persoonlijke koppelings bron zonder RBAC, gebruikt u de hand matige methode om de eigenaar van de resource toe te staan de verbinding goed te keuren.        |
|Aanvraag bericht     |  U kunt een bericht opgeven dat de aangevraagde verbindingen hand matig moeten worden goedgekeurd. Dit bericht kan worden gebruikt om een specifieke aanvraag te identificeren.        |
|Verbindingsstatus   |   Een alleen-lezen eigenschap waarmee wordt opgegeven of het persoonlijke eind punt actief is. Alleen persoonlijke eind punten in een goedgekeurde status kunnen worden gebruikt om verkeer te verzenden. Aanvullende beschik bare statussen: <br>-**Goedgekeurd**: de verbinding is automatisch of hand matig goedgekeurd en is klaar om te worden gebruikt.</br><br>-**In behandeling**: de verbinding is hand matig gemaakt en in afwachting van goed keuring door de resource-eigenaar van de persoonlijke koppeling.</br><br>-**Geweigerd**: de verbinding is geweigerd door de resource-eigenaar van de persoonlijke koppeling.</br><br>-**Verbinding verbroken**: de verbinding is verwijderd door de resource-eigenaar van de persoonlijke koppeling. Het persoonlijke eind punt wordt informatieve en moet worden verwijderd voor opschoning. </br>|

Hier volgen enkele belang rijke informatie over persoonlijke eind punten: 
- Met een privé-eind punt is connectiviteit tussen de gebruikers van hetzelfde VNet, regionale, VNets, wereld wijd verbonden VNets en on-premises mogelijk via [VPN-](https://azure.microsoft.com/services/vpn-gateway/) of [Express route](https://azure.microsoft.com/services/expressroute/) en services die worden aangedreven door privé-verbinding.
 
- Wanneer u een persoonlijk eind punt maakt, wordt er ook een netwerk interface gemaakt voor de levens cyclus van de resource. Aan de interface wordt een privé-IP-adres toegewezen uit het subnet dat is gekoppeld aan de service voor persoonlijke koppelingen.
 
- Het persoonlijke eind punt moet worden geïmplementeerd in dezelfde regio als het virtuele netwerk. 
 
- De bron van de persoonlijke koppeling kan worden geïmplementeerd in een andere regio dan het virtuele netwerk en het persoonlijke eind punt.
 
- Er kunnen meerdere persoonlijke eind punten worden gemaakt met behulp van dezelfde persoonlijke koppelings resource. Voor één netwerk dat gebruikmaakt van een gemeen schappelijke DNS-server configuratie, is het aanbevolen een enkel persoonlijk eind punt te gebruiken voor een bepaalde persoonlijke koppelings bron om dubbele vermeldingen of conflicten in DNS-omzetting te voor komen. 
 
- Meerdere persoonlijke eind punten kunnen worden gemaakt op dezelfde of verschillende subnetten binnen hetzelfde virtuele netwerk. Er zijn limieten voor het aantal privé-eind punten dat u in een abonnement kunt maken. Zie [Azure-limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)voor meer informatie.


 
## <a name="private-link-resource"></a>Persoonlijke koppelings bron 
Een persoonlijke koppelings bron is het doel doel van een gegeven persoonlijk eind punt. Hier volgt een lijst met beschik bare resource typen voor persoonlijke koppelingen: 
 
|Naam van de resource van de persoonlijke koppeling  |Resourcetype   |Subbronnen  |
|---------|---------|---------|
|**Privé koppelings service** (uw eigen service)   |  Micro soft. Network/privateLinkServices       | leeg |
|**Azure SQL Database** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL Server (sqlServer)        | 
|**Azure Storage**  | Micro soft. Storage/Storage accounts    |  BLOB (BLOB, blob_secondary)<BR> Tabel (tabel, table_secondary)<BR> Wachtrij (wachtrij, queue_secondary)<BR> Bestand (bestand, file_secondary)<BR> Web (Web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Micro soft. Storage/Storage accounts    |  BLOB (BLOB, blob_secondary)<BR> Data Lake File System Gen2 (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Micro soft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, tabel|
|**Azure Database for PostgreSQL-één server** | Micro soft. DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Micro soft. DBforMySQL/servers    | mysqlServer |
|**Azure Database for MariaDB** | Micro soft. DBforMariaDB/servers    | mariadbServer |
|**Azure Key Vault** | Micro soft.-sleutel kluis/-kluizen    | kluis |
|**Azure Kubernetes-service-Kubernetes-API** | Micro soft. container service/managedClusters    | managedCluster |
|**Azure Search** | Micro soft. Search/searchService| searchService|  
|**Azure Container Registry** | Micro soft. ContainerRegistry/registers    | registry |
|**Azure App Configuration** | Micro soft. Appconfiguration/configurationStores    | configurationStore |
|**Azure Backup** | Micro soft. Recovery Services/kluizen    | kluis |
|**Azure Event Hub** | Micro soft. EventHub/naam ruimten    | naamruimte |
|**Azure Service Bus** | Micro soft. ServiceBus/naam ruimten | naamruimte |
|**Azure Relay** | Micro soft. relay/naam ruimten | naamruimte |
|**Azure Event Grid** | Micro soft. EventGrid/topics    | onderwerp |
|**Azure Event Grid** | Micro soft. EventGrid/domeinen    | domein |
|**Azure webapps** | Micro soft. web/sites    | site |
|**Azure Machine Learning** | Micro soft. MachineLearningServices/werk ruimten    | werkruimte |
  
 
## <a name="network-security-of-private-endpoints"></a>Netwerk beveiliging van privé-eind punten 
Wanneer u privé-eind punten voor Azure-Services gebruikt, wordt het verkeer beveiligd met een specifieke persoonlijke koppelings bron. Het platform voert een toegangs controle uit om netwerk verbindingen te valideren die alleen de opgegeven persoonlijke koppelings bron bereiken. Als u toegang wilt krijgen tot extra resources binnen dezelfde Azure-service, zijn er extra persoonlijke eind punten vereist. 
 
U kunt uw workloads volledig vergren delen voor het openen van open bare eind punten om verbinding te maken met een ondersteunde Azure-service. Dit besturings element biedt een extra netwerkbeveiligingslaag aan uw resources door een ingebouwde exfiltration-beveiliging te bieden waarmee toegang tot andere resources die worden gehost op dezelfde Azure-service, wordt voor komen. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Toegang tot een persoonlijke koppelings bron via een goedkeurings werk stroom 
U kunt verbinding maken met een persoonlijke koppelings bron met behulp van de volgende verbindings goedkeurings methoden:
- **Automatisch** goedgekeurd wanneer u beschikt over een machtiging voor de specifieke persoonlijke koppelings bron. De vereiste machtiging is gebaseerd op het resource type persoonlijke koppeling in de volgende indeling: micro soft. \<>/<van Provider resource_type>/privateendpointconnectionapproval/Action
- **Hand matige** aanvraag wanneer u niet over de vereiste machtigingen beschikt en u toegang wilt aanvragen. Er wordt een goedkeurings werk stroom gestart. Het privé-eindpunt en de erop volgende verbinding met het privé-eindpunt worden in de status In behandeling gemaakt. De eigenaar van de Private Link-resource moet de verbinding goedkeuren. Nadat het is goedgekeurd, is het persoonlijke eind punt ingeschakeld voor het normaal verzenden van verkeer, zoals wordt weer gegeven in het volgende goedkeurings werk stroom diagram.  

![workflowgoedkeuring](media/private-endpoint-overview/private-link-paas-workflow.png)
 
De eigenaar van de resource van de persoonlijke koppeling kan de volgende acties uitvoeren via een verbinding met een privé-eind punt: 
- Bekijk alle details van het particuliere eind punt-verbinding. 
- Een persoonlijke eindpunt verbinding goed keuren. Het bijbehorende persoonlijke eind punt wordt ingeschakeld voor het verzenden van verkeer naar de bron van de persoonlijke koppeling. 
- Een persoonlijke eindpunt verbinding afwijzen. Het bijbehorende persoonlijke eind punt wordt bijgewerkt om de status weer te geven.
- Een particuliere-eindpunt verbinding in een wille keurige staat verwijderen. Het bijbehorende persoonlijke eind punt wordt bijgewerkt met de status niet verbonden om de actie weer te geven. de eigenaar van het privé-eind punt kan de resource op dit moment alleen verwijderen. 
 
> [!NOTE]
> Alleen een persoonlijk eind punt met een goedgekeurde status kan verkeer verzenden naar een bepaalde persoonlijke koppelings bron. 

### <a name="connecting-using-alias"></a>Verbinding maken via alias
Alias is een unieke moniker die wordt gegenereerd wanneer de service-eigenaar de privé koppelings service maakt achter een standaard load balancer. De eigenaar van de service kan deze alias met hun consumenten offline delen. Consumenten kunnen een verbinding met een privé koppelings service aanvragen met behulp van de resource-URI of de alias. Als u verbinding wilt maken met behulp van een alias, moet u een persoonlijk eind punt maken met de methode hand matige goed keuring voor verbinding. Stel voor het gebruik van hand matige goedkeurings methode de hand matige aanvraag parameter in op True tijdens het maken van een persoonlijk eind punt. Kijk naar [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) en [AZ Network private-endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) voor meer informatie. 

## <a name="dns-configuration"></a>DNS-configuratie 
Wanneer u verbinding maakt met een persoonlijke koppelings bron met behulp van een Fully Qualified Domain Name (FQDN) als onderdeel van de connection string, is het belang rijk om uw DNS-instellingen correct te configureren om te worden omgezet in het toegewezen privé-IP-adres. Bestaande Azure-Services hebben mogelijk al een DNS-configuratie die kan worden gebruikt om verbinding te maken via een openbaar eind punt. Deze moet worden overschreven om verbinding te maken via uw persoonlijke eind punt. 
 
De netwerk interface die aan het persoonlijke eind punt is gekoppeld, bevat de volledige set informatie die nodig is voor het configureren van uw DNS, inclusief FQDN-en privé IP-adressen die zijn toegewezen voor een bepaalde persoonlijke koppelings bron. 

Voor gedetailleerde informatie over aanbevolen procedures en aanbevelingen voor het configureren van DNS voor privé-eind punten raadpleegt u het artikel over de [DNS-configuratie van het privé-eind punt](private-endpoint-dns.md).



 
## <a name="limitations"></a>Beperkingen
 
De volgende tabel bevat een lijst met bekende beperkingen bij het gebruik van privé-eind punten: 


|Beperking |Beschrijving |Oplossing  |
|---------|---------|---------|
|De regels voor de netwerk beveiligings groep (NSG) en door de gebruiker gedefinieerde routes zijn niet van toepassing op een privé-eind punt    |NSG wordt niet ondersteund op privé-eind punten. Hoewel aan subnetten met het persoonlijke eind punt NSG zijn gekoppeld, zijn de regels niet effectief op het verkeer dat door het persoonlijke eind punt wordt verwerkt. U moet [netwerk beleid afdwingen uitgeschakeld](disable-private-endpoint-network-policy.md) om persoonlijke eind punten te implementeren in een subnet. NSG wordt nog steeds afgedwongen op andere workloads die worden gehost op hetzelfde subnet. Voor routes op elk client subnet wordt het voor voegsel/32 gebruikt. voor het wijzigen van het standaard routerings gedrag is een vergelijk bare UDR vereist  | Beheer het verkeer met behulp van NSG-regels voor uitgaand verkeer op de bron-clients. Implementeer afzonderlijke routes met het voor voegsel/32 om persoonlijke eindpunt routes te negeren. NSG-stroom logboeken en controle gegevens voor uitgaande verbindingen worden nog steeds ondersteund en kunnen worden gebruikt        |


## <a name="next-steps"></a>Volgende stappen
- [Een persoonlijk eind punt voor SQL Database Server maken met behulp van portal](create-private-endpoint-portal.md)
- [Een persoonlijk eind punt voor SQL Database Server maken met behulp van Power shell](create-private-endpoint-powershell.md)
- [Een persoonlijk eind punt voor SQL Database Server maken met behulp van CLI](create-private-endpoint-cli.md)
- [Een persoonlijk eind punt maken voor opslag account met behulp van portal](create-private-endpoint-storage-portal.md)
- [Een persoonlijk eind punt voor Azure Cosmos-account maken met behulp van portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Uw eigen persoonlijke koppelings service maken met behulp van Azure PowerShell](create-private-link-service-powershell.md)
- [Uw eigen persoonlijke koppeling maken voor Azure Database for PostgreSQL-één server met behulp van portal](../postgresql/howto-configure-privatelink-portal.md)
- [Uw eigen persoonlijke koppeling maken voor Azure Database for PostgreSQL-één server met behulp van CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Uw eigen persoonlijke koppeling maken voor Azure Database for MySQL met behulp van de portal](../mysql/howto-configure-privatelink-portal.md)
- [Uw eigen persoonlijke koppeling maken voor Azure Database for MySQL met behulp van CLI](../mysql/howto-configure-privatelink-cli.md)
- [Uw eigen persoonlijke koppeling maken voor Azure Database for MariaDB met behulp van de portal](../mariadb/howto-configure-privatelink-portal.md)
- [Uw eigen persoonlijke koppeling maken voor Azure Database for MariaDB met behulp van CLI](../mariadb/howto-configure-privatelink-cli.md)
