---
title: Informatie over Azure Cosmos DB bewakings gegevens | Microsoft Docs
description: Naslag informatie over Logboeken en metrieken voor het bewaken van gegevens van Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: how-to
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: 446d876033b09728ebcbec43c6300884a5c29cd3
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85262732"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Naslaginformatie over Azure Cosmos DB-bewakingsgegevens
Dit artikel bevat naslaginformatie over logboek- en metrische gegevens die worden verzameld om de prestaties en beschikbaarheid van Azure Cosmos DB te analyseren. Zie [bewaking Cosmos DB](monitor-cosmos-db.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Cosmos db.


## <a name="resource-logs"></a>Resourcelogboeken
De volgende tabel geeft een lijst van de eigenschappen voor Azure Cosmos DB bron Logboeken wanneer ze worden verzameld in Azure Monitor Logboeken of Azure Storage. In Azure Monitor logboeken worden deze verzameld in de tabel **AzureDiagnostics** met een **resource provider** -waarde van *MICROSOFT.DOCUMENTDB*. 

| Azure Storage veld of eigenschap | Eigenschap Azure Monitor logboeken | Beschrijving |
| --- | --- | --- |
| **tegelijk** | **TimeGenerated** | De datum en tijd (UTC) waarop de bewerking plaatsvond. |
| **resourceId** | **Resource** | Het Azure Cosmos DB account waarvoor logboeken zijn ingeschakeld.|
| **rubriek** | **Categorie** | Voor Azure Cosmos DB logs zijn **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** de beschik bare logboek typen. |
| **operationName** | **OperationName** | De naam van de bewerking. Deze waarde kan een van de volgende bewerkingen zijn: maken, bijwerken, lezen, ReadFeed, verwijderen, vervangen, uitvoeren, SqlQuery, query, JSQuery, Head, HeadFeed of Upsert.   |
| **eigenschappen** | N.v.t. | De inhoud van dit veld wordt beschreven in de volgende rijen. |
| **activityId** | **activityId_g** | De unieke GUID voor de geregistreerde bewerking. |
| **User agent** | **userAgent_s** | Een teken reeks die de gebruikers agent van de client opgeeft die de aanvraag uitvoert. De indeling is {naam van de gebruikers agent}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Het type van de resource waartoe toegang wordt verkregen. Deze waarde kan een van de volgende resource typen zijn: data base, container, document, bijlage, gebruiker, machtiging, bestands-, trigger-, UserDefinedFunction-of aanbiedings methode. |
| **Status code** | **statusCode_s** | De antwoord status van de bewerking. |
| **requestResourceId** | **ResourceId** | De resourceId die betrekking heeft op de aanvraag. De waarde kan verwijzen naar databaseRid, collectionRid of documentRid, afhankelijk van de bewerking die is uitgevoerd.|
| **clientIpAddress** | **clientIpAddress_s** | Het IP-adres van de client. |
| **requestCharge** | **requestCharge_s** | Het aantal RUs dat door de bewerking wordt gebruikt |
| **collectionRid** | **collectionId_s** | De unieke ID voor de verzameling.|
| **hebben** | **duration_s** | De duur van de bewerking, in milliseconden. |
| **requestLength** | **requestLength_s** | De lengte van de aanvraag, in bytes. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Deze waarde is niet-leeg wanneer [bron tokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) voor authenticatie worden gebruikt. De waarde verwijst naar de resource-ID van de gebruiker. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|

Zie [Azure monitor-logboeken categorieën en schema's](../azure-monitor/platform/diagnostic-logs-schema.md)voor een lijst met alle Azure monitor logboek categorieën en koppelingen naar gekoppelde schema's. 

## <a name="metrics"></a>Metrische gegevens
De volgende tabellen geven een lijst van de platform gegevens die zijn verzameld voor Azure CosmOS DB. Alle metrische gegevens worden opgeslagen in de naam ruimte **Cosmos DB standaard metrische gegevens**.

Zie [Azure monitor ondersteunde metrische gegevens](../azure-monitor/platform/metrics-supported.md)voor een lijst met alle Azure monitor metrische gegevens voor ondersteuning (inclusief CosmosDB). 

#### <a name="request-metrics"></a>Metrische gegevens aanvragen
            
|Metrisch (metrische weergave naam)|Eenheid (aggregatie type) |Beschrijving|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totaal aantal aanvragen) | Aantal (aantal) | Aantal aanvragen dat is gedaan| DATABASENAME, verzamel-, regio, status code| Alles | TotalRequests, http 2xx, HTTP 3xx, HTTP 400, HTTP 401, interne server fout, service niet beschikbaar, beperkt aantal aanvragen per seconde | Wordt gebruikt voor het bewaken van aanvragen per status code, container op een minuut granulatie. Als u gemiddeld aantal aanvragen per seconde wilt ophalen, gebruikt u de aggregatie van Count op minuut en deelt u deze door 60. |
| MetadataRequests (meta gegevens aanvragen) |Aantal (aantal) | Aantal aanvragen voor meta gegevens. Azure Cosmos DB behoudt de container van de meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort, en hun configuraties, kosteloos kunt inventariseren. | DATABASENAME, verzamel-, regio, status code| Alles| |Wordt gebruikt voor het bewaken van vertragingen als gevolg van aanvragen voor meta gegevens.|
| MongoRequests (Mongo-aanvragen) | Aantal (aantal) | Aantal gemaakte Mongo-aanvragen | DATABASENAME, verzamel-, regio, opdrachtnaam, error code| Alles |Mongo query-aanvraag frequentie, frequentie van Mongo-update aanvragen, Mongo aanvraag frequentie voor verwijderen, Mongo aanvraag frequentie invoegen, Mongo aantal aanvraag frequentie| Wordt gebruikt voor het bewaken van Mongo-aanvraag fouten, gebruik per opdracht type. |

#### <a name="request-unit-metrics"></a>Metrische gegevens van aanvraag eenheid

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Beschrijving|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo-aanvraag kosten) | Aantal (totaal) |Verbruikte Mongo-aanvraag eenheden| DATABASENAME, verzamel-, regio, opdrachtnaam, error code| Alles |Kosten voor query aanvragen voor Mongo, kosten voor Mongo update aanvragen, Mongo aanvraag kosten verwijderen, Mongo aanvraag kosten invoegen, Mongo aantal aanvraag kosten| Wordt gebruikt voor het bewaken van Mongo resource RUs in een minuut.|
| TotalRequestUnits (totaal aantal aanvraag eenheden)| Aantal (totaal) | Verbruikte aanvraag eenheden| DATABASENAME, verzamel-, regio, status code |Alles| TotalRequestUnits| Wordt gebruikt voor het bewaken van het totale aantal RU-gebruik met een minuut granulatie. Als u gemiddelde RU verbruikt per seconde, gebruikt u de totale aggregatie op minuut en deelt u dit door 60.|
| ProvisionedThroughput (ingerichte door Voer)| Aantal (maximum) |Ingerichte door Voer in container granulatie| DATABASENAME, containerName| 5 M| | Wordt gebruikt om de ingerichte door Voer per container te bewaken.|

