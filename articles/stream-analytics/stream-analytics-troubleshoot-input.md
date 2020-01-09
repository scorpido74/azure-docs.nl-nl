---
title: Invoer voor probleemoplossing voor Azure Stream Analytics
description: Dit artikel wordt beschreven technieken voor het oplossen van uw invoer verbindingen in Azure Stream Analytics-taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 20a161ffc82cb8f74cfcac838856434f83c4e258
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354286"
---
# <a name="troubleshoot-input-connections"></a>Problemen met invoerverbindingen oplossen

Deze pagina worden veelvoorkomende problemen beschreven met invoer verbindingen en het oplossen van deze.

## <a name="input-events-not-received-by-job"></a>Invoergebeurtenissen niet is ontvangen door de taak 
1.  Test de verbinding. Controleer de verbinding met de invoer en uitvoer met behulp van de **testverbinding** knop voor elke invoer en uitvoer.

2.  Controleer de ingevoerde gegevens.

    Gebruik om te controleren dat de invoergegevens worden doorgestuurd naar Event Hub, [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) verbinding maken met Azure Event Hub (indien Event Hub-invoer wordt gebruikt).
        
    Gebruik de [ **voorbeeldgegevens** ](stream-analytics-sample-data-input.md) knop voor elke invoer en download de voorbeeldinvoergegevens.
        
    Bekijk de voorbeeldgegevens voor inzicht in de vorm van de gegevens: het schema en de [gegevenstypen](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Ongeldige invoergebeurtenissen veroorzaken deserialisatiefouten 
Deserialisatie problemen worden veroorzaakt wanneer de invoerstroom van uw Stream Analytics-taak onjuiste berichten bevat. Bijvoorbeeld, een onjuist ingedeeld bericht wordt mogelijk veroorzaakt door een ontbrekend haakje of een accolade in een JSON-object, of heeft een onjuiste tijdstempel-indeling in het tijdveld. 
 
Wanneer een Stream Analytics-taak ontvangt een onjuist ingedeeld bericht uit de invoer, wordt het bericht verwijderd en ontvangt u een melding met een waarschuwing. Er wordt een waarschuwingssymbool weergegeven op de **invoer** tegel van uw Stream Analytics-taak. Deze waarschuwing aanmelding bestaat, zolang de taak wordt uitgevoerd:

![Azure Stream Analytics-invoer tegel](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Schakel de diagnostische logboeken om de details van de waarschuwing weer te geven. Voor onjuist gevormde invoergebeurtenissen bevatten de logboeken van de uitvoering van een item met het bericht dat lijkt op: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Over de oorzaak van de deserialisatiefout
U kunt de volgende stappen voor het analyseren van de invoer gebeurtenissen in detail om op te halen van een duidelijk wat de oorzaak van de deserialisatiefout kan duren. Vervolgens kunt u de bron van de gebeurtenis voor het genereren van gebeurtenissen in de juiste indeling om te voorkomen dat u opnieuw te maken met dit probleem oplossen.

1. Navigeer naar de tegel invoer en klik op de waarschuwing symbolen voor een overzicht van problemen.

2. De tegel invoer details geeft een lijst van waarschuwingen met informatie over elk probleem. De waarschuwing voorbeeld bevat de partitie, offset en volgnummers waarbij er ongeldige JSON-gegevens. 

   ![Stream Analytics waarschuwings bericht met offset](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Als u wilt zoeken in de JSON-gegevens met de juiste indeling, voert u de CheckMalformedEvents.cs code die beschikbaar zijn in de [opslagplaats met GitHub-voorbeelden](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Deze code leest de partitie-ID, verschuiving en de gegevens die zich in deze offset af te drukken. 

4. Zodra de gegevens zijn gelezen, kunt u de serialisatie-indeling analyseren en corrigeren.

5. U kunt ook [gebeurtenissen worden gelezen vanuit een IoT-Hub met de Service Bus Explorer](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Taak overschrijdt de maximale Event Hub-ontvangers
Een aanbevolen procedure voor het gebruik van Event Hubs is het gebruik van meerdere consumentengroepen om te controleren of de schaalbaarheid van de taak. Het aantal lezers in de Stream Analytics-taak voor een specifieke invoer is van invloed op het aantal lezers in een enkele consumergroep. Het exacte aantal ontvangers is gebaseerd op interne implementatiegegevens voor de scale-out-topologie-logica en niet extern wordt weergegeven. Het aantal lezers kunt wijzigen wanneer een taak wordt gestart of tijdens upgrades van de taak.

De fout wordt weergegeven wanneer het aantal ontvangers dat het maximum overschrijdt de volgende waarde heeft: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Als het aantal lezers worden gewijzigd tijdens de upgrade van een taak, worden tijdelijke waarschuwingen om te controleren, Logboeken geschreven. Stream Analytics-taken worden automatisch hersteld vanuit deze tijdelijke problemen.

### <a name="add-a-consumer-group-in-event-hubs"></a>Toevoegen van een consumentengroep in Event Hubs
Om toe te voegen een nieuwe consumentengroep in uw exemplaar van de Event Hubs, de volgende stappen uit:

1. Meld u aan bij Azure Portal.

2. Ga naar uw Eventhubs.

3. Selecteer **Event Hubs** onder de **entiteiten** kop.

4. Selecteer de Event Hub met de naam.

5. Op de **Event Hubs-instantie** pagina onder de **entiteiten** kop, selecteer **consumentengroepen**. Een consumentengroep met naam **$Default** wordt vermeld.

6. Selecteer **+ Consumergroep** om toe te voegen een nieuwe consumergroep. 

   ![Toevoegen van een consumentengroep in Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Wanneer u de invoer in de Stream Analytics-taak om te verwijzen naar de Event Hub hebt gemaakt, kunt u er de consumergroep opgegeven. $Default wordt gebruikt wanneer niets is opgegeven. Als u een nieuwe consumergroep maakt, de Event Hub-invoer in de Stream Analytics-taak bewerken en geef de naam van de nieuwe consumentengroep.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Lezers per partitie overschrijdt de limiet van Event Hubs

Als uw streaming-querysyntaxis verwijst meerdere keren naar dezelfde invoer Event Hub-resource, kan de engine taak meerdere lezers per query van die consumentengroep kunt gebruiken. Als er te veel verwijzingen naar de dezelfde consumergroep, de taak langer zijn dan de limiet van vijf en er een fout gegenereerd. In deze gevallen kunt u verder verdelen met behulp van meerdere invoergegevens uit meerdere consumentengroepen met behulp van de oplossing in de volgende sectie beschreven. 

Scenario's waarin het aantal lezers per partitie is groter dan de Event Hubs-limiet van vijf omvatten het volgende:

* Meerdere SELECT-instructies: als u meerdere SELECT-instructies die naar verwijzen **dezelfde** event hub-invoer, elke SELECT-instructie zorgt ervoor dat een nieuwe ontvanger moet worden gemaakt.
* UNION: Wanneer u een SAMENVOEGING gebruikt, is het mogelijk dat meerdere invoergegevens die naar verwijzen de **dezelfde** event hub- en consumer-groep.
* SELF-join: Wanneer u een SELF JOIN-bewerking gebruikt, is het mogelijk om te verwijzen naar de **dezelfde** gebeurtenishub meerdere keren.

De volgende procedures kunnen u scenario's waarin het aantal lezers per partitie is groter dan de Event Hubs-limiet van vijf.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Uw query splitsen in meerdere stappen met behulp van een WITH-component

De WITH-clausule Hiermee geeft u een tijdelijke naam resultatenset die kan worden verwezen door een FROM-component in de query. U definieert de WITH-clausule in het bereik van de uitvoering van één SELECT-instructie.

Bijvoorbeeld, in plaats van deze query:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Zorg ervoor dat de invoer aan verschillende consumentengroepen binden

Voor query's waarin drie of meer invoerwaarden zijn verbonden met de dezelfde consumergroep van Event Hubs, kunt u afzonderlijke consumergroepen maken. Dit is vereist voor het maken van aanvullende invoer van de Stream Analytics.

## <a name="get-help"></a>Hulp krijgen

Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)
