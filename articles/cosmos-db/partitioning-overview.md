---
title: Partitionering in Azure Cosmos DB
description: Meer informatie over partitioneren in Azure Cosmos DB, aanbevolen procedures bij het kiezen van een partitie sleutel en het beheren van logische partities
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: aa7d67cd6bd1bd422bd257b75ac5bde3bd534d7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85481830"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering in Azure Cosmos DB

Azure Cosmos DB maakt gebruik van partitionering om afzonderlijke containers in een Data Base te schalen om te voldoen aan de prestatie behoeften van uw toepassing. In partitioneren worden de items in een container onderverdeeld in afzonderlijke subsets met de naam *logische partities*. Logische partities worden gevormd op basis van de waarde van een *partitie sleutel* die is gekoppeld aan elk item in een container. Alle items in een logische partitie hebben dezelfde partitie sleutel waarde.

Een container bevat bijvoorbeeld items. Elk item heeft een unieke waarde voor de `UserID` eigenschap. Als `UserID` fungeert als de partitie sleutel voor de items in de container en er 1.000 unieke `UserID` waarden zijn, worden er voor de container 1.000 logische partities gemaakt.

Naast een partitie sleutel die de logische partitie van het item bepaalt, heeft elk item in een container een *item-id* (uniek binnen een logische partitie). Als de partitie sleutel en de *item-id* worden gecombineerd, wordt de *index*van het item gemaakt, waarmee het item op unieke wijze wordt geïdentificeerd.

