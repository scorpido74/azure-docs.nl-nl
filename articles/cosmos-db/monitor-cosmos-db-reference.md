---
title: Gegevensverwijzing azure cosmos DB-monitoring | Microsoft Documenten
description: Overzichts- en metrische gegevens voor het bewaken van gegevens van Azure Cosmos DB.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d243224192b5761af45d387690f5fb41b84481e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588719"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Naslaginformatie over Azure Cosmos DB-bewakingsgegevens
Dit artikel bevat naslaginformatie over logboek- en metrische gegevens die worden verzameld om de prestaties en beschikbaarheid van Azure Cosmos DB te analyseren. Zie [Monitoring Cosmos DB](monitor-cosmos-db.md) voor meer informatie over het verzamelen en analyseren van bewakingsgegevens voor Azure Cosmos DB.


## <a name="resource-logs"></a>Resourcelogboeken
In de volgende tabel worden de eigenschappen voor Azure Cosmos DB-bronlogboeken weergegeven wanneer ze worden verzameld in Azure Monitor-logboeken of Azure Storage. In Azure Monitor Logs worden ze verzameld in de **AzureDiagnostics-tabel** met een **ResourceProvider-waarde** van *MICROSOFT. DOCUMENTDB*. 

| Azure Storage-veld of -eigenschap | Azure Monitor Logs, eigenschap | Beschrijving |
| --- | --- | --- |
| **Tijd** | **TimeGenerated** | De datum en tijd (UTC) waarop de bewerking heeft plaatsgevonden. |
| **Resourceid** | **Resource** | Het Azure Cosmos DB-account waarvoor logboeken zijn ingeschakeld.|
| **Categorie** | **Categorie** | Voor Azure Cosmos DB-logboeken zijn **DataPlaneRequests**, **MongoRequests**, **QueryRuntimeStatistics**, **PartitionKeyStatistics**, **PartitionKeyRUConsumption**, **ControlPlaneRequests** de beschikbare logtypen. |
| **operationName** | **OperationName** | Naam van de bewerking. Deze waarde kan een van de volgende bewerkingen zijn: Maken, Bijwerken, Lezen, Lezen, Verwijderen, Vervangen, Uitvoeren, SqlQuery, Query, JSQuery, Head, HeadFeed of Upsert.   |
| **Eigenschappen** | N.v.t. | De inhoud van dit veld wordt beschreven in de rijen die volgen. |
| **activiteitId** | **activityId_g** | De unieke GUID voor de aangemelde bewerking. |
| **Useragent** | **userAgent_s** | Een tekenreeks die de clientgebruikersagent opgeeft die de aanvraag uitvoert. De indeling is {user agent name}/{version}.|
| **requestResourceType** | **requestResourceType_s** | Het type van de toegang tot de bron. Deze waarde kan een van de volgende brontypen zijn: Database, Container, Document, Bijlage, Gebruiker, Machtigingen, StoredProcedure, Trigger, UserDefinedFunction of Aanbieding. |
| **statusCode** | **statusCode_s** | De reactiestatus van de bewerking. |
| **requestResourceId** | **ResourceId** | De resourceId die betrekking heeft op het verzoek. De waarde kan wijzen op databaseRid, collectionRid of documentRid, afhankelijk van de uitgevoerde bewerking.|
| **clientIpAddress** | **clientIpAddress_s** | Het IP-adres van de klant. |
| **requestCharge requestCharge requestCharge requestCharge** | **requestCharge_s** | Het aantal R's dat door de bewerking wordt gebruikt |
| **collectionRid** | **collectionId_s** | De unieke ID voor de collectie.|
| **Duur** | **duration_s** | De duur van de bewerking, in milliseconden. |
| **aanvraagLengte** | **requestLength_s** | De lengte van het verzoek, in bytes. |
| **responseLengte** | **responseLength_s** | De lengte van het antwoord, in bytes.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Deze waarde is niet leeg wanneer [resourcetokens](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) worden gebruikt voor verificatie. De waarde verwijst naar de resource-id van de gebruiker. |

