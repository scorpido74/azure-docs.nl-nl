---
title: Azure Stream Analytics taakstatussen
description: In dit artikel worden de vier verschillende statussen van een Stream Analytics taak beschreven. uitvoeren, gestopt, gedegradeerd en mislukt.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: fc46b4f24c3c21cda190f80556373991f6c711c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130929"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics taakstatussen

Een Stream Analytics taak kan op elk gewenst moment een van de volgende vier statussen hebben: actief, gestopt, gedegradeerd of mislukt. U kunt de status van uw taak vinden op de overzichts pagina van uw Stream Analytics-taak in de Azure Portal. 

| Status | Beschrijving | Aanbevolen acties |
| --- | --- | --- |
| **Wordt uitgevoerd** | Uw taak wordt uitgevoerd op Azure-Lees gebeurtenissen die afkomstig zijn van de gedefinieerde invoer bronnen, de verwerking ervan en het schrijven van de resultaten naar de geconfigureerde uitvoer-Sinks. | Het is een best practice om de prestaties van uw taken te volgen door de [belangrijkste metrische gegevens](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)te controleren. |
| **Gestopt** | De taak is gestopt en verwerkt geen gebeurtenissen. | N.v.t. | 
| **Verminderd beschikbaar** | Er zijn mogelijk onregelmatige problemen met uw invoer-en uitvoer verbindingen. Deze fouten worden tijdelijke fouten genoemd, waardoor uw taak een gedegradeerde status kan geven. Stream Analytics probeert deze fouten onmiddellijk te herstellen en terug te keren naar een actieve status (binnen enkele minuten). Deze fouten kunnen zich voordoen als gevolg van netwerk problemen, Beschik baarheid van andere Azure-resources, fouten bij deserialisatie, enzovoort. De prestaties van uw taak kunnen van invloed zijn wanneer de taak een gedegradeerde status heeft.| U kunt de [diagnostische of activiteiten logboeken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) bekijken voor meer informatie over de oorzaak van deze tijdelijke fouten. In gevallen zoals het deserialiseren van fouten, is het raadzaam om corrigerende maat regelen te nemen om ervoor te zorgen dat gebeurtenissen niet misvormd zijn. Als de taak de limiet voor het resource gebruik blijft bereiken, kunt u het SU-nummer of de [parallelliseren van uw taak](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)verhogen. In andere gevallen waarin u geen actie kunt ondernemen, probeert Stream Analytics de status wordt *uitgevoerd* te herstellen. <br> U kunt de metrische gegevens van [watermerk vertraging](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) gebruiken om te begrijpen of deze tijdelijke fouten van invloed zijn op de prestaties van uw taak.|
| **Mislukt** | Er is een kritieke fout opgetreden in uw taak, wat resulteert in een mislukte status. Gebeurtenissen worden niet gelezen en verwerkt. Runtime-fouten zijn een veelvoorkomende oorzaak voor taken die eindigen op een mislukte status. | U kunt [waarschuwingen configureren](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) zodat u wordt gewaarschuwd wanneer de status van de taak wordt mislukt. <br> <br>U kunt fouten opsporen met behulp van [activiteiten en bron logboeken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) om de hoofd oorzaak te bepalen en het probleem op te lossen.|

## <a name="next-steps"></a>Volgende stappen
* [Installatie waarschuwingen voor Azure Stream Analytics taken](stream-analytics-set-up-alerts.md)
* [Metrische gegevens die beschikbaar zijn in Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Problemen oplossen met activiteiten-en resource logboeken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
