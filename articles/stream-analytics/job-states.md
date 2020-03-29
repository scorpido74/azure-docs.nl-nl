---
title: Azure Stream Analytics-taakstatussen
description: In dit artikel worden de vier verschillende statussen van een Stream Analytics-taak beschreven; rennen, gestopt, gedegradeerd en mislukt.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 454474333cac94dc25deae8196e9ba45bcb3a574
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75359763"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics-taakstatussen

Een Stream Analytics-taak kan op een van de vier statussen op een bepaald moment zijn: uitvoeren, gestopt, gedegradeerd of mislukt. U de status van uw taak vinden op de overzichtspagina van uw Stream Analytics-taak in de Azure-portal. 

| Status | Beschrijving | Aanbevolen acties |
| --- | --- | --- |
| **Wordt uitgevoerd** | Uw taak wordt uitgevoerd op Azure-leesgebeurtenissen die afkomstig zijn van de gedefinieerde invoerbronnen, deze verwerken en de resultaten naar de geconfigureerde uitvoersinks schrijven. | Het is een aanbevolen praktijk om de prestaties van uw taak bij te houden door [belangrijke statistieken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)te controleren. |
| **Gestopt** | Uw taak wordt gestopt en verwerkt geen gebeurtenissen. | N.v.t. | 
| **Verminderd beschikbaar** | Er kunnen met tussenpozen problemen zijn met uw invoer- en uitvoerverbindingen. Deze fouten worden tijdelijke fouten genoemd waardoor uw taak een gedegradeerde status kan invoeren. Stream Analytics zal onmiddellijk proberen om te herstellen van dergelijke fouten en terug te keren naar een status van actief (binnen enkele minuten). Deze fouten kunnen optreden als gevolg van netwerkproblemen, beschikbaarheid van andere Azure-bronnen, deserialisatiefouten enz. De prestaties van uw taak kunnen worden beïnvloed wanneer de taak in verslechterde staat verkeert.| U de [diagnostische of activiteitslogboeken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) bekijken om meer te weten te komen over de oorzaak van deze tijdelijke fouten. In gevallen zoals deserialisatiefouten wordt aanbevolen corrigerende maatregelen te nemen om ervoor te zorgen dat gebeurtenissen niet misvormd zijn. Als de taak de limiet voor het gebruik van resources blijft bereiken, probeert u het SU-nummer te verhogen of [uw taak te paralleleren.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization) In andere gevallen waarin u geen actie ondernemen, probeert Stream Analytics te herstellen naar een *status van actief gebruik.* <br> U [de statistiek vertraging van watermerken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) gebruiken om te begrijpen of deze tijdelijke fouten van invloed zijn op de prestaties van uw taak.|
| **Mislukt** | Er is een kritieke fout opgetreden in uw taak, waardoor een mislukte status is ontstaan. Gebeurtenissen worden niet gelezen en verwerkt. Runtime-fouten zijn een veelvoorkomende oorzaak voor taken die in een mislukte status terechtkomen. | U [waarschuwingen zo configureren](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#set-up-alerts-in-the-azure-portal) dat u een melding krijgt wanneer de taak naar De status Mislukt gaat. <br> <br>U debuggen met behulp van [activiteits- en diagnostische logboeken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs#debugging-using-activity-logs) om de hoofdoorzaak te identificeren en het probleem op te lossen.|

## <a name="next-steps"></a>Volgende stappen
* [Waarschuwingen instellen voor Azure Stream Analytics-taken](stream-analytics-set-up-alerts.md)
* [Statistieken beschikbaar in Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
* [Problemen oplossen met behulp van activiteits- en diagnostische logboeken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)
