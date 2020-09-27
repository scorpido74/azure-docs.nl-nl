---
title: Kies het juiste consistentie niveau voor uw Azure Cosmos DB-app
description: Het juiste consistentie niveau voor uw toepassing kiezen in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 0a79f6883a150cb95724a7be30dcbd2e8e03f01f
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396073"
---
# <a name="how-to-choose-the-right-consistency-level-for-your-azure-cosmos-db-application"></a>Het juiste consistentie niveau voor uw Azure Cosmos DB-toepassing kiezen? 

Gedistribueerde data bases die gebruikmaken van replicatie voor hoge Beschik baarheid, lage latentie of beide, maken de fundamentele verhouding tussen de Lees consistentie versus Beschik baarheid, latentie en door voer. De meeste commerciële beschik bare gedistribueerde data bases vragen ontwikkel aars om te kiezen tussen de twee uiterst consistente consistentie modellen: *sterke* consistentie en *uiteindelijke* consistentie. Met Azure Cosmos DB kunnen ontwikkel aars kiezen uit de vijf goed gedefinieerde consistentie modellen: *Strong*, *gebonden veroudering*, *sessie*, *consistent voor voegsel* en *uiteindelijk*. Elk van deze consistentie modellen is goed gedefinieerd, intuïtief en kan worden gebruikt voor specifieke praktijk scenario's. Elk van de vijf consistentie modellen biedt nauw keurige [Beschik baarheid en prestatie-afwegingen](consistency-levels-tradeoffs.md) en wordt ondersteund door de uitgebreide service overeenkomsten. U kunt een standaard consistentie configureren op het niveau van de account en [deze overschrijven op aanvraag niveau](how-to-manage-consistency.md#override-the-default-consistency-level). Met de volgende eenvoudige overwegingen kunt u de juiste keuze maken in veel algemene scenario's.

## <a name="sql-api-and-table-api"></a>SQL-API en Table-API

Houd rekening met de volgende punten als uw toepassing is gebouwd met behulp van SQL API of Table-API:

- Voor veel praktijk scenario's is sessie consistentie optimaal en de aanbevolen optie. Zie voor meer informatie, [How-to manage Session token voor uw toepassing](how-to-manage-consistency.md#utilize-session-tokens).

- Als voor uw toepassing sterke consistentie is vereist, is het raadzaam om het consistentie niveau van de gebonden veroudering te gebruiken.

- Als u strengere consistentie garanties nodig hebt dan door de consistentie van de sessie en latentie met één cijfer in milliseconden voor schrijf bewerkingen, wordt u aangeraden het consistentie niveau van de gebonden veroudering te gebruiken.  

- Als voor uw toepassing uiteindelijke consistentie is vereist, is het raadzaam consistent consistentie niveau te gebruiken.

- Als u minder strikte consistentie garanties nodig hebt dan die van de sessie consistentie, kunt u het beste consistent consistentie niveau gebruiken.

- Als u de hoogste Beschik baarheid en de laagste latentie nodig hebt, gebruikt u uiteindelijk consistentie niveau.

- Als u nog meer duurzaamheid van de gegevens nodig hebt zonder de prestaties te verbeteren, kunt u een aangepast consistentie niveau maken op de toepassingslaag. Zie voor meer informatie [over het implementeren van aangepaste synchronisatie in uw toepassingen](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra-, MongoDB-en Gremlin-Api's

- Zie [consistentie niveaus en Cosmos DB api's](consistency-levels-across-apis.md#cassandra-mapping)voor meer informatie over de toewijzing van het ' consistentie niveau voor lezen ' dat wordt aangeboden in Apache Cassandra-en Cosmos DB-consistentie niveaus.

- Zie [consistentie niveaus en Cosmos DB api's](consistency-levels-across-apis.md#mongo-mapping)voor meer informatie over de toewijzing van ' Lees bezorgdheid ' van MongoDb-en Azure Cosmos DB consistentie niveaus.

## <a name="consistency-guarantees-in-practice"></a>Consistentie garanties in de praktijk

In de praktijk kunt u vaak sterkere consistentie garanties krijgen. Consistentie garanties voor een lees bewerking komen overeen met de versheid en volg orde van de database status die u aanvraagt. Lees consistentie is gekoppeld aan de volg orde en doorgifte van de schrijf-en bijwerk bewerkingen.  

* Wanneer het consistentie niveau is ingesteld op **gebonden veroudering**, zorgt Cosmos DB ervoor dat de clients altijd de waarde van een voor gaande schrijf bewerking lezen, met een vertraging die wordt begrensd door het verouderde venster.

* Wanneer het consistentie niveau is ingesteld op **Strong**, is het verouderde venster gelijk aan nul en zijn de clients gegarandeerd de laatste doorgevoerde waarde van de schrijf bewerking te lezen.

* Voor de resterende drie consistentie niveaus is het verouderde venster grotendeels afhankelijk van uw werk belasting. Als er bijvoorbeeld geen schrijf bewerkingen zijn op de data base, kan een lees bewerking met een mogelijke **consistentie-of**consistentie **niveau van het** **voor voegsel** dezelfde resultaten opleveren als een lees bewerking met een hoog consistentie niveau.

Als uw Azure Cosmos-account is geconfigureerd met een ander consistentie niveau dan de sterke consistentie, kunt u de kans ontdekken dat uw clients sterke en consistente Lees bewerkingen voor uw werk belastingen krijgen door te kijken naar de metrische gegevens van de *Probabilistically-gebonden veroudering* (PBS). Deze metriek wordt weer gegeven in de Azure Portal voor meer informatie, Zie [Probabilistically gebonden verouderde waarde (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic gebonden veroudering laten zien hoe uiteindelijk uw uiteindelijke consistentie is. Deze metrische gegevens bieden een inzicht in hoe vaak u een sterkere consistentie kunt krijgen dan het consistentie niveau dat u momenteel hebt geconfigureerd in uw Azure Cosmos-account. Met andere woorden, u kunt de waarschijnlijkheid (gemeten in milliseconden) voor het verkrijgen van sterk consistente Lees bewerkingen voor een combi natie van de regio's schrijven en lezen zien.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de consistentie niveaus vindt u in de volgende artikelen:

* [Toewijzing van consistentie niveau tussen Cosmos DB-Api's](consistency-levels-across-apis.md)
* [Beschik baarheid en prestaties voor diverse consistentie niveaus](consistency-levels-tradeoffs.md)
* [Het sessie token voor uw toepassing beheren](how-to-manage-consistency.md#utilize-session-tokens)
* [Metrische gegevens van aan waarschijnlijkheid gebonden veroudering (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
