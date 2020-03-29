---
title: Partitionering in Azure Cosmos DB
description: Meer informatie over partitioneren in Azure Cosmos DB, aanbevolen procedures bij het kiezen van een partitiesleutel en het beheren van logische partities
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 551703b5dcca082904197010366ee059998dde4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251867"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Partitionering in Azure Cosmos DB

Azure Cosmos DB gebruikt partitionering om afzonderlijke containers in een database te schalen om te voldoen aan de prestatiebehoeften van uw toepassing. Bij het partitioneren worden de items in een container verdeeld in afzonderlijke subsets die *logische partities*worden genoemd. Logische partities worden gevormd op basis van de waarde van een *partitiesleutel* die is gekoppeld aan elk item in een container. Alle items in een logische partitie hebben dezelfde partitiesleutelwaarde.

Een container bevat bijvoorbeeld artikelen. Elk item heeft een `UserID` unieke waarde voor de eigenschap. Als `UserID` het gaat om de partitiesleutel voor de items in `UserID` de container en er zijn 1.000 unieke waarden, worden 1.000 logische partities gemaakt voor de container.

Naast een partitiesleutel die de logische partitie van het item bepaalt, heeft elk item in een container een *item-id* (uniek binnen een logische partitie). Door de partitiesleutel en de item-id te combineren, wordt de *index*van het item bepaald, waardoor het item uniek wordt geïdentificeerd.

[Het kiezen van een partitiesleutel](partitioning-overview.md#choose-partitionkey) is een belangrijke beslissing die de prestaties van uw toepassing zal beïnvloeden.

## <a name="managing-logical-partitions"></a>Logische partities beheren

Azure Cosmos DB beheert op transparante en automatisch de plaatsing van logische partities op fysieke partities om efficiënt te voldoen aan de schaalbaarheid en prestatiebehoeften van de container. Naarmate de doorvoer- en opslagvereisten van een toepassing toenemen, verplaatst Azure Cosmos DB logische partities om de belasting automatisch over een groter aantal servers te spreiden. 

Azure Cosmos DB maakt gebruik van hash-gebaseerde partitionering om logische partities over fysieke partities te verspreiden. Azure Cosmos DB hashes de partitiesleutelwaarde van een item. Het gehashte resultaat bepaalt de fysieke partitie. Vervolgens wijst Azure Cosmos DB de sleutelruimte van partitiesleutelhashes gelijkmatig toe over de fysieke partities.

Query's die toegang hebben tot gegevens binnen één logische partitie zijn kosteneffectiever dan query's die toegang hebben tot meerdere partities. Transacties (in opgeslagen procedures of triggers) zijn alleen toegestaan ten opzichte van artikelen in één logische partitie.

Zie [Logische partities](partition-data.md)voor meer informatie over hoe Azure Cosmos DB partities beheert. (Het is niet nodig om de interne details te begrijpen om uw toepassingen te bouwen of uit te voeren, maar hier toegevoegd voor een nieuwsgierige lezer.)

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Een partitiesleutel kiezen

Het volgende is een goede richtlijnen voor het kiezen van een partitiesleutel:

* Een enkele logische partitie heeft een bovengrens van 20 GB opslag.  

* Azure Cosmos-containers hebben een minimale doorvoer van 400 aanvraageenheden per seconde (RU/s). Wanneer doorvoer in een database wordt ingericht, zijn de minimum-RU's per container 100 aanvraageenheden per seconde (RU/s). Aanvragen voor dezelfde partitiesleutel kunnen de doorvoer die aan een partitie is toegewezen, niet overschrijden. Als aanvragen de toegewezen doorvoer overschrijden, zijn aanvragen tariefbeperkt. Het is dus belangrijk om een partitiesleutel te kiezen die niet resulteert in "hotspots" in uw toepassing.

* Kies een partitiesleutel met een breed scala aan waarden en toegangspatronen die gelijkmatig zijn verspreid over logische partities. Dit helpt bij het verspreiden van de gegevens en de activiteit in uw container over de set logische partities, zodat bronnen voor gegevensopslag en -doorvoer kunnen worden verdeeld over de logische partities.

* Kies een partitiesleutel die de werkbelasting gelijkmatig over alle partities en gelijkmatig in de loop van de tijd verspreidt. Uw keuze van partitiesleutel moet de behoefte aan efficiënte partitiequery's en transacties in evenwicht brengen met het doel om items over meerdere partities te distribueren om schaalbaarheid te bereiken.

* Kandidaten voor partitiesleutels kunnen eigenschappen bevatten die vaak worden weergegeven als een filter in uw query's. Query's kunnen efficiënt worden gerouteerd door de partitiesleutel op te nemen in het filterpredicaat.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [partitionering en horizontaal schalen in Azure Cosmos DB](partition-data.md).
* Meer informatie over [ingerichte doorvoer in Azure Cosmos DB](request-units.md).
* Meer informatie over [wereldwijde distributie in Azure Cosmos DB](distribute-data-globally.md).
