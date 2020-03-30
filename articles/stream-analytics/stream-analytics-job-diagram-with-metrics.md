---
title: Gegevensgestuurde foutopsporing in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u problemen oplossen met uw Azure Stream Analytics-taak met behulp van het taakdiagram en de statistieken in de Azure-portal.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426076"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Gegevensgestuurde foutopsporing met behulp van het taakdiagram

Met het taakdiagram op het **controleblad** in de Azure-portal u uw taakpijplijn visualiseren. Hierin worden invoer, uitvoer en querystappen weergegeven. U kunt het taakdiagram gebruiken om de metrische gegevens voor elke stap te onderzoeken, om de bron van het probleem sneller te isoleren bij het oplossen van problemen.

## <a name="using-the-job-diagram"></a>Het taakdiagram gebruiken

Selecteer **taakdiagram**in de Azure-portal in een functie Stream Analytics onder **ONDERSTEUNING + PROBLEEMOPLOSSING:**

![Taakdiagram met statistieken - locatie](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Selecteer elke querystap om de bijbehorende sectie in een querybewerkingsvenster te bekijken. Een metrische grafiek voor de stap wordt weergegeven in een lager deelvenster op de pagina.

![Taakdiagram met statistieken - basistaak](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Als u de partities van de invoer van Azure Event Hubs wilt bekijken, selecteert u **. . .** Er verschijnt een contextmenu. U ook de inputfusie zien.

![Taakdiagram met statistieken - partitie uitbreiden](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Als u de metrische grafiek voor slechts één partitie wilt weergeven, selecteert u het partitieknooppunt. De statistieken worden onder aan de pagina weergegeven.

![Taakdiagram met statistieken - meer statistieken](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Als u de grafiek met statistieken voor een fusie wilt bekijken, selecteert u het fusieknooppunt. De volgende grafiek laat zien dat er geen gebeurtenissen zijn verwijderd of aangepast.

![Taakdiagram met statistieken - raster](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Als u de details van de metrische waarde en tijd wilt zien, wijst u de grafiek aan.

![Taakdiagram met statistieken - zweven](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Problemen oplossen met behulp van statistieken

De statistiek **QueryLastProcessedTime** geeft aan wanneer een specifieke stap gegevens heeft ontvangen. Door naar de topologie te kijken, u vanuit de uitvoerprocessor achteruit werken om te zien welke stap geen gegevens ontvangt. Als een stap geen gegevens krijgt, gaat u naar de querystap net daarvoor. Controleer of de voorgaande querystap een tijdvenster heeft en of er voldoende tijd is verstreken om deze uit te voeren naar uitvoergegevens. (Houd er rekening mee dat tijdvensters aan het uur worden vastgemaakt.)
 
Als de vorige querystap een invoerprocessor is, gebruikt u de invoerstatistieken om de volgende gerichte vragen te beantwoorden. Ze kunnen u helpen bepalen of een taak gegevens uit de invoerbronnen haalt. Controleer elke partitie als de query gepartitioneerd is.
 
### <a name="how-much-data-is-being-read"></a>Hoeveel gegevens worden er gelezen?

*   **InputEventsSourcesTotal** is het aantal gelezen gegevenseenheden. Bijvoorbeeld het aantal blobs.
*   **InputEventsTotaal** is het aantal gelezen gebeurtenissen. Deze metrische waarde is beschikbaar per partitie.
*   **InputEventsInBytesTotaal** is het aantal gelezen bytes.
*   **InputEventsLastArrivalTime** wordt bijgewerkt met de in de wachtrij staande tijd van elke ontvangen gebeurtenis.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Gaat de tijd vooruit? Als er feitelijke gebeurtenissen worden gelezen, wordt er mogelijk geen interpunctie gegenereerd.

*   **InputEventsLastPunctuationTime** geeft aan wanneer er interpunctie is gegenereerd om ervoor te zorgen dat de tijd vooruit blijft lopen. Als er geen interpunctie wordt uitgegeven, kan de gegevensstroom worden geblokkeerd.
 
### <a name="are-there-any-errors-in-the-input"></a>Zijn er fouten in de invoer?

*   **InputEventsEventDataNullTotal** is een telling van gebeurtenissen met null-gegevens.
*   **InputEventsSerializerErrorsTotal** is een telling van gebeurtenissen die niet correct kunnen worden gedeserialiseerd.
*   **InputEventsDegradedTotal** is een telling van gebeurtenissen die een ander probleem hadden dan met deserialisatie.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Worden gebeurtenissen geschrapt of aangepast?

*   **InputEventsEarlyTotal** is het aantal gebeurtenissen met een toepassingstijdstempel vóór het hoge watermerk.
*   **InputEventsLateTotal** is het aantal gebeurtenissen met een toepassingstijdstempel na het hoge watermerk.
*   **InputEventsDroppedBeforeApplicationStartTimeTotaal** is het aantal gebeurtenissen dat is gedaald vóór de begintijd van de taak.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Lopen we achter bij het lezen van gegevens?

*   **Input events Backlogged (Total)** geeft aan hoeveel berichten er nog moeten worden gelezen voor Event Hubs en Azure IoT Hub-ingangen. Wanneer dit aantal groter is dan 0, betekent dit dat uw taak de gegevens niet zo snel kan verwerken als het binnenkomt. In dit geval moet u mogelijk het aantal streamingeenheden verhogen en/of ervoor zorgen dat uw taak kan worden parallellopen. Meer informatie hierover vindt u op de [pagina parallelisatie van query's.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization) 


## <a name="get-help"></a>Help opvragen
Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor extra hulp. 

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaginformatie over de queryvan Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Verwijzing naar restAPI-beheer van Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