Het [kiezen van een partitie sleutel](partitioning-overview.md#choose-partitionkey) is een belang rijke beslissing die invloed heeft op de prestaties van uw toepassing.

## <a name="managing-logical-partitions"></a>Logische partities beheren

Azure Cosmos DB transparant en beheert automatisch de plaatsing van logische partities op fysieke partities om efficiënt te voldoen aan de schaal baarheid en prestatie behoeften van de container. Naarmate de door Voer en opslag vereisten van een toepassing toenemen, Azure Cosmos DB logische partities verplaatsen om de belasting automatisch over een groter aantal fysieke partities te spreiden. U kunt meer te weten komen over [fysieke partities](partition-data.md#physical-partitions).

Azure Cosmos DB gebruikt op hash gebaseerde partitionering om logische partities over fysieke partities te verdelen. Azure Cosmos DB hashes de waarde van de partitie sleutel van een item. Het gehashte resultaat bepaalt de fysieke partitie. Azure Cosmos DB wijst vervolgens de sleutel ruimte van partitie sleutel-hashes gelijkmatig over de fysieke partities toe.

Trans acties (in opgeslagen procedures of triggers) zijn alleen toegestaan voor items in één logische partitie.

Meer informatie over [het beheren van partities](partition-data.md)vindt u in azure Cosmos db. (Het is niet nodig om de interne gegevens te begrijpen om uw toepassingen te bouwen of uit te voeren, maar hier toe te voegen voor een nieuws lezer.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Een partitiesleutel kiezen

Een partitie sleutel bestaat uit twee onderdelen: het pad van de **partitie sleutel** en de waarde van de **partitie sleutel**. U kunt bijvoorbeeld een item {"userId": "Andrew", "worksFor": "micro soft"} als u "userId" als de partitie sleutel kiest, de volgende twee partitie sleutel onderdelen zijn:

* Het pad van de partitie sleutel (bijvoorbeeld: "/userId"). Het pad naar de partitie sleutel accepteert alfanumerieke tekens en een onderstrepings teken (_). U kunt geneste objecten ook gebruiken met behulp van de standaard notatie voor paden (/).

* De partitie sleutel waarde (bijvoorbeeld: ' Andrew '). De waarde van de partitie sleutel kan van teken reeks-of numerieke typen zijn.

Zie het artikel [Azure Cosmos DB Service quota's](concepts-limits.md) voor meer informatie over de limieten voor door Voer, opslag en de lengte van de partitie sleutel.

Het selecteren van de partitie sleutel is een eenvoudige maar belang rijke ontwerp keuze in Azure Cosmos DB. Wanneer u de partitie sleutel selecteert, is het niet mogelijk om deze in-place te wijzigen. Als u de partitie sleutel moet wijzigen, moet u uw gegevens naar een nieuwe container verplaatsen met de nieuwe gewenste partitie sleutel.

Voor **alle** containers moet uw partitie sleutel:

* Een eigenschap zijn die een waarde heeft die niet verandert. Als een eigenschap uw partitie sleutel is, kunt u de waarde van die eigenschap niet bijwerken.
* Een hoge kardinaliteit hebben. Met andere woorden, de eigenschap moet een breed scala aan mogelijke waarden hebben.
* Sprei ding-verbruik en gegevens opslag gelijkmatig over alle logische partities. Dit garandeert zelfs RU-verbruik en opslag distributie over uw fysieke partities.

Als u [Meervoudige zuren-trans acties](database-transactions-optimistic-concurrency.md#multi-item-transactions) in azure Cosmos DB nodig hebt, moet u [opgeslagen procedures of triggers](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)gebruiken. Alle opgeslagen java script-procedures en triggers bevinden zich in een enkele logische partitie.

## <a name="partition-keys-for-read-heavy-containers"></a>Partitie sleutels voor Read-zware containers

Voor de meeste containers gelden de bovenstaande criteria alleen wanneer u een partitie sleutel moet kiezen. Voor grote alleen-lezen containers is het echter mogelijk dat u een partitie sleutel wilt kiezen die regel matig wordt weer gegeven als een filter in uw query's. Query's kunnen [efficiënt worden gerouteerd naar de relevante fysieke partities](how-to-query-container.md#in-partition-query) door de partitie sleutel op te nemen in het filter predicaat.

Als het meren deel van de aanvragen van uw werk belasting query's is en de meeste query's een gelijkheids filter hebben op dezelfde eigenschap, kan deze eigenschap een goede partitie sleutel zijn. Als u bijvoorbeeld regel matig een query uitvoert waarmee op wordt gefilterd `UserID` en vervolgens de partitie sleutel selecteert, wordt `UserID` het aantal [query's voor meerdere partities](how-to-query-container.md#avoiding-cross-partition-queries)verminderd.

Als uw container echter klein is, hebt u waarschijnlijk niet genoeg fysieke partities om u zorgen te maken over de invloed van de prestaties van kruis partitie query's. Voor de meeste kleine containers in Azure Cosmos DB zijn slechts één of twee fysieke partities vereist.

Als uw container kan worden uitgebreid naar meer dan een paar fysieke partities, moet u ervoor zorgen dat u een partitie sleutel kiest die kruis partitie query's minimaliseert. De container heeft meer dan een paar fysieke partities nodig wanneer aan een van de volgende voor waarden wordt voldaan:

* Uw container heeft meer dan 30.000 RU ingericht
* In uw container worden gegevens van 100 GB opgeslagen

## <a name="using-item-id-as-the-partition-key"></a>Item-ID als de partitie sleutel gebruiken

Als uw container een eigenschap heeft die een breed scala aan mogelijke waarden heeft, is het waarschijnlijk een goede keuze voor de partitie sleutel. Een mogelijk voor beeld van een dergelijke eigenschap is de *item-id*. Voor kleine alleen-lezen containers of schrijf bare containers van elke grootte is de *item-id* natuurlijk een fantastische keuze voor de partitie sleutel.

De *item-id* van de systeem eigenschap is gegarandeerd in elk item in uw Cosmos-container. Mogelijk hebt u andere eigenschappen die een logische ID van uw item vertegenwoordigen. In veel gevallen zijn dit ook fantastische opties voor partitie sleutels om dezelfde redenen als de *item-id*.

De *item-id* is een fantastische partitie sleutel keuze om de volgende redenen:

* Er zijn een breed scala aan mogelijke waarden (één unieke *item-id* per item).
* Omdat er een unieke *item-id* per item is, heeft de *item-id* een uitstekende taak op gelijkmatige verdeling van het gebruik van ru en gegevens opslag.
* U kunt eenvoudig efficiënte punt Lees bewerkingen uitvoeren omdat u altijd de partitie sleutel van een item weet als u de bijbehorende *item-id*kent.

Hieronder vindt u enkele aandachtspunten bij het selecteren van de *item-id* als de partitie sleutel:

* Als de *item-id* de partitie sleutel is, wordt deze in de gehele container een unieke id. U kunt geen items met een dubbele *item-id*hebben.
* Als u een lees-zware container hebt die een groot aantal [fysieke partities](partition-data.md#physical-partitions)heeft, zijn query's efficiënter als ze een gelijkheids filter met de *item-id*hebben.
* Opgeslagen procedures of triggers kunnen niet over meerdere logische partities worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [partitioneren en horizon taal schalen in azure Cosmos DB](partition-data.md).
* Meer informatie over [ingerichte door Voer in azure Cosmos DB](request-units.md).
* Meer informatie over [globale distributie in azure Cosmos DB](distribute-data-globally.md).
