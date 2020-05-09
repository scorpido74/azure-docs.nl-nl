---
title: Processor voor wijzigingenfeed in Azure Cosmos DB
description: Meer informatie over het gebruik van de Azure Cosmos DB Change feed-processor om de wijzigings feed, de onderdelen van de wijzigings feed-processor te lezen
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2020
ms.reviewer: sngun
ms.openlocfilehash: aa9b090627b6f27a54b67c361b45b6f99e3a6338
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982374"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processor voor wijzigingenfeed in Azure Cosmos DB

De Change feed-processor maakt deel uit van de [Azure Cosmos DB SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3). Het vereenvoudigt het proces van het lezen van de wijzigings feed en het distribueren van de gebeurtenis verwerking over meerdere gebruikers effectief.

Het belangrijkste voor deel van de processor bibliotheek voor wijzigings invoer is het probleem tolerant gedrag dat ervoor zorgt dat alle gebeurtenissen in de feed ten minste één keer worden bezorgd.

## <a name="components-of-the-change-feed-processor"></a>Onderdelen van de processor voor wijzigings invoer

Er zijn vier belang rijke onderdelen van de implementatie van de feed voor wijzigings invoer:

1. **De bewaakte container:** De bewaakte container bevat de gegevens waaruit de wijzigings feed wordt gegenereerd. Eventuele toevoegingen en updates van de bewaakte container worden weer gegeven in de wijzigings feed van de container.

1. **De lease-container:** De lease container fungeert als een status opslag en coördineert de wijzigings feed voor meerdere werk rollen. De lease container kan worden opgeslagen in hetzelfde account als de bewaakte container of in een afzonderlijk account.

1. **De host:** Een host is een toepassings exemplaar dat de feed-processor van de wijziging gebruikt om te Luis teren naar wijzigingen. Meerdere instanties met dezelfde lease configuratie kunnen parallel worden uitgevoerd, maar elk exemplaar moet een andere **exemplaar naam**hebben.

1. **De gemachtigde:** De gemachtigde is de code die definieert wat u, de ontwikkelaar, wilt doen met elke batch met wijzigingen die de wijzigings status van de feed verwerkt. 

We kijken naar een voor beeld in het volgende diagram om meer inzicht te krijgen in de manier waarop deze vier elementen van de feed-processor samen werken. In de bewaakte container worden documenten opgeslagen en wordt ' City ' gebruikt als partitie sleutel. We zien dat de partitie sleutel waarden worden gedistribueerd in bereiken die items bevatten. Er zijn twee exemplaren van hosts en de wijzigings processor wordt toegewezen aan verschillende bereiken van partitie sleutel waarden voor elke instantie om de reken distributie te maximaliseren. Elk bereik wordt parallel gelezen en de voortgang wordt afzonderlijk van andere bereiken in de lease-container bewaard.

