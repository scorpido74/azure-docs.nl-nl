---
title: Problemen met de invoer voor Azure Stream Analytics oplossen
description: In dit artikel worden technieken beschreven om problemen op te lossen met uw invoerverbindingen in Azure Stream Analytics-taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409417"
---
# <a name="troubleshoot-input-connections"></a>Problemen met invoerverbindingen oplossen

In dit artikel worden veelvoorkomende problemen beschreven met Azure Stream Analytics-invoerverbindingen, hoe u invoerproblemen oplossen en hoe u de problemen verhelpen. Voor veel stappen voor het oplossen van problemen moeten diagnostische logboeken worden ingeschakeld voor uw Stream Analytics-taak. Zie [Azure Stream Analytics oplossen met behulp van diagnostische logboeken](stream-analytics-job-diagnostic-logs.md)als u geen diagnostische logboeken hebt ingeschakeld.

## <a name="input-events-not-received-by-job"></a>Invoergebeurtenissen die niet door taak zijn ontvangen 

1.  Test uw invoer- en uitvoerconnectiviteit. Controleer de connectiviteit met ingangen en uitgangen met behulp van de knop **Verbinding testen** voor elke invoer en uitvoer.

2.  Bekijk uw invoergegevens.

    1. Gebruik de knop [**Voorbeeldgegevens**](stream-analytics-sample-data-input.md) voor elke invoer. Download de invoervoorbeeldgegevens.
        
    1. Controleer de voorbeeldgegevens om inzicht te krijgen in het schema en de [gegevenstypen](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Controleer [de statistieken van Event Hub](../event-hubs/event-hubs-metrics-azure-monitor.md) om te controleren of gebeurtenissen worden verzonden. Berichtstatistieken moeten groter zijn dan nul als gebeurtenishubs berichten ontvangen.

3.  Zorg ervoor dat u een tijdsbereik hebt geselecteerd in het invoervoorbeeld. Kies **Tijdbereik selecteren**en voer een voorbeeldduur in voordat u uw query test.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Ongeldige invoergebeurtenissen veroorzaken deserialisatiefouten 

Deserialisatieproblemen worden veroorzaakt wanneer de invoerstream van uw Stream Analytics-taak misvormde berichten bevat. Een verkeerd gevormd bericht kan bijvoorbeeld worden veroorzaakt door een ontbrekend haakje of brace in een JSON-object of een onjuiste tijdstempelnotatie in het tijdveld. 
 
Wanneer een Stream Analytics-taak een verkeerd ontvangenbericht van een invoer ontvangt, wordt het bericht verwijderd en wordt u gewaarschuwd met een waarschuwing. Er wordt een waarschuwingssymbool weergegeven op de tegel **Invoer** van uw streamanalytics-taak. Het volgende waarschuwingssymbool bestaat zolang de taak in de huidige staat is:

![Tegel Azure Stream Analytics-invoer](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Schakel diagnostische logboeken in om de details van de fout en het bericht (payload) te bekijken dat de fout heeft veroorzaakt. Er zijn meerdere redenen waarom deserialisatiefouten kunnen optreden. Zie Fouten in de [invoergegevens voor](data-errors.md#input-data-errors)meer informatie over specifieke deserialisatiefouten. Als diagnostische logboeken niet zijn ingeschakeld, is er een korte melding beschikbaar in de Azure-portal.

![Waarschuwing voor invoergegevens](media/stream-analytics-malformed-events/warning-message-with-offset.png)

In gevallen waarin de payload van het bericht groter is dan 32 KB of in binaire indeling is, voert u de CheckMalformedEvents.cs code uit die beschikbaar is in de [GitHub-samplesrepository.](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH) Deze code leest de partitie-id, ververschuiving en drukt de gegevens af die zich in die verschuiving bevinden. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Taak overschrijdt maximale Event Hub-ontvangers

Een aanbevolen manier voor het gebruik van Event Hubs is het gebruik van meerdere consumentengroepen voor taakschaalbaarheid. Het aantal lezers in de taak Stream Analytics voor een specifieke invoer is van invloed op het aantal lezers in één consumentengroep. Het precieze aantal ontvangers is gebaseerd op interne implementatiedetails voor de scale-out topologielogica en wordt niet extern belicht. Het aantal lezers kan wijzigen wanneer een taak wordt gestart of tijdens taakupgrades.

De fout wordt weergegeven wanneer het aantal ontvangers dat het maximum overschrijdt de volgende waarde heeft: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Wanneer het aantal lezers verandert tijdens een taakupgrade, worden tijdelijke waarschuwingen geschreven om logboeken te controleren. Stream Analytics-taken herstellen automatisch van deze tijdelijke problemen.

### <a name="add-a-consumer-group-in-event-hubs"></a>Een consumentengroep toevoegen in gebeurtenishubs

Voer de volgende stappen uit om een nieuwe consumentengroep toe te voegen aan uw instantie Event Hubs:

1. Meld u aan bij Azure Portal.

2. Zoek je eventhub.

3. Selecteer **Gebeurtenishubs** onder de kop **Entiteiten.**

4. Selecteer de gebeurtenishub op naam.

5. Selecteer op de pagina **Instantie van gebeurtenishubs** onder de kop **Entiteiten** de optie **Consumentengroepen**. Een consumentengroep met naam **$Default** wordt vermeld.

6. Selecteer **+ Consumentengroep** om een nieuwe consumentengroep toe te voegen. 

   ![Een consumentengroep toevoegen in gebeurtenishubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Wanneer u de invoer in de taak Stream Analytics hebt gemaakt om naar de gebeurtenishub te wijzen, hebt u daar de consumentengroep opgegeven. **$Default** wordt gebruikt wanneer geen is opgegeven. Zodra u een nieuwe consumentengroep hebt gemaakt, bewerkt u de input van de gebeurtenishub in de taak Stream Analytics en geeft u de naam van de nieuwe consumentengroep op.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Lezers per partitie overschrijdt de limiet voor gebeurtenishubs

Als de syntaxis van uw streamingquery meerdere keren naar dezelfde bron van gebeurtenishub verwijst, kan de taakengine meerdere lezers per query uit dezelfde consumentengroep gebruiken. Wanneer er te veel verwijzingen naar dezelfde consumentengroep zijn, kan de taak de limiet van vijf overschrijden en een fout worden gegenereerd. In die omstandigheden u verder delen door meerdere ingangen te gebruiken voor meerdere consumentengroepen met behulp van de oplossing die in de volgende sectie wordt beschreven. 

Scenario's waarin het aantal lezers per partitie de limiet van gebeurtenishubs van vijf overschrijdt, zijn de volgende:

* Meerdere SELECT-instructies: Als u meerdere SELECT-instructies gebruikt die verwijzen naar **dezelfde** gebeurtenishub-invoer, zorgt elke SELECT-instructie ervoor dat er een nieuwe ontvanger wordt gemaakt.

* UNION: Wanneer u een EU gebruikt, is het mogelijk om meerdere ingangen te hebben die verwijzen naar **dezelfde** gebeurtenishub en consumentengroep.

* SELF JOIN: Wanneer u een SELF JOIN-bewerking gebruikt, is het mogelijk om meerdere keren naar **dezelfde** gebeurtenishub te verwijzen.

De volgende aanbevolen procedures kunnen helpen scenario's te beperken waarin het aantal lezers per partitie de limiet van vijf gebeurtenishubs overschrijdt.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Uw query splitsen in meerdere stappen met behulp van een MET-component

De WITH-component specificeert een tijdelijke benoemde resultaatset waarnaar kan worden verwezen door een FROM-component in de query. U definieert de WITH-component in de uitvoeringsscope van één SELECT-instructie.

Bijvoorbeeld in plaats van deze query:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Gebruik deze query:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Ervoor zorgen dat inputs zich binden aan verschillende consumentengroepen

Voor query's waarin drie of meer ingangen zijn verbonden met dezelfde consumentengroep Event Hubs, maakt u afzonderlijke consumentengroepen. Dit vereist het maken van extra Stream Analytics-ingangen.

## <a name="get-help"></a>Help opvragen

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
