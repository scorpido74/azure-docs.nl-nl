---
title: Op- en uitschalen in Azure Stream Analytics-taken
description: In dit artikel wordt beschreven hoe u een Taak Van Stream Analytics schalen door invoergegevens te partitioneren, de query af te stemmen en taakstreaming-eenheden in te stellen.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: d828103bef8e57f5d0cdfe6c243c52e2d0526663
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257543"
---
# <a name="scale-an-azure-stream-analytics-job-to-increase-throughput"></a>Een Azure Stream Analytics-taak schalen om de doorvoer te verhogen
In dit artikel ziet u hoe u een Stream Analytics-query afstemmen om de doorvoer voor Streaming Analytics-taken te verhogen. U de volgende handleiding gebruiken om uw taak te schalen om een hogere belasting te verwerken en te profiteren van meer systeembronnen (zoals meer bandbreedte, meer CPU-bronnen, meer geheugen).
Als voorwaarde moet u mogelijk de volgende artikelen lezen:
-   [Streaming-eenheden begrijpen en aanpassen](stream-analytics-streaming-unit-consumption.md)
-   [Parallelle banen maken](stream-analytics-parallelization.md)

## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Case 1 – Uw query is inherent volledig parallelizable over invoerpartities
Als uw query inherent volledig parallelizable is tussen invoerpartities, u de volgende stappen volgen:
1.  Auteur van uw vraag te worden beschamend parallel met behulp van **PARTITIE PER** trefwoord. Zie meer details in de beschamend parallelle vacatures sectie [op deze pagina](stream-analytics-parallelization.md).
2.  Afhankelijk van de uitvoertypen die in uw query worden gebruikt, kan sommige uitvoer niet parallelulerend zijn of dat verdere configuratie gênant parallel moet zijn. PowerBI-uitgang is bijvoorbeeld niet parallelbaar. Uitgangen worden altijd samengevoegd voordat ze naar de uitvoergootsteen worden verzonden. Blobs, Tabellen, ADLS, Service Bus en Azure-functie worden automatisch parallel. SQL- en SQL DW-uitvoer hebben een optie voor parallelisatie. Gebeurtenishub moet de PartitionKey-configuratie hebben ingesteld op overeenkomen met het veld **PARTITION BY** (meestal PartitionId). Let bij Event Hub ook extra op het aantal partities voor alle ingangen en alle uitgangen om cross-over tussen partities te voorkomen. 
3.  Voer uw query uit met **6 SU** (dat is de volledige capaciteit van een enkel computerknooppunt) om de maximaal haalbare doorvoer te meten en als u **GROUP BY**gebruikt, meet u hoeveel groepen (kardinaliteit) de taak aankan. Algemene symptomen van de taak raken systeem resource grenzen zijn de volgende.
    - SU % bezettingsgraad is meer dan 80%. Dit geeft aan dat het geheugengebruik hoog is. De factoren die bijdragen aan de toename van deze statistiek worden [hier](stream-analytics-streaming-unit-consumption.md)beschreven . 
    -   Output tijdstempel valt achter met betrekking tot de muur klok tijd. Afhankelijk van uw querylogica kan de uitvoertijdstempel een logische verschuiving hebben ten opzichte van de kloktijd. Zij moeten echter ongeveer hetzelfde tempo vorderen. Als de output tijdstempel steeds verder achterop raakt, is het een indicator dat het systeem overwerkt. Het kan een gevolg zijn van downstream output sink throttling, of een hoog CPU-gebruik. We bieden op dit moment geen cpu-gebruiksmetriek, dus het kan moeilijk zijn om de twee te onderscheiden.
        - Als het probleem te wijten is aan sink throttling, moet u mogelijk het aantal uitvoerpartities verhogen (en ook partities invoeren om de taak volledig parallelteerbaar te houden) of de hoeveelheid resources van de gootsteen te verhogen (bijvoorbeeld het aantal aanvraageenheden voor CosmosDB).
    - In taakdiagram is er een gebeurtenisstatistiek per partitievoor elke invoer. Als de statistiek van de gebeurtenis achterstand blijft toenemen, is het ook een indicator dat de systeembron is beperkt (vanwege uitvoersinkbeperking of hoge CPU).
4.  Zodra u de grenzen hebt bepaald van wat een 6 SU-taak kan bereiken, u lineair de verwerkingscapaciteit van de taak extrapoleren terwijl u meer SU's toevoegt, ervan uitgaande dat u geen gegevensscheeftrekking hebt die bepaalde partitie hot maakt.

> [!NOTE]
> Kies het juiste aantal streamingeenheden: Omdat Stream Analytics een verwerkingsknooppunt maakt voor elke 6 SU toegevoegd, is het het beste om het aantal knooppunten een deler te maken van het aantal invoerpartities, zodat de partities gelijkmatig over de knooppunten kunnen worden verdeeld.
> U hebt bijvoorbeeld gemeten of uw 6 SU-taak 4 MB/s-verwerkingssnelheid kan bereiken en het aantal invoerpartities is 4. U ervoor kiezen om uw taak uit te voeren met 12 SU om ongeveer 8 MB /s verwerkingssnelheid te bereiken, of 24 SU om 16 MB/s te bereiken. U vervolgens bepalen wanneer u het SU-nummer voor de taak wilt verhogen tot welke waarde, als functie van uw invoersnelheid.


## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Case 2 - Als uw vraag niet beschamend parallel is.
Als uw query niet gênant parallel is, u de volgende stappen volgen.
1.  Begin met een query zonder **PARTITIE BY** eerst om partitiecomplexiteit te voorkomen en voer uw query uit met 6 SU om de maximale belasting te meten zoals in [zaak 1.](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions)
2.  Als u uw verwachte belasting in termijn van doorvoer bereiken, bent u gepresteerd. U er ook voor kiezen om dezelfde taak te meten die wordt uitgevoerd bij 3 SU en 1 SU, om het minimumaantal SU te achterhalen dat werkt voor uw scenario.
3.  Als u de gewenste doorvoer niet bereiken, probeert u uw query zo mogelijk in meerdere stappen op te splitsen, als deze nog niet meerdere stappen heeft en wijst u maximaal 6 SU toe voor elke stap in de query. Als u bijvoorbeeld 3 stappen hebt, wijst u 18 SU toe in de optie Schalen.
4.  Bij het uitvoeren van een dergelijke taak zet Stream Analytics elke stap op zijn eigen knooppunt met speciale 6 SU-resources. 
5.  Als u uw laaddoel nog steeds niet hebt bereikt, u proberen partitie te gebruiken **door** vanaf stappen dichter bij de invoer te gaan. Voor **groep per** operator die mogelijk niet van nature partitionerbaar is, u het lokale/globale geaggregeerde patroon gebruiken om een verdeelde GROEP uit te voeren **door** gevolgd door een niet-verdeelde GROEP **DOOR**. Bijvoorbeeld, als u wilt tellen hoeveel auto's gaan door elke tolcabine om de 3 minuten, en het volume van de gegevens is dan wat kan worden behandeld door 6 SU.

Query:

 ```SQL
 WITH Step1 AS (
 SELECT COUNT(*) AS Count, TollBoothId, PartitionId
 FROM Input1 Partition By PartitionId
 GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
 )
 SELECT SUM(Count) AS Count, TollBoothId
 FROM Step1
 GROUP BY TumblingWindow(minute, 3), TollBoothId
 ```
In de bovenstaande query telt u auto's per tolcel per partitie en voegt u het aantal van alle partities samen toe.

Eenmaal verdeeld, voor elke partitie van de stap, toewijzen tot 6 SU, elke partitie met 6 SU is het maximum, zodat elke partitie kan worden geplaatst op zijn eigen verwerking knooppunt.

> [!Note]
> Als uw query niet kan worden gepartitioneerd, wordt de doorvoer mogelijk niet altijd verbeterd door extra SU toe te voegen in een query met meerdere stappen. Een manier om prestaties te behalen is om het volume te verminderen op de eerste stappen met behulp van lokale / globale geaggregeerde patroon, zoals hierboven beschreven in stap 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Case 3 - U voert veel onafhankelijke query's uit in een taak.
Voor bepaalde ISV-gebruiksaanvragen, waarbij het kostenefficiënter is om gegevens van meerdere tenants in één taak te verwerken, met behulp van afzonderlijke ingangen en uitvoer voor elke tenant, u uiteindelijk een flink aantal (bijvoorbeeld 20) onafhankelijke query's uitvoeren in één taak. De aanname is dat de belasting van elke subquery relatief klein is. In dit geval u de volgende stappen volgen.
1.  Gebruik in dit geval GEEN **PARTITION BY** in de query
2.  Verlaag het aantal invoerpartities tot de laagst mogelijke waarde van 2 als u Event Hub gebruikt.
3.  Voer de query uit met 6 SU. Met de verwachte belasting voor elke subquery voegt u zoveel mogelijk van dergelijke subquery's toe, totdat de taak systeembronlimieten raakt. Raadpleeg [case 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) voor de symptomen wanneer dit gebeurt.
4.  Zodra u de subquerylimiet bereikt die hierboven is gemeten, begint u met het toevoegen van de subquery aan een nieuwe taak. Het aantal taken dat moet worden uitgevoerd als functie van het aantal onafhankelijke query's moet vrij lineair zijn, ervan uitgaande dat u geen belastingsscheeftrekking hebt. U vervolgens voorspellen hoeveel 6 SU-taken u moet uitvoeren als functie van het aantal tenants dat u wilt bedienen.
5.  Wanneer u referentiegegevens gebruikt, worden de invoer samengevoegd voordat u met dezelfde referentiegegevens wordt samengevoegd. Splits vervolgens de gebeurtenissen indien nodig uit. Anders houdt elke verwijzingsgegevensjoin een kopie van referentiegegevens in het geheugen, waardoor het geheugengebruik waarschijnlijk onnodig wordt opgeblazen.

> [!Note] 
> Hoeveel huurders te zetten in elke baan?
> Dit querypatroon heeft vaak een groot aantal subquery's en resulteert in een zeer grote en complexe topologie. De controller van de taak kan niet in staat zijn om een dergelijke grote topologie te behandelen. Als vuistregel geldt dat u onder de 40 huurders blijft voor 1 SU-baan en 60 huurders voor 3 SU- en 6 SU-banen. Wanneer u de capaciteit van de controller overschrijdt, wordt de taak niet succesvol gestart.



## <a name="get-help"></a>Help opvragen
Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: https://support.microsoft.com
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

