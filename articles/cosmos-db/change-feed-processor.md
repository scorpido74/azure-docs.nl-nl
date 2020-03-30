---
title: Feedprocessorbibliotheek wijzigen in Azure Cosmos DB
description: Meer informatie over het gebruik van de azure cosmos DB-feedprocessorbibliotheek voor het lezen van de wijzigingsfeed, de onderdelen van de wijzigingsfeedprocessor
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: e71b2807595aebeb1f0c8682fde119f4e267e55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273307"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processor voor wijzigingenfeed in Azure Cosmos DB 

De wijzigingsfeedprocessor maakt deel uit van de [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3). Het vereenvoudigt het proces van het lezen van de wijzigingfeed en verdeelt de gebeurtenisverwerking effectief over meerdere consumenten.

Het belangrijkste voordeel van change feed processor bibliotheek is de fout-tolerant gedrag dat een "ten minste eenmaal" levering van alle gebeurtenissen in de verandering feed verzekert.

## <a name="components-of-the-change-feed-processor"></a>Onderdelen van de wijzigingsfeedprocessor

Er zijn vier belangrijke onderdelen van de implementatie van de change feed processor: 

1. **De bewaakte container:** De bewaakte container heeft de gegevens waaruit de wijzigingsfeed wordt gegenereerd. Eventuele wisselplaten en updates van de bewaakte container worden weergegeven in de wijzigingsfeed van de container.

1. **De leasecontainer:** De leasecontainer fungeert als statusopslag en coördineert de verwerking van de wijzigingsfeed voor meerdere werknemers. De leasecontainer kan worden opgeslagen op dezelfde rekening als de bewaakte container of in een aparte rekening. 

1. **De gastheer:** Een host is een toepassingsinstantie die de wijzigingsfeedprocessor gebruikt om naar wijzigingen te luisteren. Meerdere exemplaren met dezelfde leaseconfiguratie kunnen parallel worden uitgevoerd, maar elke instantie moet een andere **instantienaam**hebben. 

1. **De gemachtigde:** De gemachtigde is de code die definieert wat u, de ontwikkelaar, wilt doen met elke batch van wijzigingen die de wijzigingfeedprocessor leest. 

Om verder te begrijpen hoe deze vier elementen van change feed processor samenwerken, laten we eens kijken naar een voorbeeld in het volgende diagram. De bewaakte container slaat documenten op en gebruikt 'City' als partitiesleutel. We zien dat de partitiesleutelwaarden worden verdeeld in bereiken die items bevatten. Er zijn twee host-exemplaren en de wijzigingsfeedprocessor geeft verschillende bereiken van partitiesleutelwaarden toe aan elke instantie om de compute-verdeling te maximaliseren. Elk bereik wordt parallel gelezen en de voortgang ervan wordt gescheiden van andere bereiken in de leasecontainer behouden.

![Voorbeeld van feedprocessor wijzigen](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>De wijzigingsfeedprocessor implementeren

Het punt van binnenkomst is altijd de `Container` bewaakte `GetChangeFeedProcessorBuilder`container, vanuit een instantie die u noemt:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

Wanneer de eerste parameter een afzonderlijke naam is die het doel van deze processor beschrijft en de tweede naam de gemachtigdeimplementatie is die wijzigingen afhandelt. 

Een voorbeeld van een gemachtigde is:


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

Ten slotte definieert u een `WithInstanceName` naam voor deze processorinstantie met `WithLeaseContainer`en welke is de container om de leasestatus te behouden met .

Bellen `Build` geeft u het processorexemplaar dat `StartAsync`u starten door te bellen.

## <a name="processing-life-cycle"></a>Verwerking van de levenscyclus

De normale levenscyclus van een hostinstantie is:

1. Lees de wijzigingsfeed.
1. Als er geen wijzigingen zijn, slaap dan voor een `WithPollInterval` vooraf bepaalde tijd (aanpasbaar met in de Bouwer) en ga naar #1.
1. Als er wijzigingen zijn, stuurt u deze naar de **gemachtigde.**
1. Wanneer de gemachtigde de verwerking van de wijzigingen **is voltooid,** werkt u de leasewinkel bij met het laatst verwerkte punt in de tijd en gaat u naar #1.

## <a name="error-handling"></a>Foutafhandeling

De wijzigingsfeedprocessor is bestand tegen fouten in de gebruikerscode. Dat betekent dat als uw gemachtigde implementatie een niet-behandelde uitzondering heeft (stap #4), de threadverwerking die bepaalde batch van wijzigingen wordt gestopt en een nieuwe thread wordt gemaakt. De nieuwe thread controleert welke het laatste punt in de tijd was dat de leasewinkel heeft voor dat bereik van partitiesleutelwaarden, en start vanaf daar opnieuw, waardoor dezelfde batch wijzigingen naar de gemachtigde wordt verzonden. Dit gedrag wordt voortgezet totdat uw gemachtigde de wijzigingen correct verwerkt en dit de reden is dat de wijzigingsfeedprocessor een "ten minste één keer"-garantie heeft, want als de gemachtigdecode gooit, zal deze batch opnieuw proberen.

## <a name="dynamic-scaling"></a>Dynamische schaalbaarheid

Zoals vermeld tijdens de introductie, kan de change feed processor compute automatisch over meerdere exemplaren verdelen. U meerdere exemplaren van uw toepassing implementeren met behulp van de change feed processor en er gebruik van maken, de enige belangrijke vereisten zijn:

1. Alle exemplaren moeten dezelfde leasecontainerconfiguratie hebben.
1. Alle instanties moeten dezelfde werkstroomnaam hebben.
1. Elke instantie moet een andere`WithInstanceName`instantienaam hebben ( ).

Als deze drie voorwaarden van toepassing zijn, zal de change feed processor, met behulp van een gelijk distributiealgoritme, alle leases in de leasecontainer over alle lopende instanties verdelen en compute parallellopen. Eén lease kan slechts door één instantie op een bepaald moment worden bejaard, dus het maximum aantal exemplaren is gelijk aan het aantal leases.

Het aantal exemplaren kan groeien en krimpen en de wijzigingsfeedprocessor past de belasting dynamisch aan door deze opnieuw te verdelen.

Bovendien kan de change feed processor dynamisch aanpassen aan containers schaal als gevolg van doorvoer of opslag verhogingen. Wanneer uw container groeit, verwerkt de change feed processor deze scenario's transparant door de leases dynamisch te verhogen en de nieuwe leases te verdelen over bestaande exemplaren.

## <a name="change-feed-and-provisioned-throughput"></a>Feed- en ingerichte doorvoer wijzigen

Er worden kosten in rekening gebracht voor gebruikte R's, omdat gegevensverplaatsing in en uit Cosmos-containers altijd RU's verbruikt. U wordt in rekening gebracht voor DE's die worden verbruikt door de leasecontainer.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Gebruiksvoorbeelden op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Aanvullende voorbeelden op GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de feedprocessor wijzigen in de volgende artikelen:

* [Overzicht van wijzigingsfeed](change-feed.md)
* [Migreren vanuit de bibliotheek voor de wijzigingsfeedprocessor](how-to-migrate-from-change-feed-library.md)
* [De wijzigingenfeedschatting gebruiken](how-to-use-change-feed-estimator.md)
* [Starttijd van verwerker van wijzigingenfeed](how-to-configure-change-feed-start-time.md)
