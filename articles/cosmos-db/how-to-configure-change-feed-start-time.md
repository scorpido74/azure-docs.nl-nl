---
title: Begintijd van de wijzigingsfeedprocessor configureren - Azure Cosmos DB
description: Meer informatie over het configureren van de wijzigingsfeedprocessor om vanaf een bepaalde datum en tijd te beginnen met lezen
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586271"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Begintijd van de wijzigingsfeedprocessor configureren

In dit artikel wordt beschreven hoe u de [wijzigingsfeedprocessor](./change-feed-processor.md) configureren om vanaf een bepaalde datum en tijd te beginnen met lezen.

## <a name="default-behavior"></a>Standaardgedrag

Wanneer een wijzigingsfeedprocessor de eerste keer wordt gestart, wordt de leasecontainer geïnitialiseren en wordt de [verwerkingslevenscyclus gestart.](./change-feed-processor.md#processing-life-cycle) Wijzigingen die zich in de container hebben voorgedaan voordat de wijzigingsfeedprocessor voor de eerste keer is geïnitialiseerd, worden niet gedetecteerd.

## <a name="reading-from-a-previous-date-and-time"></a>Lezen vanaf een eerdere datum en tijd

Het is mogelijk om de wijzigingsfeedprocessor te initialiseren om wijzigingen te lezen vanaf `DateTime` een `WithStartTime` bepaalde datum **en tijd,** door een instantie van een aan de builder-extensie door te geven:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

De wijzigingsfeedprocessor wordt geïnitialiseerd voor die specifieke datum en tijd en begint met het lezen van de wijzigingen die daarna zijn doorgevoerd.

## <a name="reading-from-the-beginning"></a>Lezen vanaf het begin

In andere scenario's, zoals gegevensmigraties of het analyseren van de volledige geschiedenis van een container, moeten we de wijzigingsfeed lezen vanaf **het begin van de levensduur van die container.** Om dat te doen, kunnen we `WithStartTime` gebruiken `DateTime.MinValue.ToUniversalTime()`op de builder extensie, `DateTime` maar passeren , die zou genereren de UTC vertegenwoordiging van de minimale waarde, zoals zo:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

De wijzigingsfeedprocessor wordt geïnitialiseerd en begint met het lezen van wijzigingen vanaf het begin van de levensduur van de container.

> [!NOTE]
> Deze aanpassingsopties werken alleen om het beginpunt in de tijd van de wijzigingsfeedprocessor in te stellen. Zodra de leasecontainer voor de eerste keer is geïnitialiseerd, heeft het wijzigen ervan geen effect.

> [!NOTE]
> Wanneer u een tijdstip opgeeft, worden alleen wijzigingen voor items die momenteel in de container aanwezig zijn, gelezen. Als een item is verwijderd, wordt ook de geschiedenis in de feed Wijzigen verwijderd.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Gebruiksvoorbeelden op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voorbeelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de feedprocessor wijzigen in de volgende artikelen:

* [Overzicht van de feedprocessor van change](change-feed-processor.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
