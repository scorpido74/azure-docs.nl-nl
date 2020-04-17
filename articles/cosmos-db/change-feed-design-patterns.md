---
title: Voerontwerppatronen wijzigen in Azure Cosmos DB
description: Overzicht van algemene wijzigingsvoerontwerppatronen
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 012d27b44ecfbdd460adf241742df397880f78c6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450348"
---
# <a name="change-feed-design-patterns-in-azure-cosmos-db"></a>Voerontwerppatronen wijzigen in Azure Cosmos DB

De Azure Cosmos DB-wijzigingsfeed maakt een efficiënte verwerking van grote gegevenssets met een hoog volume aan schrijfbewerkingen mogelijk. Change feed biedt ook een alternatief voor het opvragen van een volledige gegevensset om te bepalen wat er is veranderd. Dit document richt zich op veelvoorkomende wijzigingsvoerontwerppatronen, ontwerpafwegingen en wijzigingsfeedbeperkingen.

Azure Cosmos DB is zeer geschikt voor IoT-, gaming-, retail- en operationele registratietoepassingen. Een gemeenschappelijk ontwerppatroon in deze toepassingen is het gebruik van wijzigingen in de gegevens om extra acties te activeren. Voorbeelden van aanvullende acties zijn:

* Een melding of een aanroep naar een API activeren wanneer een item wordt ingevoegd of bijgewerkt.
* Real-time stream processing voor IoT of real-time analytics processing op operationele data.
* Gegevensverplaatsing, zoals synchroniseren met een cache, een zoekmachine, een gegevensmagazijn of koude opslag.

Met de wijzigingsfeed in Azure Cosmos DB u voor elk van deze patronen efficiënte en schaalbare oplossingen bouwen, zoals in de volgende afbeelding wordt weergegeven:

