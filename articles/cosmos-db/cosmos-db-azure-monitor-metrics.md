---
title: Azure Cosmos DB metrische gegevens ophalen van Azure Monitor
description: ''
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: 762c910336fa2b50a46eda23cf66d8a7aa383c52
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241226"
---
# <a name="monitor-and-debug-azure-cosmos-db-metrics-from-azure-monitor"></a>Meet gegevens van Azure Monitor controleren en fouten opsporen Azure Cosmos DB

U kunt Azure Cosmos DB metrieken van Azure Monitor API weer geven. Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, met inbegrip van de Azure Portal, het verkrijgen van toegang via de REST API of het opvragen van query's met behulp van Power shell of CLI. Azure Cosmos DB metrische gegevens zijn numerieke waarden met lage latentie die standaard worden verzameld bij een frequentie van één minuut, u kunt deze metrische gegevens ook samen voegen. Deze metrische gegevens kunnen real-time scenario's ondersteunen.  

In dit artikel worden verschillende Azure Cosmos DB metrische gegevens beschreven die u kunt bekijken vanuit Azure Monitor met behulp van Azure Portal. Zie [monitor en debug met metrische gegevens in azure Cosmos DB](use-metrics.md) artikel als u geïnteresseerd bent in algemene gebruiks situaties en hoe Azure Cosmos DB metrische gegevens opnieuw worden gebruikt voor het analyseren en debuggen van deze problemen. U gebruikt een van uw bestaande Azure Cosmos-accounts en bekijkt de verschillende metrische gegevens in de data base, container, regio, aanvraag of bewerkings niveau. Zorg er dus voor dat u een Azure Cosmos-account hebt met voorbeeld gegevens en ruwe bewerkingen op die gegevens uitvoert.

## <a name="view-metrics-from-azure-portal"></a>Metrische gegevens van Azure Portal weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   ![Deel venster metrische gegevens in Azure Monitor](./media/cosmos-db-azure-monitor-metrics/monitor-metrics-blade.png)

1. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement**en de **resource groep**. Voor het **bron type**selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**. 

   ![Kies een Cosmos DB account om de metrische gegevens weer te geven](./media/cosmos-db-azure-monitor-metrics/select-cosmosdb-account.png)

1. Vervolgens kunt u een metriek selecteren in de lijst met beschik bare metrische gegevens. U kunt metrische gegevens selecteren die specifiek zijn voor aanvraag eenheden, opslag, latentie, Beschik baarheid, Cassandra en andere. Zie de sectie [metrische gegevens per categorie](#metrics-by-category) in dit artikel voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we **aanvraag eenheden** en **Gem** als de aggregatie waarde. 

   Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter. U kunt het gemiddelde aantal verbruikte aanvraag eenheden per minuut voor de geselecteerde periode bekijken.  

   ![Kies een waarde in het Azure Portal](./media/cosmos-db-azure-monitor-metrics/metric-types.png)

## <a name="add-filters-to-metrics"></a>Filters toevoegen aan metrische gegevens

U kunt ook de metrische gegevens en de grafiek die worden weer gegeven met een specifieke **verzamelingnaam**, **DATABASENAME**, **OperationType**, **Region**en **status**code filteren. Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de vereiste eigenschap, zoals **OperationType** , en selecteert u een waarde zoals **query**. In de grafiek worden vervolgens de verbruikte aanvraag eenheden voor de query bewerking voor de geselecteerde periode weer gegeven. De bewerkingen die zijn uitgevoerd via een opgeslagen procedure, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metrische waarde voor OperationType.

![Een filter toevoegen om de metrische granulatie te selecteren](./media/cosmos-db-azure-monitor-metrics/add-metrics-filter.png)

U kunt metrische gegevens groeperen met behulp van de optie **splitsing Toep assen** . U kunt bijvoorbeeld de aanvraag eenheden per bewerkings type groeperen en de grafiek voor alle bewerkingen tegelijk bekijken, zoals wordt weer gegeven in de volgende afbeelding: 

![Splitsings filter Toep assen toevoegen](./media/cosmos-db-azure-monitor-metrics/apply-metrics-splitting.png)


## <a id="metrics-by-category"></a>Metrische gegevens per categorie

### <a name="request-metrics"></a>Aanvraag voor metrische gegevens
            
|Metrisch (metrische weergave naam)|Eenheid (aggregatie type) |Description|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| TotalRequests (totaal aantal aanvragen) | Aantal (aantal) | Aantal aanvragen dat is gedaan| DATABASENAME, verzamel-, regio, status code| Alle | TotalRequests, http 2xx, HTTP 3xx, HTTP 400, HTTP 401, interne server fout, service niet beschikbaar, beperkt aantal aanvragen per seconde | Wordt gebruikt voor het bewaken van aanvragen per status code, container op een minuut granulatie. Als u gemiddeld aantal aanvragen per seconde wilt ophalen, gebruikt u de aggregatie van Count op minuut en deelt u deze door 60. |
| MetadataRequests (meta gegevens aanvragen) |Aantal (aantal) | Aantal aanvragen voor meta gegevens. Azure Cosmos DB behoudt de container van de meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort, en hun configuraties, kosteloos kunt inventariseren. | DATABASENAME, verzamel-, regio, status code| Alle| |Wordt gebruikt voor het bewaken van vertragingen als gevolg van aanvragen voor meta gegevens.|
| MongoRequests (Mongo-aanvragen) | Aantal (aantal) | Aantal gemaakte Mongo-aanvragen | DATABASENAME, verzamel-, regio, opdrachtnaam, error code| Alle |Mongo query-aanvraag frequentie, frequentie van Mongo-update aanvragen, Mongo aanvraag frequentie voor verwijderen, Mongo aanvraag frequentie invoegen, Mongo aantal aanvraag frequentie| Wordt gebruikt voor het bewaken van Mongo-aanvraag fouten, gebruik per opdracht type. |

### <a name="request-unit-metrics"></a>Metrische gegevens van aanvraag eenheid

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo-aanvraag kosten) | Aantal (totaal) |Verbruikte Mongo-aanvraag eenheden| DATABASENAME, verzamel-, regio, opdrachtnaam, error code| Alle |Kosten voor query aanvragen voor Mongo, kosten voor Mongo update aanvragen, Mongo aanvraag kosten verwijderen, Mongo aanvraag kosten invoegen, Mongo aantal aanvraag kosten| Wordt gebruikt voor het bewaken van Mongo resource RUs in een minuut.|
| TotalRequestUnits (totaal aantal aanvraag eenheden)| Aantal (totaal) | Verbruikte aanvraag eenheden| DATABASENAME, verzamel-, regio, status code |Alle| TotalRequestUnits| Wordt gebruikt voor het bewaken van het totale aantal RU-gebruik met een minuut granulatie. Als u gemiddelde RU verbruikt per seconde, gebruikt u de totale aggregatie op minuut en deelt u dit door 60.|
| ProvisionedThroughput (ingerichte door Voer)| Aantal (maximum) |Ingerichte door Voer in container granulatie| DATABASENAME, containerName| 5 MIN.| | Wordt gebruikt om de ingerichte door Voer per container te bewaken.|