Zie [Categorieën en schema's](../azure-monitor/platform/diagnostic-logs-schema.md)van Azure Monitor logboeken voor een lijst met alle azure monitor-logboekcategorieën en koppelingen naar gekoppelde schema's. 

## <a name="metrics"></a>Metrische gegevens
In de volgende tabellen worden de platformstatistieken weergegeven die zijn verzameld voor Azure CosmOS DB. Alle statistieken worden opgeslagen in de standaardstatistieken van namespace **Cosmos DB.**

For a list of all Azure Monitor support metrics (including CosmosDB), see [Azure Monitor supported metrics](../azure-monitor/platform/metrics-supported.md). 

#### <a name="request-metrics"></a>Statistieken aanvragen
            
|Metrische aanduiding (metrische weergavenaam)|Eenheid (aggregatietype) |Beschrijving|Dimensies| Tijdgranulariteiten| Verouderde metrische toewijzing | Gebruik |
|---|---|---|---| ---| ---| ---|
| Totaal aantal aanvragen (totaal aantal aanvragen) | Aantal (aantal) | Aantal gedaane verzoeken| DatabaseNaam, CollectionName, Regio, StatusCode| Alle | Totaalaanvragen, Http 2xx, Http 3xx, Http 400, Http 401, Interne serverfout, Service niet beschikbaar, geweigerde aanvragen, gemiddelde aanvragen per seconde | Wordt gebruikt om aanvragen per statuscode te controleren, container met een minuut granulariteit. Als u gemiddelde aanvragen per seconde wilt ontvangen, gebruikt u De aggregatie tellen op minuut en delen door 60. |
| Metagegevensverzoeken (metagegevensaanvragen) |Aantal (aantal) | Aantal metagegevensaanvragen. Azure Cosmos DB onderhoudt voor elk account de container metagegevens van het systeem, waarmee u gratis verzamelingen, databases, enz., en hun configuraties opsommen. | DatabaseNaam, CollectionName, Regio, StatusCode| Alle| |Wordt gebruikt om gashendels te controleren vanwege metadataverzoeken.|
| MongoRequests (Mongo-verzoeken) | Aantal (aantal) | Aantal Mongo-verzoeken | DatabaseNaam, CollectionName, Regio, CommandName, ErrorCode| Alle |Mongo-aanvraagpercentage, Mongo-updateaanvraagpercentage, Mongo-verwijderingsaanvraagpercentage, mongo-aanvraagpercentage voor invoegen, mongo-aanvraagpercentage| Wordt gebruikt om Mongo-aanvraagfouten, gebruikstoepassingen per opdrachttype, te controleren. |

#### <a name="request-unit-metrics"></a>Eenheidsstatistieken aanvragen

|Metrische aanduiding (metrische weergavenaam)|Eenheid (aggregatietype)|Beschrijving|Dimensies| Tijdgranulariteiten| Verouderde metrische toewijzing | Gebruik |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo Aanvraag Kosten) | Aantal (totaal) |Mongo Aanvraag Eenheden verbruikt| DatabaseNaam, CollectionName, Regio, CommandName, ErrorCode| Alle |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Wordt gebruikt om Mongo resource RU's in een minuut te controleren.|
| TotalRequestUnits (Totaal aantal aanvraageenheden)| Aantal (totaal) | Eenheden aanvragen verbruikt| DatabaseNaam, CollectionName, Regio, StatusCode |Alle| TotalRequestUnits| Wordt gebruikt om het totale RU-gebruik met een minuut granulariteit te controleren. Als u de gemiddelde RU-verbruikt per seconde wilt krijgen, gebruikt u Totale aggregatie op minuut en deelt u door 60.|
| ProvisionedThroughput (ingerichte doorvoer)| Aantal (Maximum) |Ingerichte doorvoer bij containergranulariteit| DatabaseNaam, ContainerName| 5 M| | Wordt gebruikt om de ingerichte doorvoer per container te controleren.|

#### <a name="storage-metrics"></a>Opslagstatistieken

