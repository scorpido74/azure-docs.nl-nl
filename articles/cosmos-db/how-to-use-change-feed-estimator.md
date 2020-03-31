---
title: De wijzigingsfeedschatter gebruiken - Azure Cosmos DB
description: Meer informatie over het gebruik van de change feed estimator om de voortgang van uw change feed processor te analyseren
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585285"
---
# <a name="use-the-change-feed-estimator"></a>De wijzigingsfeedschatter gebruiken

In dit artikel wordt beschreven hoe u de voortgang van de exemplaren van uw [wijzigingsfeedprocessor](./change-feed-processor.md) controleren terwijl ze de wijzigingsfeed lezen.

## <a name="why-is-monitoring-progress-important"></a>Waarom is het monitoren van de vooruitgang belangrijk?

De wijzigingsfeedprocessor fungeert als een aanwijzer die verder gaat in uw [wijzigingsfeed](./change-feed.md) en de wijzigingen in een gemachtigde implementatie oplevert. 

Uw implementatie van de wijzigingsfeedprocessor kan wijzigingen in een bepaald tempo verwerken op basis van de beschikbare bronnen zoals CPU, geheugen, netwerk, enzovoort.

Als deze snelheid langzamer is dan de snelheid waarmee uw wijzigingen plaatsvinden in uw Azure Cosmos-container, loopt uw processor achter.

Als u dit scenario identificeert, wordt u beter weten of we onze implementatie van de wijzigingsfeedprocessor moeten schalen.

## <a name="implement-the-change-feed-estimator"></a>De change feed estimator implementeren

Net als de [change feed processor](./change-feed-processor.md), de change feed schatter werkt als een push model. De schatter meet het verschil tussen het laatst verwerkte artikel (gedefinieerd door de status van de leasecontainer) en de laatste wijziging in de container en duwt deze waarde naar een gemachtigde. Het interval waarmee de meting wordt uitgevoerd, kan ook worden aangepast met een standaardwaarde van 5 seconden.

Als u bijvoorbeeld de feedprocessor voor wijzigingen als volgt gedefinieerd:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

De juiste manier om een schatter te initialiseren `GetChangeFeedEstimatorBuilder` om die processor te meten zou gebruiken als volgt:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Waar zowel de processor als de `leaseContainer` schatter dezelfde en dezelfde naam delen.

De andere twee parameters zijn de gemachtigde, die een getal ontvangt dat aangeeft **hoeveel wijzigingen in behandeling zijn om** door de processor te worden gelezen en het tijdsinterval waarop u deze meting wilt laten plaatsvinden.

Een voorbeeld van een gemachtigde die de schatting ontvangt, is:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

U deze schatting naar uw bewakingsoplossing sturen en deze gebruiken om te begrijpen hoe uw vooruitgang zich in de loop van de tijd gedraagt.

> [!NOTE]
> De wijzigingsfeedchatter hoeft niet te worden geïmplementeerd als onderdeel van uw change feed-processor en maakt ook geen deel uit van hetzelfde project. Het kan onafhankelijk zijn en in een heel ander geval worden uitgevoerd. Het hoeft alleen maar dezelfde naam en lease configuratie te gebruiken.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Gebruiksvoorbeelden op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voorbeelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de feedprocessor wijzigen in de volgende artikelen:

* [Overzicht van de feedprocessor van change](change-feed-processor.md)
* [Starttijd van verwerker van wijzigingenfeed](how-to-configure-change-feed-start-time.md)
