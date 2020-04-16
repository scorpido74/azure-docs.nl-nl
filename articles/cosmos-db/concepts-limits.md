---
title: Azure Cosmos DB-servicequota
description: Azure Cosmos DB-servicequota en standaardlimieten voor verschillende resourcetypen.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: b24d7db679bb9cb9dacd5e1db8e6410b883548cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415697"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB-servicequota

Dit artikel bevat een overzicht van de standaardquota's die worden aangeboden aan verschillende resources in de Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Opslag en doorvoer

Nadat u onder uw abonnement een Azure Cosmos-account hebt gemaakt, u gegevens in uw account beheren door [databases, containers en items te maken.](databases-containers-items.md) U doorvoer op containerniveau of databaseniveau inrichten in termen van [aanvraageenheden (RU/s of RU's).](request-units.md) In de volgende tabel worden de limieten voor opslag en doorvoer per container/database weergegeven.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale R's per container ([speciale doorvoeringerichte modus)](databases-containers-items.md#azure-cosmos-containers) | Standaard 1.000.000. U dit verhogen door [een Azure-ondersteuningsticket in te dienen](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximale R's per database[(in de ingerichte modus gedeelde doorvoer)](databases-containers-items.md#azure-cosmos-containers) | Standaard 1.000.000. U dit verhogen door [een Azure-ondersteuningsticket in te dienen](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximale R's per (logische) partitiesleutel | 10.000 |
| Maximale opslag voor alle items per (logische) partitiesleutel| 20 GB |
| Maximum aantal afzonderlijke (logische) partitiesleutels | Onbeperkt |
| Maximale opslag per container | Onbeperkt |
| Maximale opslag per database | Onbeperkt |
| Maximale bijlagegrootte per account (bijlagefunctie wordt afgeschreven) | 2 GB |
| Minimale RALLY's vereist per 1 GB | 10 RU/s |

> [!NOTE]
> Zie [Een synthetische partitiesleutel maken](synthetic-partition-keys.md)voor meer informatie over aanbevolen procedures voor het beheren van workloads met partitiesleutels die hogere limieten voor opslag of doorvoer vereisen.
>

Een Cosmos-container (of gedeelde doorvoerdatabase) moet een minimale doorvoer van 400 RU's hebben. Naarmate de container groeit, is de minimale ondersteunde doorvoer ook afhankelijk van de volgende factoren:

* De minimale doorvoer die u op een container instellen, is afhankelijk van de maximale doorvoer die ooit op de container is ingesteld. Als uw doorvoer bijvoorbeeld is verhoogd tot 10000 RU's, is de laagst mogelijke ingerichte doorvoer 1000 RU's
* De minimale doorvoer op een gedeelde doorvoerdatabase is ook afhankelijk van het totale aantal containers dat u ooit hebt gemaakt in een gedeelde doorvoerdatabase, gemeten met 100 R's per container. Als u bijvoorbeeld vijf containers hebt gemaakt in een gedeelde doorvoerdatabase, moet de doorvoer ten minste 500 RU's zijn

De huidige en minimale doorvoer van een container of database kan worden opgehaald uit de Azure-portal of de SDK's. Zie [Doorvoer voorziening op containers en databases](set-throughput.md)voor meer informatie . 

> [!NOTE]
> In sommige gevallen u de doorvoer verlagen tot minder dan 10%. Gebruik de API om de exacte minimale R's per container te krijgen.
>

Samengevat, hier zijn de minimale ingerichte RU-limieten. 

| Resource | Standaardlimiet |
| --- | --- |
| Minimum-R's per container[(speciale doorvoeringerichte modus)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minimum-RALLY's per database[(in de ingerichte modus gedeelde doorvoer)](databases-containers-items.md#azure-cosmos-containers) | 400 |
| Minimum-RALLY's per container in een gedeelde doorvoerdatabase | 100 |

Cosmos DB ondersteunt elastische schaling van doorvoer (RU's) per container of database via de SDKs of portal. Elke container kan synchroon en onmiddellijk schalen binnen een schaalbereik van 10 tot 100 keer, tussen minimum- en maximumwaarden. Als de gevraagde doorvoerwaarde zich buiten het bereik bevindt, wordt schalen asynchroon uitgevoerd. Asynchrone schaling kan minuten tot uren duren, afhankelijk van de gevraagde doorvoer en gegevensopslaggrootte in de container.  

## <a name="control-plane-operations"></a>Bewerkingen van besturingsvlak

U [uw Azure Cosmos-account inrichten en beheren](how-to-manage-database-account.md) met de azure-portal-, Azure PowerShell-, Azure CLI- en Azure Resource Manager-sjablonen. In de volgende tabel worden de limieten per abonnement, account en aantal bewerkingen weergegeven.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale databaseaccounts per abonnement | Standaard 50. U dit verhogen door [een Azure-ondersteuningsticket in te dienen](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximum aantal regionale failovers | Standaard 1/uur. U dit verhogen door [een Azure-ondersteuningsticket in te dienen](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Regionale failovers zijn alleen van toepassing op afzonderlijke regioschrijft accounts. Schrijfaccounts met meerdere regio's vereisen geen beperkingen voor het wijzigen van het schrijfgebied.

Cosmos DB maakt automatisch regelmatig back-ups van uw gegevens. Zie [Online back-up en on-demand gegevensherstel in Azure Cosmos DB](online-backup-and-restore.md)voor meer informatie over back-upretentieintervallen en vensters.

## <a name="per-account-limits"></a>Limieten per account

| Resource | Standaardlimiet |
| --- | --- |
| Maximum aantal databases | Onbeperkt |
| Maximaal aantal containers per database met gedeelde doorvoer |25 |
| Maximum aantal containers per database of account met speciale doorvoer  |onbeperkt |
| Maximum aantal regio's | Geen limiet (Alle Azure-regio's) |

## <a name="per-container-limits"></a>Limieten per container

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-container een verzameling, een tabel of een grafiek vertegenwoordigen. Containers ondersteunen configuraties voor [unieke belangrijke beperkingen](unique-keys.md), opgeslagen [procedures, triggers en UDF's](stored-procedures-triggers-udfs.md)en [indexeringsbeleid](how-to-manage-indexing-policy.md). In de volgende tabel worden de grenzen vermeld die specifiek zijn voor configuraties in een container. 

| Resource | Standaardlimiet |
| --- | --- |
| Maximale lengte van de database of containernaam | 255 |
| Maximaal opgeslagen procedures per container | 100<sup>*</sup>|
| Maximale UDF's per container | 25<sup>*</sup>|
| Maximaal aantal paden in indexeringsbeleid| 100<sup>*</sup>|
| Maximaal aantal unieke sleutels per container|10<sup>*</sup>|
| Maximaal aantal paden per unieke sleutelbeperking|16<sup>*</sup>|

<sup>*</sup>U een van deze limieten per container verhogen door contact op te nemen met Azure Support.

## <a name="per-item-limits"></a>Limieten per artikel

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-item een document in een verzameling, een rij in een tabel of een knooppunt of rand in een grafiek weergeven. In de volgende tabel worden de limieten per item in Cosmos DB weergegeven. 

| Resource | Standaardlimiet |
| --- | --- |
| Maximale grootte van een item | 2 MB (UTF-8 lengte van JSON-representatie) |
| Maximale lengte van de waarde van de partitiesleutel | 2048 bytes |
| Maximale lengte van id-waarde | 1023 bytes |
| Maximum aantal eigenschappen per artikel | Geen praktische limiet |
| Maximale nestdiepte | Geen praktische limiet |
| Maximale lengte van de eigenschapsnaam | Geen praktische limiet |
| Maximale lengte van de eigenschapswaarde | Geen praktische limiet |
| Maximale lengte van de eigenschapswaarde string | Geen praktische limiet |
| Maximale lengte van de numerieke eigenschapswaarde | IEEE754 met dubbele precisie 64-bits |

Er zijn geen beperkingen op de item payloads zoals het aantal eigenschappen en nestdiepte, met uitzondering van de lengte beperkingen op partitiesleutel en id-waarden, en de totale grootte beperking van 2 MB. Mogelijk moet u indexeringsbeleid configureren voor containers met grote of complexe artikelstructuren om het RU-verbruik te verminderen. Zie [Modelleringsitems in Cosmos DB](how-to-model-partition-example.md) voor een voorbeeld in de echte wereld en patronen om grote items te beheren.

## <a name="per-request-limits"></a>Limieten per aanvraag

Azure Cosmos DB ondersteunt [CRUD- en querybewerkingen](https://docs.microsoft.com/rest/api/cosmos-db/) op basis van resources zoals containers, items en databases. Het ondersteunt ook [transactionele batchaanvragen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) voor meerdere artikelen met dezelfde partitiesleutel in een container.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale uitvoeringstijd voor één bewerking (zoals een opgeslagen procedureuitvoering of het ophalen van één querypagina)| 5 sec |
| Maximale aanvraaggrootte (bijvoorbeeld opgeslagen procedure, CRUD)| 2 MB |
| Maximale responsgrootte (bijvoorbeeld paginated query) | 4 MB |
| Maximum aantal bewerkingen in een transactionele batch | 100 |

Zodra een bewerking als query de time-out of responsgroottelimiet van de uitvoering heeft bereikt, retourneert deze een pagina met resultaten en een vervolgtoken aan de client om de uitvoering te hervatten. Er is geen praktische limiet voor de duur die een enkele query kan uitvoeren over pagina's/voortzettingen.

Cosmos DB gebruikt HMAC voor autorisatie. U een hoofdsleutel of een [resourcetokens](secure-access-to-data.md) gebruiken voor fijnmazige toegangscontrole tot bronnen zoals containers, partitiesleutels of items. In de volgende tabel worden limieten weergegeven voor autorisatietokens in Cosmos DB.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale vervaldatum van het mastertoken | 15 min  |
| Minimale vervaldatum van resourcetoken | 10 min  |
| Maximale vervaldatum van resourcetoken | Standaard 24 uur. U dit verhogen door [een Azure-ondersteuningsticket in te dienen](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximale klokscheeftrekking voor tokenautorisatie| 15 min |

Cosmos DB ondersteunt de uitvoering van triggers tijdens het schrijven. De service ondersteunt maximaal één pre-trigger en één post-trigger per schrijfbewerking. 

## <a name="autopilot-mode-limits"></a>Limieten voor de automatische pilootmodus

Zie het artikel [Autopilot](provision-throughput-autopilot.md#autopilot-limits) voor de doorvoer- en opslaglimieten in de automatische pilootmodus.

## <a name="sql-query-limits"></a>SQL-querylimieten

Cosmos DB ondersteunt queryitems met [SQL](how-to-sql-query.md). In de volgende tabel worden beperkingen in queryinstructies beschreven, bijvoorbeeld in termen van aantal clausules of queryduur.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale lengte van SQL-query| 256 kB |
| Maximale JOIN per query| 5<sup>*</sup>|
| Maximale UDF's per query| 10<sup>*</sup>|
| Maximale punten per veelhoek| 4096 |
| Maximaal opgenomen paden per container| 500 |
| Maximaal uitgesloten paden per container| 500 |
| Maximale eigenschappen in een samengestelde index| 8 |

<sup>*</sup>U deze SQL-querylimieten verhogen door contact op te nemen met Azure Support.

## <a name="mongodb-api-specific-limits"></a>MongoDB API-specifieke limieten

Cosmos DB ondersteunt het MongoDB draadprotocol voor toepassingen geschreven tegen MongoDB. U vindt de ondersteunde opdrachten en protocolversies op [ondersteunde MongoDB-functies en -syntaxis.](mongodb-feature-support.md)

In de volgende tabel worden de grenzen vermeld die specifiek zijn voor mongoDB-functieondersteuning. Andere servicelimieten die voor de SQL (core) API worden genoemd, zijn ook van toepassing op de MongoDB API.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale MongoDB-querygeheugengrootte (Deze beperking is alleen voor 3.2-serverversie) | 40 MB |
| Maximale uitvoeringstijd voor MongoDB-activiteiten| 30s |
| Time-out voor idle verbinding voor afsluiting van de verbinding aan de server* | 30 minuten |

\*We raden clienttoepassingen aan de time-out van de inactieve verbinding in de stuurprogramma-instellingen in te stellen op 2-3 minuten, omdat de [standaardtime-out voor Azure LoadBalancer 4 minuten bedraagt.](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)  Deze time-out zorgt ervoor dat niet-actieve verbindingen worden afgesloten door een tussenliggende load balancer tussen de clientmachine en Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Probeer Cosmos DB Free-limieten

In de volgende tabel worden de limieten weergegeven voor de proefversie [Van Azure Cosmos DB for Free.](https://azure.microsoft.com/try/cosmosdb/)

| Resource | Standaardlimiet |
| --- | --- |
| Duur van de proef | 30 dagen (kan een aantal keren worden verlengd) |
| Maximale containers per abonnement (SQL, Gremlin, Tabel API) | 1 |
| Maximale containers per abonnement (MongoDB API) | 3 |
| Maximale doorvoer per container | 5000 |
| Maximale doorvoer per database met gedeelde doorvoer | 20.000 |
| Maximale totale opslag per account | 10 GB |

Try Cosmos DB ondersteunt wereldwijde distributie in alleen de regio's Centraal-VS, Noord-Europa en Zuidoost-Azië. Azure-ondersteuningstickets kunnen niet worden gemaakt voor Try Azure Cosmos DB-accounts. Er wordt echter ondersteuning geboden aan abonnees met bestaande ondersteuningsplannen.

## <a name="free-tier-account-limits"></a>Limieten voor gratis rijen
In de volgende tabel worden de limieten voor [gratis laagaccounts van Azure Cosmos DB weergegeven.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Resource | Standaardlimiet |
| --- | --- |
| Aantal gratis laagaccounts per Azure-abonnement | 1 |
| Duur van de gratis korting | Levensduur van de rekening. Moet zich aanmelden tijdens het aanmaken van een account. |
| Maximale RU/s gratis | 400 RU/s |
| Maximale opslag gratis | 5 GB |
| Maximaal aantal gedeelde doorvoerdatabases | 5 |
| Maximaal aantal containers in een gedeelde doorvoerdatabase | 25 <br>In gratis laagaccounts is de minimale RU/s voor een gedeelde doorvoerdatabase met maximaal 25 containers 400 RU/s. |

  Naast het bovenstaande zijn de [limieten per account](#per-account-limits) ook van toepassing op gratis laagaccounts.

## <a name="next-steps"></a>Volgende stappen

Lees meer over cosmos DB's kernconcepten [wereldwijde distributie](distribute-data-globally.md) en [partitionering](partitioning-overview.md) en [ingerichte doorvoer](request-units.md).

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

* [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
* [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
* [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
* [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
* [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Probeer Azure Cosmos DB gratis uit](https://azure.microsoft.com/try/cosmosdb/)
