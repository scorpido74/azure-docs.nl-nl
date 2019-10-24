---
title: De kosten van lees-en schrijf bewerkingen in Azure Cosmos DB optimaliseren
description: In dit artikel wordt uitgelegd hoe u Azure Cosmos DB kosten kunt verminderen bij het uitvoeren van lees-en schrijf bewerkingen op de gegevens.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 934853b80c6e6377923df4c2b5cce7b7d7d57d7c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754935"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>De kosten voor lees-en schrijf bewerkingen optimaliseren in Azure Cosmos DB

In dit artikel wordt beschreven hoe de kosten die nodig zijn om gegevens van Azure Cosmos DB te lezen en te schrijven, worden berekend. Lees bewerkingen zijn onder meer Get-bewerkingen voor items en schrijf bewerkingen omvatten invoegen, vervangen, verwijderen en upsert van items.  

## <a name="cost-of-reads-and-writes"></a>Kosten van lees-en schrijf bewerkingen

Azure Cosmos DB garandeert voorspel bare prestaties in termen van door Voer en latentie met behulp van een ingericht doorvoer model. De ingerichte door Voer is weer gegeven in termen van [aanvraag eenheden](request-units.md) per seconde, of ru/s. Een aanvraag eenheid (RU) is een logische abstractie voor reken resources, zoals CPU, geheugen, IO, enzovoort, die vereist zijn om een aanvraag uit te voeren. De ingerichte door Voer (RUs) wordt gereserveerd en toegewezen aan uw container of Data Base om voorspel bare door Voer en latentie te bieden. Met ingerichte door Voer kunnen Azure Cosmos DB voorspel bare en consistente prestaties bieden, gegarandeerd lage latentie en hoge Beschik baarheid op elke schaal. Aanvraag eenheden vertegenwoordigen de genormaliseerde valuta die de reden vereenvoudigt van het aantal resources dat een toepassing nodig heeft. 

U hoeft niet na te denken over afwijkende aanvraag eenheden tussen lees-en schrijf bewerkingen. Het Unified Currency-model van aanvraag eenheden maakt efficiency verbeteringen voor zowel lees-als schrijf capaciteit. In de volgende tabel ziet u de kosten van lees-en schrijf bewerkingen in termen van RU/s voor items die 1 KB en 100 KB groot zijn.

|**Item grootte**  |**Kosten per Lees bewerking** |**Kosten per schrijf bewerking**|
|---------|---------|---------|
|1 KB |1 RU |5 RUs |
|100 kB |Tien aanvraageenheden |50 RUs |

Het lezen van een item met een grootte van 1 KB per RU. Een item schrijven dat 1 KB kosten vijf RUs heeft. De lees-en schrijf kosten zijn van toepassing wanneer u het standaard [consistentie niveau](consistency-levels.md)van de sessie gebruikt.  Aandachtspunten voor RUs zijn: item grootte, aantal eigenschappen, consistentie van gegevens, geïndexeerde eigenschappen, indexering en query patronen.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>Genormaliseerde kosten voor 1.000.000 Lees-en schrijf bewerkingen

Het inrichten van 1.000 RU/s verloopt op 3.600.000 RU/uur en kost $0,08 voor het uur (in de Verenigde Staten en Europa). Voor een 1-KB-item kunt u 3.600.000 Lees-of 720.000-schrijf bewerkingen uitvoeren (deze waarde wordt als volgt berekend: `3.6 million RU / 5`) per uur met deze ingerichte door voer. De kosten zijn genormaliseerd tot een miljoen Lees-en schrijf bewerkingen, maar $0,022 voor 1.000.000 Lees bewerkingen (deze waarde wordt berekend als: $0.08/3.6 miljoen) en $0,111 voor 1.000.000 schrijf bewerkingen (deze waarde wordt berekend als: $0.08/0,72 miljoen).

## <a name="number-of-regions-and-the-request-units-cost"></a>Aantal regio's en kosten van aanvraag eenheden

De kosten voor schrijf bewerkingen zijn constant, ongeacht het aantal regio's dat is gekoppeld aan het Azure Cosmos-account. Met andere woorden, een schrijf bewerking van 1 KB kost vijf RUs, onafhankelijk van het aantal regio's dat aan het account is gekoppeld. Er is een niet-gelastige hoeveelheid resources die is besteed aan het repliceren, accepteren en verwerken van het replicatie verkeer in elke regio. Zie [het artikel kosten van Multi-Region Cosmos-accounts optimaliseren](optimize-cost-regions.md) voor meer informatie over de optimalisatie van de kosten voor meerdere regio's.

## <a name="optimize-the-cost-of-writes-and-reads"></a>De kosten van schrijf bewerkingen en lees bewerkingen optimaliseren

Wanneer u schrijf bewerkingen uitvoert, moet u voldoende capaciteit inrichten ter ondersteuning van het aantal schrijf bewerkingen dat nodig is per seconde. U kunt de ingerichte door Voer verhogen met behulp van SDK, portal, CLI voordat u de schrijf bewerkingen uitvoert en de door Voer verminderen nadat de schrijf bewerkingen zijn voltooid. Uw door Voer voor de schrijf periode is de minimale door Voer die nodig is voor de gegeven gegevens, plus de door Voer die vereist is voor het invoegen van de werk belasting, uitgaande van andere werk belastingen. 

Als u andere werk belastingen gelijktijdig uitvoert, bijvoorbeeld query/lezen/bijwerken/verwijderen, moet u ook de extra aanvraag eenheden toevoegen die vereist zijn voor deze bewerkingen. Als de schrijf bewerkingen de frequentie beperkt zijn, kunt u het beleid voor opnieuw proberen of uitstel aanpassen met behulp van Azure Cosmos DB Sdk's. U kunt bijvoorbeeld de belasting verg Roten tot een klein aantal aanvragen een beperkt aantal wordt. Als er sprake is van frequentie limiet, moet de client toepassing een back-up maken van aanvragen met een frequentie beperking voor het opgegeven interval voor nieuwe pogingen. Voordat u opnieuw probeert te schrijven, moet u een minimale hoeveelheid tijd tussen nieuwe pogingen hebben. Ondersteuning voor het beleid voor opnieuw proberen is opgenomen in SQL .NET-, Java-, node. js-en python-Sdk's en alle ondersteunde versies van de .NET core-Sdk's. 

U kunt ook bulksgewijs gegevens invoegen in Azure Cosmos DB of gegevens uit een ondersteund bron gegevens archief naar Azure Cosmos DB kopiëren met behulp van [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md). Azure Data Factory systeem eigen integreert met de Azure Cosmos DB bulk-API om de beste prestaties te bieden, wanneer u gegevens schrijft.

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u meer te weten komen over cost Optimization in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over het [optimaliseren van opslag kosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
