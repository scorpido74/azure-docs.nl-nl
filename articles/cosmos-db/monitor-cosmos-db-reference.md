---
title: Informatie over Azure Cosmos DB bewakings gegevens | Microsoft Docs
description: Naslag informatie over Logboeken en metrieken voor het bewaken van gegevens van Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 5f46c9bf53d791eaec68763edc32996847e78ebd
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186901"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Naslag informatie over Azure Cosmos DB bewakings gegevens
Dit artikel bevat een verwijzing naar logboek-en metrische gegevens die worden verzameld voor het analyseren van de prestaties en beschik baarheid van Azure Cosmos DB. Zie [bewaking Cosmos DB](monitor-cosmos-db.md) voor meer informatie over het verzamelen en analyseren van bewakings gegevens voor Azure Cosmos db.


## <a name="resource-logs"></a>Resourcelogboeken
De volgende tabel geeft een lijst van de eigenschappen voor Azure Cosmos DB bron Logboeken wanneer ze worden verzameld in Azure Monitor Logboeken of Azure Storage. In Azure Monitor logboeken worden deze verzameld in de tabel **AzureDiagnostics** met de waarde **resource provider** van *micro soft. DOCUMENTDB*.

| Azure Storage-veld of eigenschap | Eigenschap Azure Monitor logboeken | Beschrijving |
| --- | --- | --- |
| **tijd** | **TimeGenerated** | De datum en tijd (UTC) wanneer de bewerking opgetreden. |
| **ResourceId** | **Resource** | Het Azure Cosmos DB-account waarvoor de logboeken zijn ingeschakeld.|
| **Categorie** | **Categorie** | Voor Azure Cosmos DB-Logboeken is **DataPlaneRequests** is de enige beschikbare waarde. |
| **OperationName** | **OperationName** | Naam van de bewerking. Deze waarde kan een van de volgende bewerkingen zijn: maken, bijwerken, lezen, ReadFeed, verwijderen, vervangen, uitvoeren, SqlQuery, Query, JSQuery, Head, HeadFeed of Upsert.   |
| **Eigenschappen** | N.v.t. | De inhoud van dit veld worden beschreven in de rijen die volgen. |
| **ActivityId** | **activityId_g** | De unieke GUID voor de geregistreerde bewerking. |
| **UserAgent** | **userAgent_s** | Een tekenreeks waarmee de clientagent van de gebruiker die de aanvraag uitvoert. De indeling is {gebruiker agent name} / {version}.|
| **requestResourceType** | **requestResourceType_s** | Het type van de toegang tot bronnen. Deze waarde kan een van de volgende resourcetypen zijn: Database, Container, Document, bijlage, gebruikers, machtigingen, StoredProcedure, Trigger, UserDefinedFunction of aanbieding. |
| **statusCode** | **statusCode_s** | De reactiestatus van de bewerking. |
| **requestResourceId** | **ResourceId** | De resourceId die betrekking hebben op de aanvraag. De waarde kan verwijzen naar databaseRid, collectionRid of documentRid, afhankelijk van de bewerking die wordt uitgevoerd.|
| **clientIpAddress** | **clientIpAddress_s** | IP-adres van de client. |
| **requestCharge** | **requestCharge_s** | Het aantal ru's die worden gebruikt door de bewerking |
| **collectionRid** | **collectionId_s** | De unieke ID voor de verzameling.|
| **Duur** | **duration_s** | De duur van de bewerking, in milliseconden. |
| **requestLength** | **requestLength_s** | De lengte van de aanvraag, in bytes. |
| **responseLength** | **responseLength_s** | De lengte van het antwoord, in bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Deze waarde is niet leeg zijn wanneer [brontokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) worden gebruikt voor verificatie. De waarde verwijst naar de resource-ID van de gebruiker. |

## <a name="metrics"></a>Metrische gegevens
De volgende tabellen geven een lijst van de platform gegevens die zijn verzameld voor Azure CosmOS DB. Alle metrische gegevens worden opgeslagen in de naam ruimte **Cosmos DB standaard metrische gegevens**.

#### <a name="request-metrics"></a>Aanvraag voor metrische gegevens
            
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
| ProvisionedThroughput (ingerichte door Voer)| Aantal (maximum) |Ingerichte door Voer in container granulatie| DATABASENAME, containerName| 5 min.| | Wordt gebruikt om de ingerichte door Voer per container te bewaken.|

#### <a name="storage-metrics"></a>Metrische gegevens over opslag

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Beschrijving|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (beschik bare opslag) |Bytes (totaal) | Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 min.| Beschik bare opslag| Wordt gebruikt voor het bewaken van beschik bare opslag capaciteit (alleen van toepassing voor vaste opslag verzamelingen). de minimale granulatie moet 5 minuten zijn.| 
| DataUsage (gegevens gebruik) |Bytes (totaal) |Totaal gegevens gebruik gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 min. |Gegevens grootte | Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn.|
| IndexUsage (index gebruik) | Bytes (totaal) |Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 min.| Index grootte| Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn. |
| DocumentQuota (document quotum) | Bytes (totaal) | Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten per regio.| Databasenaam, verzamel-, regio| 5 min. |Opslagcapaciteit| Voor het bewaken van het totale quotum in container en regio moet de minimale granulatie 5 minuten zijn.|
| DocumentCount (aantal documenten) | Aantal (totaal) |Totaal aantal documenten gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 min. |Aantal documenten|Voor het bewaken van het aantal documenten in de container en de regio moet de minimale granulatie 5 minuten zijn.|

#### <a name="latency-metrics"></a>Metrische latentie gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Beschrijving|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| ReplicationLatency (replicatie latentie)| MilliSeconden (minimum, maximum, gemiddeld) | Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account| SourceRegion, TargetRegion| Alles | Wordt gebruikt voor het bewaken van de replicatie latentie van P99 tussen twee regio's voor een geo-gerepliceerd account. |


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