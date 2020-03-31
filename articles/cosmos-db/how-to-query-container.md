---
title: Query's uitvoeren op containers in Azure Cosmos DB
description: Meer informatie over het opvragen van containers in Azure Cosmos DB met in-partitie- en partitionerquery's
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131398"
---
# <a name="query-an-azure-cosmos-container"></a>Query’s uitvoeren op een Azure Cosmos-container

In dit artikel wordt uitgelegd hoe u query's kunt uitvoeren op containers (collecties, grafieken, tabellen) in Azure Cosmos DB. Het gaat met name over hoe in-partition- en cross-partition query's werken in Azure Cosmos DB.

## <a name="in-partition-query"></a>In-partitiequery

Wanneer u gegevens uit containers opvraagt, als de query een partitiesleutelfilter heeft opgegeven, optimaliseert Azure Cosmos DB de query automatisch. Hiermee wordt de query doorgestoofd naar de [fysieke partities](partition-data.md#physical-partitions) die overeenkomen met de partitiesleutelwaarden die in het filter zijn opgegeven.

Denk bijvoorbeeld aan de onderstaande query `DeviceId`met een gelijkheidsfilter op . Als we deze query uitvoeren op `DeviceId`een container die is verdeeld op, wordt deze query gefilterd op één fysieke partitie.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Net als in het eerdere voorbeeld wordt deze query ook gefilterd op één partitie. Het toevoegen van `Location` het extra filter aan verandert dit niet:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Hier is een query met een bereikfilter op de partitiesleutel en wordt niet naar één fysieke partitie getoepassingsgebied. Om een in-partitiequery te zijn, moet de query een gelijkheidsfilter hebben dat de partitiesleutel bevat:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Partitieoverkoepelende query

De volgende query heeft geen filter op`DeviceId`de partitiesleutel ( ). Daarom moet het uitwaaieren naar alle fysieke partities waar deze wordt uitgevoerd tegen de index van elke partitie:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Elke fysieke partitie heeft zijn eigen index. Wanneer u een kruispartitiequery uitvoert op een container, voert u dus effectief één query *per* fysieke partitie uit. Azure Cosmos DB verzamelt automatisch resultaten over verschillende fysieke partities.

De indexen in verschillende fysieke partities zijn onafhankelijk van elkaar. Er is geen globale index in Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Parallelle partitieoverkoepelende query

De Cosmos DB-SDK's 1.9.0 en hoger bieden ondersteuning voor opties voor parallelle query-uitvoering. Met parallelle partitieoverkoepelende query's kunt u query's met lage latentie uitvoeren op meerdere partities.

U kunt parallelle queryuitvoering beheren door de volgende parameters af te stemmen:

- **MaxConcurrency**: Hiermee stelt u het maximum aantal gelijktijdige netwerkverbindingen in op de partities van de container. Als u deze `-1`eigenschap instelt op , beheert de SDK de mate van parallellisme. Als `MaxConcurrency` de `0`ingestelde , is er een enkele netwerkverbinding met de partities van de container.

- **MaxBufferedItemCount**: hiermee wordt de latentie van de query ingewisseld voor het geheugengebruik aan de clientzijde. Als deze optie wordt weggelaten of ingesteld op -1, wordt het aantal items dat in de buffer opgeslagen tijdens parallelle query-uitvoering, beheerd door de SDK.

Vanwege de mogelijkheid van Azure Cosmos DB om cross-partitiequery's te paralleleren, zal querylatentie over het algemeen goed schalen als het systeem [fysieke partities](partition-data.md#physical-partitions)toevoegt. Ru-kosten zullen echter aanzienlijk toenemen naarmate het totale aantal fysieke partities toeneemt.

Wanneer u een kruispartitiequery uitvoert, doet u in wezen een afzonderlijke query per afzonderlijke fysieke partitie. Hoewel query's met kruispartitiequery's de index gebruiken, zijn ze, indien beschikbaar, nog lang niet zo efficiënt als in-partition query's.

## <a name="useful-example"></a>Nuttig voorbeeld

Hier is een analogie om cross-partitie query's beter te begrijpen:

Stel je voor dat je een bezorger bent die pakketten moet afleveren bij verschillende appartementencomplexen. Elk appartementencomplex heeft een lijst op het terrein met alle eenheidsnummers van de bewoner. We kunnen elk appartementencomplex vergelijken met een fysieke partitie en elke lijst met de index van de fysieke partitie.

We kunnen query's in partitie en partitie vergelijken met behulp van dit voorbeeld:

### <a name="in-partition-query"></a>In-partitiequery

Als de bezorger het juiste appartementencomplex (fysieke partitie) kent, kunnen ze meteen naar het juiste gebouw rijden. De chauffeur kan de lijst van de eenheidsnummers van de bewoner (de index) controleren en snel de juiste pakketten bezorgen. In dit geval verspilt de chauffeur geen tijd of moeite om naar een appartementencomplex te rijden om te controleren of er pakketontvangers wonen.

### <a name="cross-partition-query-fan-out"></a>Kruispartitiequery (uitwaaiering)

Als de bezorger niet het juiste appartementencomplex (fysieke partitie) kent, moeten ze naar elk appartementencomplex rijden en de lijst controleren met alle eenheidsnummers van de bewoner (de index). Zodra ze bij elk appartementencomplex aankomen, kunnen ze nog steeds de lijst met adressen van elke bewoner gebruiken. Ze moeten echter de lijst van elk appartementencomplex controleren, of er nu pakketontvangers wonen of niet. Dit is hoe cross-partitiequery's werken. Hoewel ze de index kunnen gebruiken (hoeven niet op elke deur te kloppen), moeten ze de index afzonderlijk controleren voor elke fysieke partitie.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Kruispartitiequery (beperkt tot slechts een paar fysieke partities)

Als de bezorger weet dat alle pakketontvangers in een aantal appartementencomplexen wonen, hoeven ze niet naar elke woning te rijden. Tijdens het rijden naar een paar appartementencomplexen zal nog steeds meer werk dan een bezoek aan slechts een enkel gebouw, de bezorger nog steeds bespaart aanzienlijke tijd en moeite. Als een query de partitiesleutel in `IN` het filter met het trefwoord heeft, controleert deze alleen de indexen van de relevante fysieke partitie op gegevens.

## <a name="avoiding-cross-partition-queries"></a>Kruispartitiequery's vermijden

Voor de meeste containers is het onvermijdelijk dat u een aantal cross-partitiequery's hebt. Na een aantal cross-partitie query's is ok! Bijna alle querybewerkingen worden ondersteund voor partities (zowel logische partitiesleutels als fysieke partities). Azure Cosmos DB heeft ook veel optimalisaties in de queryengine en client-SDK's om query-uitvoering te paralleleren tussen fysieke partities.

Voor de meeste leeszware scenario's raden we u aan gewoon de meest voorkomende eigenschap in uw queryfilters te selecteren. U moet er ook voor zorgen dat uw partitiesleutel zich houdt aan andere best practices voor [het selecteren van partitiesleutelselectie.](partitioning-overview.md#choose-partitionkey)

Het vermijden van cross-partitie query's meestal alleen zaken met grote containers. Er worden minimaal ongeveer 2,5 RU's in rekening gebracht elke keer dat u de index van een fysieke partitie controleert op resultaten, zelfs als er geen items in de fysieke partitie overeenkomen met het filter van de query. Als u dus slechts één (of slechts enkele) fysieke partities hebt, zullen kruispartitiequery's niet aanzienlijk meer RU's verbruiken dan in-partitiequery's.

Het aantal fysieke partities is gekoppeld aan het aantal ingerichte RU's. Elke fysieke partitie biedt maximaal 10.000 ingerichte RU's en kan tot 50 GB aan gegevens opslaan. Azure Cosmos DB beheert automatisch fysieke partities voor u. Het aantal fysieke partities in uw container is afhankelijk van uw ingerichte doorvoer en verbruikte opslag.

U moet proberen kruispartitiequery's te vermijden als uw werkbelasting voldoet aan de onderstaande criteria:
- U bent van plan om meer dan 30.000 RU's te laten inrichten
- U bent van plan meer dan 100 GB aan gegevens op te slaan

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over partitionering in Azure Cosmos DB:

- [Partitionering in Azure Cosmos DB](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB](synthetic-partition-keys.md) (Synthetische partitiesleutels in Azure Cosmos DB)
