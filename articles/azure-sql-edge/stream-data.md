---
title: Streamen van gegevens in Azure SQL Edge (preview-versie)
description: Meer informatie over het streamen van gegevens in Azure SQL Edge (preview-versie)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: ea1bb05e111432a45fffa3b770e01c6da9a076b6
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233132"
---
# <a name="data-streaming-in-azure-sql-edge-preview"></a>Streamen van gegevens in Azure SQL Edge (preview-versie)

Azure SQL Edge (preview) biedt twee verschillende opties voor het implementeren van gegevens stromen. 

1. Implementatie van Azure streaming Analytics Edge-taken die zijn gemaakt in Azure. Ga voor meer informatie over het implementeren van Azure streaming Analytics-Edge-taken in Azure SQL Edge naar [deploy Azure stream Analytics Jobs](deploy-dacpac.md).
2. Met de nieuwe functie voor **T-SQL-streaming** kunt u streaming-taken maken in SQL-rand, zonder dat u streaming-taken hoeft te configureren in Azure. 

Hoewel het mogelijk is om beide opties te gebruiken voor het implementeren van gegevens stromen in SQL-rand, is het raadzaam om slechts één te gebruiken. Wanneer beide worden gebruikt, kunnen er timing fouten optreden die van invloed zijn op de werking van de gegevensstreaming-bewerkingen.

De rest van dit document verwijst naar de nieuwe functie, **T-SQL-streaming**, waarmee realtime gegevens stromen, analyses en gebeurtenis verwerking worden geboden voor het analyseren en verwerken van hoge volumes van gegevens snel streamen vanuit meerdere bronnen tegelijk. *Streaming met T-SQL* is gebouwd met behulp van dezelfde krachtige streaming-engine die [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction) in Microsoft Azure voorziet en een vergelijk bare reeks mogelijkheden ondersteunt die wordt geboden door Azure stream Analytics op de rand.

Net als bij Azure Stream Analytics maakt T-SQL-streaming herkenning mogelijk van patronen en relaties in informatie die is geëxtraheerd uit een aantal IoT-invoer bronnen, inclusief apparaten, Sens oren en toepassingen. Deze patronen kunnen worden gebruikt voor het activeren van acties en het initiëren van werk stromen, zoals het maken van waarschuwingen, het uitvoeren van gegevens naar een rapportage-of visualisatie-oplossing of het opslaan van de gegevens voor later gebruik. 

De volgende scenario's zijn voor beelden van wanneer u T-SQL-streaming kunt gebruiken:

* Analyseer realtime telemetriegegevens van IoT-apparaten.
* Real-time analyse van gegevens die zijn gegenereerd op basis van autonome en stuur programmaloze Voer tuigen.
* Externe controle en voor speld onderhoud van hoogwaardige industriële of productie-assets.
* Anomalie detectie en/of patroon herkenning van IoT-sensor aflezingen in een land bouwer of een energie-Farm.

## <a name="how-does-t-sql-streaming-work"></a>Hoe werkt T-SQL-streaming?

T-SQL-streaming werkt op exact dezelfde manier als [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-introduction#how-does-stream-analytics-work), bijvoorbeeld het concept van streaming-taken voor de verwerking van realtime gegevens stromen. 

Een stream Analytics-taak bestaat uit:

- Stroom invoer: een stroom invoer definieert de verbindingen met een gegevens bron waaruit de gegevens stroom moet worden gelezen. Azure SQL Edge ondersteunt momenteel de volgende invoer typen voor streams:
    - Edge hub
    - Kafka-ondersteuning voor Kafka-invoer is momenteel alleen beschikbaar voor Intel/AMD64-versies van Azure SQL Edge.

- Uitvoer van de stream: een stream-uitvoer definieert de verbindingen met een gegevens bron om de gegevens stroom naar te schrijven. Azure SQL Edge ondersteunt momenteel de volgende uitvoer typen voor streams
    - Edge hub
    - SQL-de SQL-uitvoer kan een lokale Data Base zijn binnen het SQL Edge-exemplaar of een externe SQL Server of Azure SQL Database. 
    - Azure Blob Storage

- Stream-query: de stream-query definieert de trans formatie, aggregaties, filters, sorteren en samen voegingen die moeten worden toegepast op de invoer stroom voordat deze naar de uitvoer van de stream wordt geschreven. De stroom query is gebaseerd op de query taal die wordt gebruikt door Azure Stream Analytics. Raadpleeg [Stream Analytics query taal](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)voor meer informatie over Azure stream Analytics query taal.

> [!IMPORTANT]
> T-SQL-streaming, in tegens telling tot Azure Stream Analytics, biedt momenteel geen ondersteuning [voor het gebruik van referentie gegevens voor Zoek opdrachten](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-use-reference-data) of het [gebruik van UDF en UDA in een stroom taak](https://docs.microsoft.com/azure/stream-analytics/streaming-technologies#you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c).

> [!NOTE]
> T-SQL streaming ondersteunt alleen een subset van de taal surface area die door Azure Stream Analytics wordt ondersteund. Zie [Stream Analytics query language (Engelstalig](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)) voor meer informatie over Azure stream Analytics query taal.

## <a name="limitations-and-restrictions"></a>Beperkingen en beperkingen

De volgende beperkingen en beperkingen zijn van toepassing op T-SQL-streaming. 

- Op elk gewenst moment kan slechts één streaming-taak actief zijn. Taken die al worden uitgevoerd, moeten worden gestopt voordat een andere taak kan worden gestart.
- De uitvoering van elke streaming-taak bestaat uit één thread. Als de streaming-taak meerdere query's bevat, wordt elke query in een seriële volg orde geëvalueerd.

## <a name="next-steps"></a>Volgende stappen

- [Een Stream Analytics-taak in Azure SQL Edge maken (preview)](create-stream-analytics-job.md)
- [Meta gegevens weer geven die zijn gekoppeld aan stroom taken in Azure SQL Edge (preview)](streaming-catalog-views.md)
- [Externe stroom maken](create-external-stream-transact-sql.md)