---
title: Azure Stream Analytics-query's lokaal opsporen met taakdiagram in Visual Studio
description: In dit artikel wordt beschreven hoe u query's lokaal opsporen met behulp van taakdiagram in Azure Stream Analytics-hulpprogramma's voor Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: 106b1f0b765700803d2cd55b5e049fae5be3dfad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76847197"
---
# <a name="debug-azure-stream-analytics-queries-locally-using-job-diagram-in-visual-studio"></a>Azure Stream Analytics-query's lokaal opsporen met taakdiagram in Visual Studio

Taken die geen resultaat of onverwachte resultaten uitvoeren, zijn veelvoorkomende probleemoplossingsscenario's voor streamingquery's. U het taakdiagram gebruiken terwijl u uw query lokaal test in Visual Studio om de tussentijdse resultaatset en statistieken voor elke stap te onderzoeken. Taakdiagrammen kunnen u helpen de bron van een probleem snel te isoleren wanneer u problemen oplost.

## <a name="debug-a-query-using-job-diagram"></a>Een query debuggen met een taakdiagram

Een Azure Stream Analytics-script wordt gebruikt om invoergegevens om te zetten in uitvoergegevens. Het taakdiagram laat zien hoe gegevens stromen uit invoerbronnen (Gebeurtenishub, IoT Hub, enz.) via meerdere querystappen en, ten slotte, naar uitvoergootstenen. Elke querystap wordt toegewezen aan een tijdelijke resultaatset `WITH` die in het script is gedefinieerd met behulp van een instructie. U de gegevens en statistieken van elke querystap in elke tussenliggende resultatenset bekijken om de bron van een probleem te vinden.

> [!NOTE]
> In dit taakdiagram worden alleen de gegevens en statistieken voor lokale tests in één knooppunt weergegeven. Het mag niet worden gebruikt voor het afstemmen van de prestaties en het oplossen van problemen.

### <a name="start-local-testing"></a>Lokale tests starten