![Azure Cosmos DB-wijzigingsfeed gebruiken om realtime analyse- en gebeurtenisgestuurde computerscenario's van stroom te bieden](./media/change-feed/changefeedoverview.png)

## <a name="event-computing-and-notifications"></a>Event computing en meldingen

De Azure Cosmos DB-wijzigingsfeed kan scenario's vereenvoudigen die een melding of een aanroep naar een API moeten activeren op basis van een bepaalde gebeurtenis. U de [bibliotheek Feedproces wijzigen](change-feed-processor.md) gebruiken om uw container automatisch te peilen op wijzigingen en elke keer dat er een schrijf- of update is, een externe API aan te roepen.

U ook selectief een melding activeren of een oproep naar een API verzenden op basis van specifieke criteria. Als u bijvoorbeeld uit de wijzigingsfeed leest met [Azure-functies,](change-feed-functions.md)u logica in de functie plaatsen om alleen een melding te verzenden als aan een specifieke criteria is voldaan. Hoewel de Azure-functiecode tijdens elke schrijf- en update zou worden uitgevoerd, wordt de melding alleen verzonden als aan specifieke criteria was voldaan.

## <a name="real-time-stream-processing"></a>Real-time streamverwerking

De Azure Cosmos DB-wijzigingsfeed kan worden gebruikt voor realtime streamverwerking voor IoT- of realtime analyseverwerking op operationele gegevens.
U bijvoorbeeld gebeurtenisgegevens van apparaten, sensoren, infrastructuur en toepassingen ontvangen en opslaan en deze gebeurtenissen in realtime verwerken met [Spark.](../hdinsight/spark/apache-spark-overview.md) In de volgende afbeelding ziet u hoe u een lambda-architectuur implementeren met behulp van de Azure Cosmos DB via de change feed:

![Azure Cosmos DB-gebaseerde lambda-pijplijn voor inname en query](./media/change-feed/lambda.png)

In veel gevallen ontvangen implementaties voor streamverwerking eerst een groot aantal binnenkomende gegevens in een tijdelijke berichtenwachtrij, zoals Azure Event Hub of Apache Kafka. De wijzigingsfeed is een geweldig alternatief vanwege het vermogen van Azure Cosmos DB om een aanhoudende hoge mate van gegevensopname te ondersteunen met gegarandeerde lage lees- en schrijflatentie. De voordelen van de Azure Cosmos DB-wijzigingsfeed ten opzichte van een berichtenwachtrij zijn:

### <a name="data-persistence"></a>Gegevenspersistentie

Gegevens die naar Azure Cosmos DB zijn geschreven, worden weergegeven in de wijzigingsfeed en worden bewaard totdat ze zijn verwijderd. Berichtenwachtrijen hebben doorgaans een maximale bewaartermijn. [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/) biedt bijvoorbeeld een maximale gegevensbewaring van 90 dagen.

### <a name="querying-ability"></a>Querycapaciteit

Naast het lezen van de wijzigingsfeed van een Cosmos-container, u ook SQL-query's uitvoeren op de gegevens die zijn opgeslagen in Azure Cosmos DB. De wijziging feed is niet een verdubbeling van de gegevens al in de container, maar gewoon een ander mechanisme van het lezen van de gegevens. Als u gegevens uit de wijzigingsfeed leest, is deze dus altijd consistent met query's van dezelfde Azure Cosmos DB-container.

### <a name="high-availability"></a>Hoge beschikbaarheid

Azure Cosmos DB biedt tot 99,999% lees- en schrijfbeschikbaarheid. In tegenstelling tot veel berichtenwachtrijen kunnen Azure Cosmos DB-gegevens eenvoudig wereldwijd worden gedistribueerd en geconfigureerd met een [RTO -doelstelling (Hersteltijddoelstelling)](consistency-levels-tradeoffs.md#rto) van nul.

Nadat u items in de wijzigingsfeed hebt verwerkt, u een gematerialiseerde weergave en geaggregeerde waarden weer in Azure Cosmos DB behouden. Als u Azure Cosmos DB gebruikt om een game te bouwen, u bijvoorbeeld wijzigingsfeed gebruiken om realtime leaderboards te implementeren op basis van scores uit voltooide games.

## <a name="data-movement"></a>Gegevensverplaatsing

U ook lezen uit de change feed voor real-time data beweging.

Met de wijzigingsfeed u bijvoorbeeld de volgende taken efficiënt uitvoeren:

* Werk een cache-, zoekindex- of gegevensmagazijn bij met gegevens die zijn opgeslagen in Azure Cosmos DB.

* Voer nul downtime-migraties uit naar een ander Azure Cosmos-account of een andere Azure Cosmos-container met een andere logische partitiesleutel.

* Implementeer een gegevensgelaagdheid op toepassingsniveau en archivering. U bijvoorbeeld 'hot data' opslaan in Azure Cosmos DB en 'koude gegevens' verouderen naar andere opslagsystemen zoals [Azure Blob Storage.](../storage/common/storage-introduction.md)

Wanneer u gegevens moet [denormaliseren tussen partities en containers,](how-to-model-partition-example.md#v2-introducing-denormalization-to-optimize-read-queries
)u uit de wijzigingsfeed van uw container lezen als bron voor deze gegevensreplicatie. Real-time gegevensreplicatie met de wijzigingsfeed kan alleen een uiteindelijke consistentie garanderen. U [controleren hoe ver de Change Feed Processor achterblijft](how-to-use-change-feed-estimator.md) bij het verwerken van wijzigingen in uw Cosmos-container.

## <a name="event-sourcing"></a>Event sourcing

Het [patroon voor het zoeken naar gebeurtenissen](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) omvat het gebruik van een append-only winkel om de volledige reeks acties op die gegevens vast te leggen. De wijzigingsfeed van Azure Cosmos DB is een goede keuze als een centraal gegevensarchief in architectuur voor het vinden van gebeurtenissen waarbij alle gegevensopname wordt gemodelleerd als schrijft (geen updates of verwijderingen). In dit geval is elke schrijven naar Azure Cosmos DB een 'gebeurtenis' en hebt u een volledige registratie van gebeurtenissen uit het verleden in de wijzigingsfeed. Typische toepassingen van de gebeurtenissen die door de centrale gebeurtenisopslag worden gepubliceerd zijn voor het handhaven van gematerialiseerde meningen of voor integratie met externe systemen. Omdat er geen tijdslimiet is voor retentie in de wijzigingsfeed, u alle gebeurtenissen uit het verleden opnieuw afspelen door vanaf het begin van de wijzigingsfeed van uw Cosmos-container te lezen.

U ervoor hebben dat [consumenten met meerdere wijzigingen zich abonneren op de wijzigingsfeed van dezelfde container.](how-to-create-multiple-cosmos-db-triggers.md#optimizing-containers-for-multiple-triggers) Afgezien van de ingerichte doorvoer van de [leasecontainer,](change-feed-processor.md#components-of-the-change-feed-processor) zijn er geen kosten verbonden aan het gebruik van de wijzigingsfeed. De wijzigingfeed is beschikbaar in elke container, ongeacht of deze wordt gebruikt.

Azure Cosmos DB is een groot centraal permanente gegevensarchief in het gebeurtenissourcingpatroon vanwege de sterke punten in horizontale schaalbaarheid en hoge beschikbaarheid. Bovendien biedt de bibliotheek voor wijzigingsfeedprocessor een ["ten minste eenmaal"](change-feed-processor.md#error-handling) garantie, zodat u de verwerking van gebeurtenissen niet mist.

## <a name="current-limitations"></a>Huidige beperkingen

De wijzigingsfeed heeft belangrijke beperkingen die u moet begrijpen. Hoewel items in een Cosmos-container altijd in de wijzigingsfeed blijven, is de wijzigingsfeed geen volledig bewerkingslogboek. Er zijn belangrijke gebieden om te overwegen bij het ontwerpen van een toepassing die de wijzigingsfeed gebruikt.

### <a name="intermediate-updates"></a>Tussentijdse updates

Alleen de meest recente wijziging voor een bepaald item is opgenomen in de wijzigingsfeed. Wanneer wijzigingen worden verwerkt, leest u de laatst beschikbare artikelversie. Als er meerdere updates voor hetzelfde item in een korte periode van tijd, is het mogelijk om te missen verwerking tussentijdse updates. Als u updates wilt bijhouden en eerdere updates voor een item wilt afspelen, raden we u aan deze updates te modelleren als een reeks schrijfbewerkingen.

### <a name="deletes"></a>Verwijderd

De wijzigingsfeed legt geen deletes vast. Als u een item uit uw container verwijdert, wordt het ook uit de wijzigingsfeed verwijderd. De meest voorkomende methode om dit te verwerken is het toevoegen van een zachte markering op de items die worden verwijderd. U een eigenschap met de naam 'verwijderd' toevoegen en deze instellen op 'true' op het moment van verwijdering. Deze documentupdate wordt weergegeven in de wijzigingsfeed. U een TTL instellen op dit item, zodat het later automatisch kan worden verwijderd.

### <a name="guaranteed-order"></a>Gegarandeerde bestelling

Er is een gegarandeerde volgorde in de wijzigingsfeed binnen een partitiesleutelwaarde, maar niet over partitiesleutelwaarden. U moet een partitiesleutel selecteren die u een zinvolle ordergarantie geeft.

Overweeg bijvoorbeeld een retailtoepassing met behulp van het ontwerppatroon voor het zoeken naar evenementen. In deze toepassing zijn verschillende gebruikersacties elke 'gebeurtenissen' die zijn gemodelleerd naar Azure Cosmos DB. Stel je voor dat er enkele voorbeeldgebeurtenissen in de volgende reeks hebben plaatsgevonden:

1. Klant voegt punt A toe aan winkelwagentje
2. Klant voegt punt B toe aan winkelwagentje
3. Klant verwijdert punt A uit winkelwagentje
4. Klant checkt uit en de inhoud van winkelwagentjes wordt verzonden

Voor elke klant wordt een gematerialiseerde weergave van de huidige inhoud van winkelwagentjes behouden. Deze toepassing moet ervoor zorgen dat deze gebeurtenissen worden verwerkt in de volgorde waarin ze zich voordoen. Als bijvoorbeeld de kassawagen voor de verwijdering van artikel A zou worden verwerkt, is het waarschijnlijk dat de klant artikel A zou hebben laten verzenden, in tegenstelling tot het gewenste artikel B. Om te garanderen dat deze vier gebeurtenissen worden verwerkt in volgorde van hun voorkomen, moeten ze binnen dezelfde waarde van de partitiesleutel vallen. Als u **gebruikersnaam** (elke klant heeft een unieke gebruikersnaam) als partitiesleutel selecteert, u garanderen dat deze gebeurtenissen worden weergegeven in de wijzigingsfeed in dezelfde volgorde waarin ze zijn geschreven naar Azure Cosmos DB.

## <a name="examples"></a>Voorbeelden

Hier volgen enkele voorbeelden van echte wijzigingen feedcode die verder reiken dan het bereik van de voorbeelden in Microsoft-documenten:

- [Inleiding tot de wijzigingsfeed](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html)
- [IoT-gebruikscase gecentreerd rond de wijzigingsfeed](https://github.com/AzureCosmosDB/scenario-based-labs)
- [Retail use case gecentreerd rond de change feed](https://github.com/AzureCosmosDB/scenario-based-labs)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht wijzigingenfeed](change-feed.md)
* [Opties voor het lezen van wijzigingsfeed](read-change-feed.md)
* [Wijzigingsfeed gebruiken met Azure-functies](change-feed-functions.md)