#### <a name="storage-metrics"></a>Metrische opslag gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Beschrijving|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (beschik bare opslag) |Bytes (totaal) | Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M| Beschikbare opslag| Wordt gebruikt voor het bewaken van beschik bare opslag capaciteit (alleen van toepassing voor vaste opslag verzamelingen). de minimale granulatie moet 5 minuten zijn.| 
| DataUsage (gegevens gebruik) |Bytes (totaal) |Totaal gegevens gebruik gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M |Gegevens grootte | Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn.|
| IndexUsage (index gebruik) | Bytes (totaal) |Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M| Index grootte| Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn. |
| DocumentQuota (document quotum) | Bytes (totaal) | Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten per regio.| Databasenaam, verzamel-, regio| 5 M |Opslagcapaciteit| Voor het bewaken van het totale quotum in container en regio moet de minimale granulatie 5 minuten zijn.|
| DocumentCount (aantal documenten) | Aantal (totaal) |Totaal aantal documenten gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 M |Aantal documenten|Voor het bewaken van het aantal documenten in de container en de regio moet de minimale granulatie 5 minuten zijn.|

#### <a name="latency-metrics"></a>Metrische latentie gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Beschrijving|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| ReplicationLatency (replicatie latentie)| MilliSeconden (minimum, maximum, gemiddeld) | Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account| SourceRegion, TargetRegion| Alles | Wordt gebruikt voor het bewaken van de replicatie latentie van P99 tussen twee regio's voor een geo-gerepliceerd account. |
| Latentie aan server zijde| MilliSeconden (gemiddeld) | De tijd die de server nodig heeft om de aanvraag te verwerken. | Verzamelingnaam, ConnectionMode, DATABASENAME, OperationType, PublicAPIType, regio | Alles | Wordt gebruikt om de latentie van de aanvraag op de Azure Cosmos DB server te bewaken. |



#### <a name="availability-metrics"></a>Metrische beschikbaarheids gegevens

|Metrisch (metrische weergave naam) |Eenheid (aggregatie type)|Beschrijving| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service beschikbaarheid)| Percentage (minimum, maximum) | Beschik baarheid van account aanvragen op een granulatie van één uur| 1U | Service beschikbaarheid | Vertegenwoordigt het percentage van het totaal aantal geslaagde aanvragen. Een aanvraag wordt als mislukt beschouwd als gevolg van een systeem fout als de status code 410, 500 of 503 wordt gebruikt om de beschik baarheid van het account te controleren op de granulatie van het uur. |


#### <a name="cassandra-api-metrics"></a>Cassandra-API metrische gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Beschrijving|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-aanvragen) | Aantal (aantal) | Aantal gemaakte Cassandra-API aanvragen| DATABASENAME, Verzamelingsset, error code, regio, OperationType, resource type| Alles| Wordt gebruikt om Cassandra-aanvragen te bewaken met een granulatie van minuten. Als u gemiddeld aantal aanvragen per seconde wilt ophalen, gebruikt u de aggregatie van Count op minuut en deelt u deze door 60.|
| CassandraRequestCharges (Cassandra-aanvraag kosten) | Aantal (som, min, Max, Gem) | Verbruikte aanvraag eenheden door Cassandra-API aanvragen| DATABASENAME, verzamel-, regio, OperationType, resource type| Alles| Wordt gebruikt om te controleren RUs dat per minuut wordt gebruikt door een Cassandra-API-account.|
| CassandraConnectionClosures (Cassandra-verbinding gesloten) |Aantal (aantal) |Aantal gesloten Cassandra-verbindingen| ClosureReason, regio| Alles | Wordt gebruikt om de connectiviteit tussen clients en de Azure Cosmos DB Cassandra-API te bewaken.|

## <a name="see-also"></a>Zie ook

- Zie [bewaking Azure Cosmos DB](monitor-cosmos-db.md) voor een beschrijving van de bewakings Azure Cosmos db.
- Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