Gebruik deze [Quickstart](stream-analytics-quick-create-vs.md) om te leren hoe u een Stream Analytics-taak maakt met Visual Studio of [een bestaande taak exporteert naar een lokaal project.](stream-analytics-vs-tools.md#export-jobs-to-a-project) Als u de query wilt testen met lokale invoergegevens, volgt u deze [instructies](stream-analytics-live-data-local-testing.md). Als u wilt testen met live-invoer, gaat u naar de volgende stap.

> [!NOTE]
> Als u een taak exporteert naar lokaal project en wilt testen tegen een live invoerstream, moet u de referenties voor alle invoer opnieuw opgeven.  

Kies de invoer- en uitvoerbron in de scripteditor en selecteer **Lokaal uitvoeren**. Het taakdiagram wordt aan de rechterkant weergegeven.

### <a name="view-the-intermediate-result-set"></a>Bekijk de tussenliggende resultaatset  

1. Selecteer de querystap om naar het script te navigeren. U wordt automatisch doorverwezen naar het bijbehorende script in de editor aan de linkerkant.

   ![Het navigerenscript van het taakdiagram](./media/debug-locally-using-job-diagram/navigate-script.png)

2. Selecteer de querystap en selecteer **Voorbeeld** in het dialoogvenster opgedoken. De resultaatset wordt weergegeven op een tabblad in het onderste resultaatvenster.

   ![Voorbeeld van het voorbeeld van het taakdiagram](./media/debug-locally-using-job-diagram/preview-result.png)

### <a name="view-step-metrics"></a>Stapstatistieken weergeven

In deze sectie verkent u de statistieken die beschikbaar zijn voor elk deel van het diagram.

#### <a name="input-sources-live-stream"></a>Invoerbronnen (livestream)

![Taakdiagram live-invoerbronnen](./media/debug-locally-using-job-diagram/live-input.png)

|Gegevens|Beschrijving|
|-|-|
|**TaxiRide TaxiRide**| De naam van de invoer.|
|**Gebeurtenishub** | Type invoerbron.|
|**Gebeurtenissen**|Het aantal gelezen gebeurtenissen.|
|**Backlogged EventBronnen**|Hoeveel berichten moeten nog worden gelezen voor gebeurtenishubs en IoT Hub-ingangen.|
|**Gebeurtenissen in bytes**|Het aantal gelezen bytes.|
| **Gedegradeerde gebeurtenissen**|De telling van gebeurtenissen die een ander probleem hadden dan met deserialisatie.|
|**Vroege gebeurtenissen**| Het aantal gebeurtenissen met een toepassingstijdstempel vóór het hoge watermerk.|
|**Late evenementen**| Het aantal gebeurtenissen met een toepassingstijdstempel na het hoge watermerk.|
|**Gebeurtenisbronnen**| Het aantal gelezen gegevenseenheden. Bijvoorbeeld het aantal blobs.|

#### <a name="input-sources-local-input"></a>Invoerbronnen (lokale invoer)

![Lokale invoerbronnen voor taakdiagram](./media/debug-locally-using-job-diagram/local-input.png)

|Gegevens|Beschrijving|
|-|-|
|**TaxiRide TaxiRide**| De naam van de invoer.|
|**Aantal rijen**| Het aantal rijen dat uit de stap wordt gegenereerd.|
|**Gegevensgrootte**| De grootte van de gegevens die uit deze stap worden gegenereerd.|
|**Lokale invoer**| Gebruik lokale gegevens als invoer.|

#### <a name="query-steps"></a>Querystappen

![Stappen voor queryquery](./media/debug-locally-using-job-diagram/query-step.png)

|Gegevens|Beschrijving|
|-|-|
|**TripData TripData**|De naam van de tijdelijke resultatenset.|
|**Aantal rijen**| Het aantal rijen dat uit de stap wordt gegenereerd.|
|**Gegevensgrootte**| De grootte van de gegevens die uit deze stap worden gegenereerd.|
  
#### <a name="output-sinks-live-output"></a>Uitvoerputten (Live-uitvoer)

![Lokale uitvoersinks van taakdiagram](./media/debug-locally-using-job-diagram/live-output.png)

|Gegevens|Beschrijving|
|-|-|
|**regionaggEH**|De naam van de uitvoer.|
|**Gebeurtenissen**|Het aantal gebeurtenissen dat naar sinks gaat.|

#### <a name="output-sinks-local-output"></a>Uitvoerputten (lokale uitvoer)

![Lokale uitvoersinks van taakdiagram](./media/debug-locally-using-job-diagram/local-output.png)

|Gegevens|Beschrijving|
|-|-|
|**regionaggEH**|De naam van de uitvoer.|
|**Lokale uitvoer**| Resultaatuitvoer naar een lokaal bestand.|
|**Aantal rijen**| Het aantal rijen dat wordt uitgevoerd naar het lokale bestand.|
|**Gegevensgrootte**| De grootte van de gegevensuitvoer naar het lokale bestand.|

### <a name="close-job-diagram"></a>Taakdiagram sluiten

Als u het taakdiagram niet meer nodig hebt, selecteert u **Sluiten** in de rechterbovenhoek. Nadat u het diagramvenster hebt gesloten, moet u opnieuw beginnen met lokale tests om het te kunnen zien.

### <a name="view-job-level-metrics-and-stop-running"></a>Statistieken op taakniveau weergeven en stoppen met uitvoeren

Andere statistieken op taakniveau worden weergegeven in de pop-upconsole. Druk **op Ctrl+C** in de console als u de taak wilt stoppen.

![Taak stoppen met taak](./media/debug-locally-using-job-diagram/stop-job.png)

## <a name="limitations"></a>Beperkingen

* Power BI- en Azure Data Lake Storage Gen1-uitvoersinks worden niet ondersteund vanwege beperkingen van het verificatiemodel.

* Alleen opties voor cloudinvoer hebben [ondersteuning voor tijdbeleid,](stream-analytics-out-of-order-and-late-events.md) terwijl lokale invoeropties dat niet doen.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een functie Stream Analytics maken met Visual Studio](stream-analytics-quick-create-vs.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
* [Live gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio (Preview)](stream-analytics-live-data-local-testing.md)
