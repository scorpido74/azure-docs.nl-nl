---
title: Service quota's Azure Cosmos DB
description: Azure Cosmos DB Service quota's en standaard limieten voor verschillende resource typen.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 9e5b3e633bf35a25a68eb513d0ec691c3254bde4
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78674232"
---
# <a name="azure-cosmos-db-service-quotas"></a>Service quota's Azure Cosmos DB

Dit artikel bevat een overzicht van de standaardquota's die worden aangeboden aan verschillende resources in de Azure Cosmos DB.

## <a name="storage-and-throughput"></a>Opslag en door Voer

Nadat u in uw abonnement een Azure Cosmos-account hebt gemaakt, kunt u gegevens in uw account beheren door [data bases, containers en items te maken](databases-containers-items.md). U kunt de door Voer inrichten op container niveau of op database niveau in termen van [aanvraag eenheden (ru/s of RUs)](request-units.md). De volgende tabel bevat de limieten voor opslag en door Voer per container/data base.

| Resource | Standaardlimiet |
| --- | --- |
| Maximum RUs per container ([ingerichte modus toegewezen door Voer](databases-containers-items.md#azure-cosmos-containers)) | standaard 1.000.000. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximum RUs per data base ([ingerichte modus voor gedeelde door Voer](databases-containers-items.md#azure-cosmos-containers)) | standaard 1.000.000. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) |
| Maximum-RUs per (logische) partitie sleutel | 10.000 |
| Maximale opslag ruimte in alle items per (logische) partitie sleutel| 20 GB |
| Maximum aantal afzonderlijke (logische) partitie sleutels | Onbeperkt |
| Maximale opslag per container | Onbeperkt |
| Maximale opslag per data base | Onbeperkt |
| Maximale grootte van bijlagen per account (bijlage functie wordt afgeschreven) | 2 GB |
| Mini maal RUs vereist per 1 GB | 10 RU/s |

> [!NOTE]
> Zie [een synthetische partitie sleutel maken](synthetic-partition-keys.md)voor meer informatie over aanbevolen procedures voor het beheren van werk belastingen met partitie sleutels waarvoor hogere limieten vereist zijn voor opslag of door voer.
>

Een Cosmos-container (of een gedeelde doorvoer database) moet een minimum doorvoer van 400 RUs hebben. Naarmate de container groeit, is de mini maal ondersteunde door Voer ook afhankelijk van de volgende factoren:

* De minimale door Voer die u op een container kunt instellen, is afhankelijk van de maximale door Voer die ooit is ingericht op de container. Als uw door Voer bijvoorbeeld is verhoogd naar 10000 RUs, zou de laagst mogelijke ingerichte door Voer 1000 zijn.
* De minimale door Voer voor een Data Base met gedeelde door Voer is ook afhankelijk van het totale aantal containers dat u ooit hebt gemaakt in een gedeelde doorvoer database, gemeten op basis van 100 RUs per container. Als u bijvoorbeeld vijf containers hebt gemaakt in een gedeelde doorvoer database, moet de door Voer ten minste 500 RUs zijn

De huidige en minimale door Voer van een container of een Data Base kunnen worden opgehaald uit de Azure Portal of de Sdk's. Zie [door Voer inrichten voor containers en data bases](set-throughput.md)voor meer informatie. 

> [!NOTE]
> In sommige gevallen kunt u de door Voer verlagen tot minder dan 10%. Gebruik de API om het exacte minimale RUs per container op te halen.
>

In samen vatting vindt u de minimale ingerichte RU-limieten. 

| Resource | Standaardlimiet |
| --- | --- |
| Mini maal RUs per container ([ingerichte modus toegewezen door Voer](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Mini maal RUs per data base ([ingerichte modus voor gedeelde door Voer](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Mini maal RUs per container in een gedeelde doorvoer database | 100 |

Cosmos DB ondersteunt elastisch schalen van door Voer (RUs) per container of Data Base via de Sdk's of portal. Elke container kan synchroon en direct binnen een schaal bereik van 10 tot 100 keer worden geschaald, tussen de minimum-en maximum waarden. Als de aangevraagde doorvoer waarde zich buiten het bereik bevindt, wordt de schaal asynchroon uitgevoerd. Het volt ooien van asynchrone schaling kan minuten tot uren duren, afhankelijk van de gevraagde grootte van door Voer en gegevens opslag in de container.  

## <a name="control-plane-operations"></a>Bewerkingen voor het beheer vlak

U kunt [uw Azure Cosmos-account inrichten en beheren](how-to-manage-database-account.md) met behulp van de Azure Portal-, Azure PowerShell-, Azure CLI-en Azure Resource Manager-sjablonen. De volgende tabel bevat de limieten per abonnement, account en aantal bewerkingen.

| Resource | Standaardlimiet |
| --- | --- |
| Maximum aantal database accounts per abonnement | standaard 50. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximum aantal regionale failovers | standaard 1/uur. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|

> [!NOTE]
> Regionale failovers zijn alleen van toepassing op de ene regio en schrijft accounts. Voor schrijf accounts met meerdere regio's zijn geen beperkingen vereist voor het wijzigen van de schrijf regio.

Cosmos DB maakt met regel matige tussen pozen automatisch back-ups van uw gegevens. Zie [online back-ups en gegevens herstel op aanvraag in azure Cosmos DB](online-backup-and-restore.md)voor meer informatie over retentie-intervallen en Windows-back-ups.

## <a name="per-account-limits"></a>Limieten per account

| Resource | Standaardlimiet |
| --- | --- |
| Maximum aantal data bases | Onbeperkt |
| Maximum aantal containers per data base met gedeelde door Voer |25 |
| Maximum aantal containers per data base of account met toegewezen door Voer  |onbeperkt |
| Maximum aantal regio's | Geen limiet (alle Azure-regio's) |

## <a name="per-container-limits"></a>Limieten per container

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-container een verzameling, een tabel of een grafiek vertegenwoordigen. Containers ondersteunen configuraties voor [unieke sleutel beperkingen](unique-keys.md), [opgeslagen procedures, triggers en udf's](stored-procedures-triggers-udfs.md)en [indexerings beleid](how-to-manage-indexing-policy.md). De volgende tabel bevat de limieten die specifiek zijn voor configuraties binnen een container. 

| Resource | Standaardlimiet |
| --- | --- |
| Maximale lengte van de data base of container naam | 255 |
| Maximum aantal opgeslagen procedures per container | 100 <sup>*</sup>|
| Maximum aantal Udf's per container | 25 <sup>*</sup>|
| Maximum aantal paden in het indexerings beleid| 100 <sup>*</sup>|
| Maximum aantal unieke sleutels per container|10 <sup>*</sup>|
| Maximum aantal paden per beperking van de unieke sleutel|16 <sup>*</sup>|

<sup>*</sup> U kunt deze limieten per container verhogen door contact op te nemen met de ondersteuning van Azure.

## <a name="per-item-limits"></a>Limieten per item

Afhankelijk van de API die u gebruikt, kan een Azure Cosmos-item een document in een verzameling, een rij in een tabel of een knoop punt of rand in een grafiek vertegenwoordigen. In de volgende tabel ziet u de limieten per item in Cosmos DB. 

| Resource | Standaardlimiet |
| --- | --- |
| Maximale grootte van een item | 2 MB (UTF-8-lengte van JSON-weer gave) |
| Maximale lengte van partitie sleutel waarde | 2048 bytes |
| Maximale lengte van id-waarde | 1023 bytes |
| Maximum aantal eigenschappen per item | Geen praktische limiet |
| Maximale nest diepte | Geen praktische limiet |
| Maximale lengte van eigenschaps naam | Geen praktische limiet |
| Maximale lengte van eigenschaps waarde | Geen praktische limiet |
| Maximale lengte van de waarde van de teken reeks eigenschap | Geen praktische limiet |
| Maximale lengte van de waarde van de numerieke eigenschap | IEEE754 met dubbele precisie van 64 bits |

Er zijn geen beperkingen voor de nettoladingen van het item, zoals het aantal eigenschappen en de diepte van het nesten, met uitzonde ring van de lengte beperkingen voor de waarden van de partitie sleutel en-id en de totale grootte beperking van 2 MB. Mogelijk moet u het indexerings beleid voor containers met grote of complexe item structuren configureren om het gebruik van RU te verminderen. Zie [modellerings items in Cosmos DB](how-to-model-partition-example.md) voor een echt voor beeld en patronen voor het beheren van grote items.

## <a name="per-request-limits"></a>Limieten per aanvraag

Azure Cosmos DB ondersteunt [ruwe en query bewerkingen](https://docs.microsoft.com/rest/api/cosmos-db/) voor resources zoals containers, items en data bases. Het biedt ook ondersteuning voor [transactionele batch-aanvragen](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch) voor meerdere items met dezelfde partitie sleutel in een container.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale uitvoerings tijd voor één bewerking (zoals het uitvoeren van een opgeslagen procedure of het ophalen van één query pagina)| 5 sec |
| Maximale aanvraag grootte (bijvoorbeeld opgeslagen procedure, ruw)| 2 MB |
| Maximale grootte van antwoorden (bijvoorbeeld gepagineerde query) | 4 MB |
| Maximum aantal bewerkingen in een transactionele batch | 100 |

Zodra een bewerking als query de time-out voor de uitvoering of de grootte van de reactie bereikt, wordt er een pagina met resultaten en een vervolg token naar de client geretourneerd om de uitvoering te hervatten. Er is geen praktische limiet voor de duur dat één query op meerdere pagina's/voortzettingen kan worden uitgevoerd.

Cosmos DB gebruikt HMAC voor autorisatie. U kunt een hoofd sleutel of een [bron tokens](secure-access-to-data.md) gebruiken voor nauw keurig toegangs beheer voor bronnen als containers, partitie sleutels of items. De volgende tabel bevat de limieten voor autorisatie tokens in Cosmos DB.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale verloop tijd van Master token | 15 min  |
| Minimale verloop tijd van bron token | 10 minuten  |
| Maximale verloop tijd van bron token | standaard 24 uur. U kunt deze verhogen door [een ondersteunings ticket voor Azure te archiveren](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)|
| Maximale klok scheefheid voor token autorisatie| 15 min |

Cosmos DB ondersteunt de uitvoering van triggers tijdens het schrijven. De service ondersteunt Maxi maal één pretrigger en één post-trigger per schrijf bewerking. 

## <a name="autopilot-mode-limits"></a>Limieten voor automatische test modus

Zie het artikel auto [pilot](provision-throughput-autopilot.md#autopilot-limits) voor de limieten voor door Voer en opslag in de modus Auto Pilot.

## <a name="sql-query-limits"></a>SQL-query limieten

Cosmos DB biedt ondersteuning voor het uitvoeren van query's op items met [SQL](how-to-sql-query.md). In de volgende tabel worden de beperkingen in query-instructies beschreven, bijvoorbeeld in termen van het aantal componenten of de lengte van de query.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale lengte van SQL-query| 256 KB <sup>*</sup>|
| Maximum aantal JOIN'S per query| 5 <sup>*</sup>|
| Maximum aantal and per query| 2000 <sup>*</sup>|
| Maximum aantal or per query| 2000 <sup>*</sup>|
| Maximum aantal Udf's per query| 10 <sup>*</sup>|
| Maximum aantal argumenten per IN expressie| 6000 <sup>*</sup>|
| Maximum aantal punten per veelhoek| 4096 <sup>*</sup>|

<sup>*</sup> U kunt deze limieten voor SQL-query's verhogen door contact op te nemen met de ondersteuning van Azure.

## <a name="mongodb-api-specific-limits"></a>MongoDB-API-specifieke limieten

Cosmos DB ondersteunt het MongoDB wire-protocol voor toepassingen die zijn geschreven tegen MongoDB. U kunt de ondersteunde opdrachten en protocol versies vinden op [ondersteunde MongoDb-functies en-syntaxis](mongodb-feature-support.md).

De volgende tabel bevat de limieten die specifiek zijn voor de ondersteuning van MongoDB-functies. Andere service limieten die worden vermeld voor de SQL-API (core), zijn ook van toepassing op de MongoDB-API.

| Resource | Standaardlimiet |
| --- | --- |
| Maximale geheugen grootte van MongoDB-query | 40 MB |
| Maximale uitvoerings tijd voor MongoDB-bewerkingen| 30s |
| Time-out voor niet-actieve verbinding voor sluiting verbinding aan server zijde * | 30 minuten |

\* het is raadzaam dat client toepassingen de time-out voor niet-actieve verbindingen in de stuur programma-instellingen instellen op 2-3 minuten omdat de [standaardtime-out voor Azure LoadBalancer 4 minuten is](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout).  Met deze time-out wordt voor komen dat niet-actieve verbindingen worden gesloten door een tussenliggende load balancer tussen de client computer en Azure Cosmos DB.

## <a name="try-cosmos-db-free-limits"></a>Probeer Cosmos DB gratis limieten

De volgende tabel bevat de limieten voor de gratis proef versie [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) .

| Resource | Standaardlimiet |
| --- | --- |
| Duur van de proef versie | 30 dagen (kan een wille keurig aantal keer worden vernieuwd) |
| Maximum aantal containers per abonnement (SQL, Gremlin, Table-API) | 1 |
| Maximum aantal containers per abonnement (MongoDB-API) | 3 |
| Maximale door Voer per container | 5000 |
| Maximale door Voer per Data Base voor gedeelde door Voer | 20.000 |
| Maximale totale opslag per account | 10 GB |

Probeer Cosmos DB wereld wijde distributie alleen te ondersteunen in de regio's VS-centraal, Europa-noord en Zuidoost-Azië. Er kunnen geen ondersteunings tickets voor Azure worden gemaakt voor het proberen van Azure Cosmos DB accounts. Er wordt echter ondersteuning geboden voor abonnees met bestaande ondersteunings abonnementen.

## <a name="free-tier-account-limits"></a>Limieten voor gratis laag accounts
De volgende tabel bevat de limieten voor [Azure Cosmos DB gratis laag accounts.](optimize-dev-test.md#azure-cosmos-db-free-tier)

| Resource | Standaardlimiet |
| --- | --- |
| Aantal gratis laag accounts per Azure-abonnement | 1 |
| Duur van de gratis laag korting | De levens duur van het account. Moet zich aanmelden tijdens het maken van het account. |
| Maxi maal aantal RU/s gratis | 400 RU/s |
| Maximale opslag voor gratis | 5 GB |
| Maximum aantal gedeelde doorvoer databases | 5 |
| Maximum aantal containers in een gedeelde doorvoer database | 25 <br>In accounts met een gratis laag is de minimale RU/s voor een gedeelde doorvoer database met Maxi maal 25 containers 400 RU/s. |

  Naast het bovenstaande zijn de [limieten per account](#per-account-limits) ook van toepassing op gratis laag accounts.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de belangrijkste concepten van Cosmos DB voor [wereld wijde distributie](distribute-data-globally.md) en [partitionering](partitioning-overview.md) en [ingerichte door Voer](request-units.md).

Lees onze snelstartgidsen om snel aan de slag te gaan met Azure Cosmos DB:

* [Aan de slag met de SQL-API van Azure Cosmos DB](create-sql-api-dotnet.md)
* [Aan de slag met de API van Azure Cosmos DB voor MongoDB](create-mongodb-nodejs.md)
* [Aan de slag met de Cassandra-API van Azure Cosmos DB](create-cassandra-dotnet.md)
* [Aan de slag met de Gremlin-API van Azure Cosmos DB](create-graph-dotnet.md)
* [Aan de slag met de Table-API van Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB gratis uitproberen](https://azure.microsoft.com/try/cosmosdb/)
