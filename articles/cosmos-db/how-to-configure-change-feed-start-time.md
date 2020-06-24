---
title: De start tijd voor de wijzigings invoer processor configureren-Azure Cosmos DB
description: Meer informatie over het configureren van de processor voor wijzigings invoer om te beginnen met lezen vanaf een specifieke datum en tijd
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: d3d9af1b8cbda3f0fa2ff4650fef4602f7812f79
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261712"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>De start tijd voor de wijzigings invoer van de processor configureren

In dit artikel wordt beschreven hoe u de [processor voor wijzigings invoer](./change-feed-processor.md) kunt configureren om te beginnen met lezen vanaf een specifieke datum en tijd.

## <a name="default-behavior"></a>Standaardgedrag

Wanneer een wijzigings verwerkings processor de eerste keer wordt gestart, wordt de container leases geïnitialiseerd en wordt de [verwerkings levenscyclus](./change-feed-processor.md#processing-life-cycle)gestart. Wijzigingen die in de container zijn aangebracht voordat de wijzigings verwerkings processor voor de eerste keer werd geïnitialiseerd, worden niet gedetecteerd.

## <a name="reading-from-a-previous-date-and-time"></a>Lezen van een vorige datum en tijd

Het is mogelijk om de Change feed-processor te initialiseren om wijzigingen te lezen die beginnen op een **specifieke datum en tijd**, door een exemplaar van een `DateTime` te geven aan de `WithStartTime` uitbrei ding van de opbouw functie:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

De Change feed-processor wordt geïnitialiseerd voor die specifieke datum en tijd en begint met het lezen van de wijzigingen die zijn opgetreden.

## <a name="reading-from-the-beginning"></a>Lezen vanaf het begin

In andere scenario's, zoals gegevens migraties of het analyseren van de volledige geschiedenis van een container, moeten we de wijzigings feed lezen vanaf **het begin van de levens duur van die container**. Hiervoor kunnen we gebruiken `WithStartTime` op de uitbrei ding van de opbouw functie, maar door geven `DateTime.MinValue.ToUniversalTime()` , waardoor de UTC-weer gave van de minimum `DateTime` waarde zou worden gegenereerd, zoals:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

De Change feed-processor wordt geïnitialiseerd en begint met het lezen van wijzigingen aan het begin van de levens duur van de container.

> [!NOTE]
> Deze aanpassings opties werken alleen voor het instellen van het begin punt in de tijd van de Change feed-processor. Als de container leases voor de eerste keer wordt geïnitialiseerd, heeft het wijzigen van de containers geen effect.

> [!NOTE]
> Wanneer u een punt in de tijd opgeeft, worden alleen de wijzigingen van items die momenteel in de container bestaan, gelezen. Als een item is verwijderd, wordt de geschiedenis van de wijzigings feed ook verwijderd.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Voor beelden van gebruik op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voor beelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over het wijzigen van de feed-processor in de volgende artikelen:

* [Overzicht van de processor voor wijzigings invoer](change-feed-processor.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
