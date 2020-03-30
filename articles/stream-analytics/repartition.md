---
title: Repartitionering gebruiken om Azure Stream Analytics-taken te optimaliseren
description: In dit artikel wordt beschreven hoe u repartitionering gebruikt om Azure Stream Analytics-taken te optimaliseren die niet kunnen worden parallelgezet.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/19/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: c70cfb6c1626908a2ba4e707a890f6dc7481c51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732379"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Repartitionering gebruiken om de verwerking te optimaliseren met Azure Stream Analytics

In dit artikel ziet u hoe u repartitionering gebruikt om uw Azure Stream Analytics-query te schalen voor scenario's die niet volledig kunnen worden [parallelgemaakt.](stream-analytics-scale-jobs.md)

Mogelijk u [geen parallelisatie](stream-analytics-parallelization.md) gebruiken als:

* U beheert de partitiesleutel voor uw invoerstream niet.
* Uw bron "sprays" input over meerdere partities die later moeten worden samengevoegd.

Herverdelen of herschikken is vereist wanneer u gegevens verwerkt op een stream die niet is geshard volgens een natuurlijk invoerschema, zoals **PartitionId** voor gebeurtenishubs. Wanneer u opnieuw partitie, kan elke scherf onafhankelijk worden verwerkt, waardoor u lineair uw streamingpijplijn schalen.

## <a name="how-to-repartition"></a>Opnieuw verdelen

Als u opnieuw wilt partitioneren, gebruikt u het trefwoord **INTO** na een **partitie per** instructie in uw query. In het volgende voorbeeld worden de gegevens per **DeviceID** verdeeld in een partitieaantal van 10. Hashing van **DeviceID** wordt gebruikt om te bepalen welke partitie welke substream moet accepteren. De gegevens worden onafhankelijk gespoeld voor elke partitiestroom, ervan uitgaande dat de uitvoer partitieschrijfbewerkingen ondersteunt en 10 partities heeft.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

In het volgende voorbeeldquery worden twee gegevensstromen met een herverdeling samengebracht. Wanneer u twee stromen van opnieuw verdeelde gegevens samenvoegt, moeten de streams dezelfde partitiesleutel en het zelfde aantal hebben. Het resultaat is een stroom met hetzelfde partitieschema.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

Het uitvoerschema moet overeenkomen met de toets van het stroomschema en tellen, zodat elke substream onafhankelijk kan worden gespoeld. De stroom kan ook worden samengevoegd en opnieuw worden geherpartitioneerd door een ander schema voordat u doorspoelt, maar u moet die methode vermijden omdat deze wordt toegevoegd aan de algemene latentie van de verwerking en het gebruik van resources verhoogt.

## <a name="streaming-units-for-repartitions"></a>Streaming-eenheden voor herpartities

Experimenteer en observeer het resourcegebruik van uw taak om het exacte aantal partities te bepalen dat u nodig hebt. Het aantal [streaming-eenheden (SU)](stream-analytics-streaming-unit-consumption.md) moet worden aangepast aan de fysieke resources die nodig zijn voor elke partitie. In het algemeen zijn zes SU's nodig voor elke partitie. Als er onvoldoende resources aan de taak zijn toegewezen, past het systeem de herverdeling alleen toe als de taak ten goede komt.

## <a name="repartitions-for-sql-output"></a>Repartities voor SQL-uitvoer

Wanneer uw taak SQL-database gebruikt voor uitvoer, gebruikt u expliciete herverdeling om het optimale aantal partities te evenaren om de doorvoer te maximaliseren. Aangezien SQL het beste werkt met acht schrijvers, kan het opnieuw verdelen van de stroom naar acht voordat u doorspoelt, of ergens verder stroomopwaarts, ten goede komen aan de prestaties van de taak. 

Wanneer er meer dan 8 invoerpartities zijn, is het overnemen van het invoerpartitieschema mogelijk geen geschikte keuze. Overweeg [in uw](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) query INTO te gebruiken om het aantal uitvoerschrijvers expliciet op te geven. 

Het volgende voorbeeld leest uit de invoer, ongeacht of deze op natuurlijke wijze wordt verdeeld, en herschikt de stream tienkeer volgens de DeviceID-dimensie en spoelt de gegevens door naar uitvoer. 

```sql
SELECT * INTO [output] FROM [input] PARTITION BY DeviceID INTO 10
```

Zie [Azure Stream Analytics-uitvoer naar Azure SQL Database voor](stream-analytics-sql-output-perf.md)meer informatie.


## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Stream Analytics](stream-analytics-introduction.md)
* [Gebruik maken van queryparallelisatie in Azure Stream Analytics](stream-analytics-parallelization.md)
