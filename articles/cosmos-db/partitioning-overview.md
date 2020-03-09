---
title: Partitioneren in Azure Cosmos DB
description: Meer informatie over partitioneren in Azure Cosmos DB, aanbevolen procedures bij het kiezen van een partitie sleutel en het beheren van logische partities
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365549"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitioneren in Azure Cosmos DB

Azure Cosmos DB maakt gebruik van partitionering om afzonderlijke containers in een Data Base te schalen om te voldoen aan de prestatie behoeften van uw toepassing. In partitioneren worden de items in een container onderverdeeld in afzonderlijke subsets met de naam *logische partities*. Logische partities worden gevormd op basis van de waarde van een *partitie sleutel* die is gekoppeld aan elk item in een container. Alle items in een logische partitie hebben dezelfde partitie sleutel waarde.

Een container bevat bijvoorbeeld items. Elk item heeft een unieke waarde voor de eigenschap `UserID`. Als `UserID` fungeert als de partitie sleutel voor de items in de container en er 1.000 unieke `UserID` waarden zijn, worden er voor de container 1.000 logische partities gemaakt.

Naast een partitie sleutel die de logische partitie van het item bepaalt, heeft elk item in een container een *item-id* (uniek binnen een logische partitie). Als de partitie sleutel en de item-ID worden gecombineerd, wordt de *index*van het item gemaakt, waarmee het item op unieke wijze wordt geïdentificeerd.

Het [kiezen van een partitie sleutel](partitioning-overview.md#choose-partitionkey) is een belang rijke beslissing die invloed heeft op de prestaties van uw toepassing.

## <a name="managing-logical-partitions"></a>Logische partities beheren

Azure Cosmos DB transparant en beheert automatisch de plaatsing van logische partities op fysieke partities om efficiënt te voldoen aan de schaal baarheid en prestatie behoeften van de container. Naarmate de door Voer en opslag vereisten van een toepassing toenemen, Azure Cosmos DB logische partities verplaatsen om de belasting automatisch over een groter aantal servers te spreiden. 

Azure Cosmos DB gebruikt op hash gebaseerde partitionering om logische partities over fysieke partities te verdelen. Azure Cosmos DB hashes de waarde van de partitie sleutel van een item. Het gehashte resultaat bepaalt de fysieke partitie. Azure Cosmos DB wijst vervolgens de sleutel ruimte van partitie sleutel-hashes gelijkmatig over de fysieke partities toe.

Query's die toegang hebben tot gegevens in één logische partitie, zijn rendabeler dan query's die toegang hebben tot meerdere partities. Trans acties (in opgeslagen procedures of triggers) zijn alleen toegestaan voor items in één logische partitie.

Zie [logische partities](partition-data.md)voor meer informatie over de manier waarop Azure Cosmos DB partities beheert. (Het is niet nodig om de interne gegevens te begrijpen om uw toepassingen te bouwen of uit te voeren, maar hier toe te voegen voor een nieuws lezer.)

## <a id="choose-partitionkey"></a>Een partitie sleutel kiezen

Hieronder vindt u een goede richt lijn voor het kiezen van een partitie sleutel:

* Eén logische partitie heeft een bovengrens van 20 GB aan opslag ruimte.  

* Azure Cosmos-containers hebben een minimale door Voer van 400 aanvraag eenheden per seconde (RU/s). Wanneer de door Voer is ingericht voor een Data Base, is het minimale RUs per container 100 aanvraag eenheden per seconde (RU/s). Aanvragen naar dezelfde partitie sleutel kunnen niet groter zijn dan de door Voer die is toegewezen aan een partitie. Als aanvragen de toegewezen door Voer overschrijden, zijn aanvragen een beperkt aantal. Het is dus belang rijk om een partitie sleutel te kiezen die niet resulteert in ' hot spots ' in uw toepassing.

* Kies een partitie sleutel met een breed scala aan waarden en toegangs patronen die gelijkmatig over logische partities worden verdeeld. Zo kunt u de gegevens en de activiteit in uw container verspreiden over de set logische partities, zodat resources voor gegevens opslag en-door Voer kunnen worden gedistribueerd over de logische partities.

* Kies een partitie sleutel waarmee de werk belasting gelijkmatig over alle partities en gelijkmatig over de tijd wordt verdeeld. De keuze van de partitie sleutel moet de nood zaak van efficiënte partitie query's en trans acties afstemmen op het doel van het distribueren van items over meerdere partities voor schaal baarheid.

* Kandidaten voor partitie sleutels kunnen eigenschappen bevatten die regel matig worden weer gegeven als een filter in uw query's. Query's kunnen efficiënt worden gerouteerd door de partitie sleutel op te nemen in het filter predicaat.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [partitioneren en horizon taal schalen in azure Cosmos DB](partition-data.md).
* Meer informatie over [ingerichte door Voer in azure Cosmos DB](request-units.md).
* Meer informatie over [globale distributie in azure Cosmos DB](distribute-data-globally.md).