### <a name="storage-metrics"></a>Metrische gegevens over opslag

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---| ---|
| AvailableStorage (beschik bare opslag) |Bytes (totaal) | Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 MIN.| Beschik bare opslag| Wordt gebruikt voor het bewaken van beschik bare opslag capaciteit (alleen van toepassing voor vaste opslag verzamelingen). de minimale granulatie moet 5 minuten zijn.| 
| DataUsage (gegevens gebruik) |Bytes (totaal) |Totaal gegevens gebruik gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 MIN. |Gegevensgrootte | Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn.|
| IndexUsage (index gebruik) | Bytes (totaal) |Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 MIN.| Indexgrootte| Voor het bewaken van het totale gegevens gebruik in de container en de regio, moet de minimale granulatie 5 minuten zijn. |
| DocumentQuota (document quotum) | Bytes (totaal) | Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten per regio.| Databasenaam, verzamel-, regio| 5 MIN. |Opslagcapaciteit| Voor het bewaken van het totale quotum in container en regio moet de minimale granulatie 5 minuten zijn.|
| DocumentCount (aantal documenten) | Aantal (totaal) |Totaal aantal documenten gerapporteerd bij een granulatie van 5 minuten per regio| Databasenaam, verzamel-, regio| 5 MIN. |Aantal documenten|Voor het bewaken van het aantal documenten in de container en de regio moet de minimale granulatie 5 minuten zijn.|

### <a name="latency-metrics"></a>Metrische latentie gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| ReplicationLatency (replicatie latentie)| MilliSeconden (minimum, maximum, gemiddeld) | Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account| SourceRegion, TargetRegion| Alle | Wordt gebruikt voor het bewaken van de replicatie latentie van P99 tussen twee regio's voor een geo-gerepliceerd account. |


### <a name="availability-metrics"></a>Metrische beschikbaarheids gegevens

|Metrisch (metrische weergave naam) |Eenheid (aggregatie type)|Description| Tijd granulatie| Toewijzing van verouderde metriek | Gebruik |
|---|---|---|---| ---| ---|
| ServiceAvailability (Service beschikbaarheid)| Percentage (minimum, maximum) | Beschik baarheid van account aanvragen op een granulatie van één uur| 1U | Service beschikbaarheid | Vertegenwoordigt het percentage van het totaal aantal geslaagde aanvragen. Een aanvraag wordt als mislukt beschouwd als gevolg van een systeem fout als de status code 410, 500 of 503 wordt gebruikt om de beschik baarheid van het account te controleren op de granulatie van het uur. |


### <a name="cassandra-api-metrics"></a>Cassandra-API metrische gegevens

|Metrisch (metrische weergave naam)|Eenheid (aggregatie type)|Description|Dimensies| Tijd granulatie| Gebruik |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra-aanvragen) | Aantal (aantal) | Aantal gemaakte Cassandra-API aanvragen| DATABASENAME, Verzamelingsset, error code, regio, OperationType, resource type| Alle| Wordt gebruikt om Cassandra-aanvragen te bewaken met een granulatie van minuten. Als u gemiddeld aantal aanvragen per seconde wilt ophalen, gebruikt u de aggregatie van Count op minuut en deelt u deze door 60.|
| CassandraRequestCharges (Cassandra-aanvraag kosten) | Aantal (som, min, Max, Gem) | Verbruikte aanvraag eenheden door Cassandra-API aanvragen| DATABASENAME, verzamel-, regio, OperationType, resource type| Alle| Wordt gebruikt om te controleren RUs dat per minuut wordt gebruikt door een Cassandra-API-account.|
| CassandraConnectionClosures (Cassandra-verbinding gesloten) |Aantal (aantal) |Aantal gesloten Cassandra-verbindingen| ClosureReason, regio| Alle | Wordt gebruikt om de connectiviteit tussen clients en de Azure Cosmos DB Cassandra-API te bewaken.|

## <a name="next-steps"></a>Volgende stappen

* [Gegevens weer geven en bewaken in het deel venster metrische gegevens van Azure Cosmos DB-account](use-metrics.md)

* [Diagnostische logboek registratie in Azure Cosmos DB](logging.md)
