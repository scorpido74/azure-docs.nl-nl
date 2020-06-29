---
title: Genormaliseerde RU/s voor een Azure Cosmos-container of-account bewaken
description: Meer informatie over het controleren van het gebruik van de genormaliseerde aanvraag eenheid van een bewerking in Azure Cosmos DB. Eigen aren van een Azure Cosmos DB-account kunnen begrijpen welke bewerkingen meer aanvraag eenheden gebruiken.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85482901"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Genormaliseerde RU/s voor een Azure Cosmos-container of-account bewaken

Azure Monitor voor Azure Cosmos DB biedt een weer gave van metrische gegevens voor het bewaken van uw account en het maken van Dash boards. De Azure Cosmos DB metrische gegevens worden standaard verzameld. voor deze functie hoeft u niets expliciet in te scha kelen of te configureren.

De **genormaliseerde** metrische gegevens over het gebruik van ru worden gebruikt om te zien hoe goed verzadigde replica's zijn met betrekking tot het verbruik van aanvraag eenheden in de partitie sleutel bereik. Azure Cosmos DB distribueert de door Voer gelijkmatig over alle fysieke partities. Deze metrische gegevens bieden een per seconde weer gave van het maximale doorvoer gebruik binnen een replicaset. Gebruik deze metrische gegevens voor het berekenen van het gebruik van de RU/s in partities voor de opgegeven container. Als u deze metrische gegevens ziet, moet u de door Voer verhogen om te voldoen aan de behoeften van uw werk belasting.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Wat u kunt verwachten en wanneer genormaliseerde RU/s hoger is

Wanneer het genormaliseerde RU/s-verbruik 100% bereikt, ontvangt de client frequentie limiet fouten. De client moet de wacht tijd respecteren en opnieuw proberen. Als er een korte piek is die 100% gebruikt, betekent dit dat de door Voer van de replica de maximale prestatie limiet heeft bereikt. Een enkele bewerking zoals een opgeslagen procedure die alle RU/s op een replica verbruikt, leidt bijvoorbeeld tot een korte piek in genormaliseerd RU/s-verbruik. In dergelijke gevallen zijn er geen directe tarieven voor het beperken van fouten als de aanvraag frequentie laag is. Dat komt omdat bij Azure Cosmos DB aanvragen voor meer dan de ingerichte RU/s voor de specifieke aanvraag en andere aanvragen binnen die periode een tarief beperkt zijn.

Met de Azure Monitor metrische gegevens kunt u de bewerkingen per status code vinden met behulp van het **totale aantal aanvragen** . Later kunt u filteren op deze aanvragen met de status code 429 en deze vervolgens splitsen op **bewerkings type**.

Voor het vinden van de aanvragen die beperkt zijn, is de aanbevolen manier om deze informatie op te halen via Diagnostische logboeken.

Als er sprake is van een doorlopende piek van 100% genormaliseerd RU/s-verbruik of dicht bij 100%, is het raadzaam de door voer te verg Roten. U kunt nagaan welke bewerkingen zwaar en het piek gebruik zijn door gebruik te maken van de Azure monitor-metrische gegevens en logboeken van Azure monitor.

De **genormaliseerde** metrische gegevens over het gebruik van ru worden ook gebruikt om te zien welk partitie sleutel bereik beter is in de gebruiks voorwaarden. Zo krijgt u de scheefheid van de door voer naar een partitie sleutel bereik. U kunt later aan de slag om het **PartitionKeyRUConsumption** -logboek in azure monitor logboeken te bekijken voor informatie over welke logische partitie sleutels worden gebruikt in termen van gebruik.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>De metrische gegevens over het verbruik van de genormaliseerde aanvraag eenheden weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Deel venster metrische gegevens in Azure Monitor":::

3. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement**en de **resource groep**. Voor het **bron type**selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Kies een Azure Cosmos-account om de metrische gegevens weer te geven":::

4. Vervolgens kunt u een metriek selecteren in de lijst met beschik bare metrische gegevens. U kunt metrische gegevens selecteren die specifiek zijn voor aanvraag eenheden, opslag, latentie, Beschik baarheid, Cassandra en andere. Zie het artikel [metrische gegevens per categorie](monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we **genormaliseerde** metrische gegevens van ru-verbruik en **Max** als de aggregatie waarde.

   Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Kies een waarde in het Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filters voor genormaliseerd verbruik van aanvraag eenheden

U kunt ook de metrische gegevens en de grafiek die worden weer gegeven met een specifieke **verzamelingnaam**, **DATABASENAME**, **PartitionKeyRangeID**en **regio**filteren. Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de gewenste eigenschap, zoals naam **verzameling** en bijbehorende waarde die u interesseert. In de grafiek worden vervolgens de genormaliseerde RU-verbruiks eenheden weer gegeven die zijn gebruikt voor de container voor de geselecteerde periode.  

U kunt metrische gegevens groeperen met behulp van de optie **splitsing Toep assen** .  

De genormaliseerde metrische aanvraag eenheden voor elke container worden weer gegeven, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Filters toep assen op genormaliseerde metrische aanvraag eenheden verbruik":::

## <a name="next-steps"></a>Volgende stappen

* Bewaak Azure Cosmos DB gegevens met behulp van [Diagnostische instellingen](cosmosdb-monitor-resource-logs.md) in Azure.
* [Bewerkingen voor Azure Cosmos DB Control-vlak controleren](audit-control-plane-logs.md)