|Metrische aanduiding (metrische weergavenaam)|Eenheid (aggregatietype)|Beschrijving|Dimensies| Tijdgranulariteiten| Verouderde metrische toewijzing | Gebruik |
|---|---|---|---| ---| ---| ---|
| Beschikbare opslagruimte (beschikbare opslagruimte) |Bytes (totaal) | Totaal beschikbare opslag gerapporteerd met 5 minuten granulariteit per regio| DatabaseNaam, CollectionName, Regio| 5 M| Beschikbare opslagruimte| Wordt gebruikt om de beschikbare opslagcapaciteit te controleren (alleen van toepassing op verzamelingen met vaste opslag) Minimale granulariteit moet 5 minuten bedragen.| 
| DataUsage (gegevensgebruik) |Bytes (totaal) |Totaal gegevensgebruik gerapporteerd met granulariteit van 5 minuten per regio| DatabaseNaam, CollectionName, Regio| 5 M |Gegevensgrootte | Wordt gebruikt om het totale gegevensgebruik in de container en regio te controleren, moet de minimale granulariteit 5 minuten bedragen.|
| Indexgebruik (indexgebruik) | Bytes (totaal) |Totaal indexgebruik gerapporteerd op granulariteit van 5 minuten per regio| DatabaseNaam, CollectionName, Regio| 5 M| Indexgrootte| Wordt gebruikt om het totale gegevensgebruik in de container en regio te controleren, moet de minimale granulariteit 5 minuten bedragen. |
| Documentquota (documentquotum) | Bytes (totaal) | Totaal opslagquotum gerapporteerd op 5 minuten granulariteit per regio.| DatabaseNaam, CollectionName, Regio| 5 M |Opslagcapaciteit| Wordt gebruikt om het totale quotum in de container en regio te controleren, moet de minimale granulariteit 5 minuten bedragen.|
| Documenttelling (aantal documenten) | Aantal (totaal) |Totaal aantal documenten gerapporteerd op granulariteit van 5 minuten per regio| DatabaseNaam, CollectionName, Regio| 5 M |Documentaantal|Wordt gebruikt om het aantal documenten bij de container en regio te controleren, moet de minimale granulariteit 5 minuten bedragen.|

#### <a name="latency-metrics"></a>Latentiestatistieken

|Metrische aanduiding (metrische weergavenaam)|Eenheid (aggregatietype)|Beschrijving|Dimensies| Tijdgranulariteiten| Gebruik |
|---|---|---|---| ---| ---|
| Replicatielatentie (replicatielatentie)| MilliSeconds (minimaal, maximum, gemiddelde) | P99-replicatielatentie in bron- en doelregio's voor een geo-enabled account| SourceRegion, TargetRegion| Alle | Wordt gebruikt om de latentie van P99-replicatie tussen twee regio's te controleren voor een geo-gerepliceerd account. |
| Latentie aan serverzijde| MilliSeconds (gemiddeld) | De tijd die de server nodig heeft om de aanvraag te verwerken. | CollectionName, ConnectionMode, DatabaseName, OperationType, PublicAPIType, Regio | Alle | Wordt gebruikt om de aanvraaglatentie op de Azure Cosmos DB-server te controleren. |



#### <a name="availability-metrics"></a>Beschikbaarheidsstatistieken

|Metrische aanduiding (metrische weergavenaam) |Eenheid (aggregatietype)|Beschrijving| Tijdgranulariteiten| Verouderde metrische toewijzing | Gebruik |
|---|---|---|---| ---| ---|
| Beschikbaarheid van service (beschikbaarheid van service)| Percentage (minimaal, maximaal) | Beschikbaarheid van accountaanvragen op een uur granulariteit| 1H | Beschikbaarheid van service | Vertegenwoordigt het percentage van de totale doorgegeven aanvragen. Een aanvraag wordt als mislukt beschouwd als gevolg van systeemfouten als de statuscode 410, 500 of 503 is die wordt gebruikt om de beschikbaarheid van het account op uur granulariteit te controleren. |


#### <a name="cassandra-api-metrics"></a>Cassandra API-statistieken

|Metrische aanduiding (metrische weergavenaam)|Eenheid (aggregatietype)|Beschrijving|Dimensies| Tijdgranulariteiten| Gebruik |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra Requests) | Aantal (aantal) | Aantal ingediend Cassandra API-aanvragen| Databasenaam, CollectionName, ErrorCode, Region, OperationType, ResourceType| Alle| Gebruikt om Cassandra verzoeken te controleren op een minuut granulariteit. Als u gemiddelde aanvragen per seconde wilt ontvangen, gebruikt u De aggregatie tellen op minuut en delen door 60.|
| CassandraRequestCharges (Cassandra Request Charges) | Tel (Som, Min, Max, Avg) | Aanvragen Eenheden verbruikt door Cassandra API-aanvragen| DatabaseNaam, CollectionName, Regio, OperationType, Resourcetype| Alle| Wordt gebruikt om de per minuut gebruikte R's te controleren door een Cassandra API-account.|
| CassandraConnectionClosures (Cassandra Connection Sluitingen) |Aantal (aantal) |Aantal Cassandra Connections gesloten| ClosureReason, Regio| Alle | Wordt gebruikt om de connectiviteit tussen clients en de Azure Cosmos DB Cassandra API te controleren.|

## <a name="see-also"></a>Zie ook

- Zie [Azure Cosmos DB bewaken](monitor-cosmos-db.md) voor een beschrijving van het bewaken van Azure Cosmos DB.
- Zie [Azure-resources bewaken met Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
