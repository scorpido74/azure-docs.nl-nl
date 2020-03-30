---
title: De kosten van lezen en schrijven optimaliseren in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u de DB-kosten van Azure Cosmos verlagen bij het uitvoeren van lees- en schrijfbewerkingen op de gegevens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 725876594a7e7c5f3b3a02802f487dc5fdfb64dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535932"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Lees- en schrijfkosten optimaliseren in Azure Cosmos DB

In dit artikel wordt beschreven hoe de kosten voor het lezen en schrijven van gegevens van Azure Cosmos DB worden berekend. Leesbewerkingen omvatten bewerkingen voor items en schrijfbewerkingen omvatten invoegen, vervangen, verwijderen en upsert van items.  

## <a name="cost-of-reads-and-writes"></a>Kosten van lezen en schrijven

Azure Cosmos DB garandeert voorspelbare prestaties op het gebied van doorvoer en latentie met behulp van een ingerichte doorvoermodel. De beoogde doorvoer wordt weergegeven in termen van [aanvraageenheden](request-units.md) per seconde of RU/s. Een Aanvraageenheid (RU) is een logische abstractie ten opzichte van rekenbronnen zoals CPU, geheugen, IO, enz. De ingerichte doorvoer (RU's) is gereserveerd en gewijd aan uw container of database om voorspelbare doorvoer en latentie te bieden. Met de ingerichte doorvoer kan Azure Cosmos DB voorspelbare en consistente prestaties, gegarandeerde lage latentie en hoge beschikbaarheid op elke schaal bieden. Aanvraageenheden vertegenwoordigen de genormaliseerde valuta die de redenering over het aantal resources dat een toepassing nodig heeft, vereenvoudigt. 

U hoeft niet na te denken over het differentiëren van aanvraageenheden tussen lezen en schrijven. Het uniforme valutamodel van aanvraageenheden creëert efficiëntieom door elkaar dezelfde doorvoercapaciteit te gebruiken voor zowel lees- als schrijfbewerkingen. In de volgende tabel ziet u de kosten van het lezen en schrijven in termen van RU/s voor artikelen met een grootte van 1 KB en 100 KB.

|**Objectgrootte**  |**Kosten van één gelezen** |**Kosten van een schrijven**|
|---------|---------|---------|
|1 kB |1 RU |5 -10U's |
|100 kB |Tien aanvraageenheden |50 10 000 |

Het lezen van een item dat 1 KB groot is kost een RU. Het schrijven van een item dat 1-KB kost vijf AMERIKAANSE. De lees- en schrijfkosten zijn van toepassing bij het gebruik van het [standaardconsistentieniveau](consistency-levels.md)van de sessie .  De overwegingen rond R's zijn: itemgrootte, eigenschapstelling, gegevensconsistentie, geïndexeerde eigenschappen, indexering en querypatronen.

## <a name="optimize-the-cost-of-writes-and-reads"></a>De kosten van schrijven en lezen optimaliseren

Wanneer u schrijfbewerkingen uitvoert, moet u voldoende capaciteit inrichten om het aantal schrijfbewerkingen te ondersteunen dat per seconde nodig is. U de ingerichte doorvoer verhogen door SDK, portal, CLI te gebruiken voordat u de schrijfbewerkingen uitvoert en vervolgens de doorvoer te verlagen nadat de schrijfbewerkingen zijn voltooid. Uw doorvoer voor de schrijfperiode is de minimale doorvoer die nodig is voor de gegeven gegevens, plus de doorvoer die nodig is voor het invoegen van werkbelasting ervan uitgaande dat er geen andere workloads worden uitgevoerd. 

Als u andere workloads gelijktijdig uitvoert, bijvoorbeeld query/read/update/delete, moet u ook de extra aanvraageenheden toevoegen die nodig zijn voor die bewerkingen. Als de schrijfbewerkingen beperkt zijn, u het beleid voor opnieuw proberen/back-off aanpassen met Azure Cosmos DB SDKs. U bijvoorbeeld de belasting verhogen totdat een klein aantal aanvragen tariefbeperkt wordt. Als de tarieflimiet optreedt, moet de clienttoepassing zich terugtrekken op tariefbeperkende aanvragen voor het opgegeven interval voor het opnieuw proberen. Voordat u schrijft opnieuw probeert, moet u een minimale tijdsruimte tussen nieuwe pogingen hebben. Beleidsondersteuning opnieuw proberen is opgenomen in SQL .NET, Java, Node.js en Python SDKs en alle ondersteunde versies van de .NET Core SDKs. 

U ook gegevens invoegen in Azure Cosmos DB of gegevens uit een ondersteunde brongegevensopslag kopiëren naar Azure Cosmos DB met [Azure Data Factory.](../data-factory/connector-azure-cosmos-db.md) Azure Data Factory integreert native met de Azure Cosmos DB Bulk API om de beste prestaties te bieden, wanneer u gegevens schrijft.

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer te weten komen over kostenoptimalisatie in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten van Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