![Voor beeld van een feed-processor wijzigen](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>De processor voor de wijzigings feed implementeren

Het gegeven punt is altijd de bewaakte container, van een `Container` instantie die u `GetChangeFeedProcessorBuilder`aanroept:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Waarbij de eerste para meter een unieke naam is die het doel van deze processor beschrijft, en de tweede naam is de gemachtigde-implementatie die wijzigingen verwerkt. 

Een voor beeld van een gemachtigde is:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Ten slotte definieert u een naam voor dit processor exemplaar `WithInstanceName` met en dit is de container waarin de lease status moet `WithLeaseContainer`worden onderhouden.

Met `Build` aanroepen krijgt u het processor exemplaar dat u kunt starten door `StartAsync`aan te roepen.

## <a name="processing-life-cycle"></a>Levens cyclus verwerken

De normale levens cyclus van een exemplaar van een host is:

1. Lees de wijzigings feed.
1. Als er geen wijzigingen zijn, schakelt u de slaap stand in voor een vooraf gedefinieerde `WithPollInterval` hoeveelheid tijd (aanpasbaar met in de opbouw functie) en gaat u naar #1.
1. Als er wijzigingen zijn, stuurt u deze naar de **gemachtigde**.
1. Wanneer de gemachtigde de verwerking van de wijzigingen **heeft**voltooid, werkt u de lease Store bij met het meest recente verwerkte punt in de tijd en gaat u naar #1.

## <a name="error-handling"></a>Foutafhandeling

De Change feed-processor is robuust voor gebruikers code fouten. Dit betekent dat als de implementatie van de gemachtigde een niet-verwerkte uitzonde ring heeft (stap #4), de thread verwerking die door de desbetreffende batch wijzigingen wordt uitgevoerd, wordt gestopt en er een nieuwe thread wordt gemaakt. In de nieuwe thread wordt gecontroleerd welk punt in de tijd dat de lease-opslag het meest recent is voor dat bereik van partitie sleutel waarden en opnieuw wordt opgestart, waardoor dezelfde batch met wijzigingen in de gemachtigde effectief wordt verzonden. Dit gedrag wordt voortgezet totdat de gemachtigde de wijzigingen correct verwerkt en de reden is dat de wijzigings processor ten minste eenmaal is gegarandeerd, omdat als de code van de gemachtigde een uitzonde ring genereert, wordt de batch opnieuw geprobeerd.

Als u wilt voor komen dat de processor voor wijzigings invoer voortdurend opnieuw probeert dezelfde batch met wijzigingen aan te brengen, moet u logica toevoegen aan de code van de gemachtigde om documenten te schrijven, na uitzonde ring, naar een wachtrij met onbestelbare berichten. Met dit ontwerp zorgt u ervoor dat u niet-verwerkte wijzigingen kunt bijhouden, terwijl u toekomstige wijzigingen nog steeds kunt blijven verwerken. De wachtrij met onbestelbare berichten kan slechts een andere Cosmos-container zijn. Het exacte gegevens archief is niet van belang, alleen dat de niet-verwerkte wijzigingen behouden blijven.

Daarnaast kunt u de [wijzigings feed-Estimator](how-to-use-change-feed-estimator.md) gebruiken om de voortgang van de wijzigingen in de feeder-processor te controleren wanneer ze de wijzigings feed lezen. Naast het volgen van de controle als de wijzigings processor ' vastgelopen ' voortdurend opnieuw wordt uitgevoerd, kunt u ook zien of de verwerkings processor van de wijziging zich voordoet als gevolg van de beschik bare bronnen zoals CPU, geheugen en netwerk bandbreedte.

## <a name="dynamic-scaling"></a>Dynamische schaalbaarheid

Zoals vermeld tijdens de introductie, kan de wijzigings verwerkings processor de berekening automatisch verdelen over meerdere exemplaren. U kunt meerdere exemplaren van uw toepassing implementeren met behulp van de processor voor wijzigings invoer en hiervan profiteren, de enige vereisten voor de sleutel zijn:

1. Alle exemplaren moeten dezelfde configuratie voor de lease container hebben.
1. Alle exemplaren moeten dezelfde werk stroom naam hebben.
1. Elk exemplaar moet een andere exemplaar naam (`WithInstanceName`) hebben.

Als deze drie voor waarden van toepassing zijn, wordt met behulp van een even distributie algoritme alle leases in de lease-container gedistribueerd op alle actieve instanties en parallelliseren compute. Een lease kan alleen eigendom zijn van één exemplaar op een bepaald moment, waardoor het maximum aantal exemplaren gelijk is aan het aantal leases.

Het aantal exemplaren kan worden verg root of verkleind, en de wijzigings processor past de belasting dynamisch aan door dienovereenkomstig te distribueren.

Daarnaast kan de wijzigings processor van de feed dynamisch worden aangepast aan de schaal van containers als gevolg van de door Voer of opslag toeneemt. Wanneer uw container groeit, verwerkt de wijzigings verwerkings processor deze scenario's transparant door de leases dynamisch te verhogen en de nieuwe leases te verdelen over bestaande instanties.

## <a name="change-feed-and-provisioned-throughput"></a>Feed en ingerichte door Voer wijzigen

Er worden kosten in rekening gebracht voor het verbruikte RUs, omdat gegevens verplaatsing in en buiten Cosmos containers altijd RUs gebruikt. Er worden kosten in rekening gebracht voor RUs dat wordt gebruikt door de lease-container.

## <a name="additional-resources"></a>Extra resources

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Voor beelden van gebruik op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voor beelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over het wijzigen van de feed-processor in de volgende artikelen:

* [Overzicht van wijzigings feed](change-feed.md)
* [Pull-model voor feed wijzigen](change-feed-pull-model.md)
* [Migreren vanuit de bibliotheek van de wijzigings feed-processor](how-to-migrate-from-change-feed-library.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
* [Starttijd van verwerker van wijzigingenfeed](how-to-configure-change-feed-start-time.md)
