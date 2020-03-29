---
title: Bewakingswaarschuwingen instellen voor Azure Stream Analytics-taken
description: In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om bewaking en waarschuwingen voor Azure Stream Analytics-taken in te stellen.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 836b7a489e3c73d745b128cbbc0c3566220ac409
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458731"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Waarschuwingen instellen voor Azure Stream Analytics-taken

Het is belangrijk om uw Azure Stream Analytics-taak te controleren om ervoor te zorgen dat de taak probleemloos wordt uitgevoerd. In dit artikel wordt beschreven hoe u waarschuwingen instelt voor veelvoorkomende scenario's die moeten worden gecontroleerd. 

U regels voor statistieken definiëren via de portal via de portal en [programmatisch.](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a)

## <a name="set-up-alerts-in-the-azure-portal"></a>Waarschuwingen instellen in de Azure-portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Ontvang een waarschuwing wanneer een taak onverwacht stopt

In het volgende voorbeeld wordt uitgelegd hoe u waarschuwingen instelt voor wanneer uw taak een mislukte status invoert. Deze waarschuwing wordt aanbevolen voor alle taken.

1. Open in de Azure-portal de streamanalytics-taak waarvoor u een waarschuwing wilt maken.

2. Navigeer **op de** pagina Taak naar de sectie **Controle.**  

3. Selecteer **Statistieken**en vervolgens **Nieuwe waarschuwingsregel**.

   ![Azure portal Stream Analytics-waarschuwingen instellen](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. De functienaam van Stream Analytics moet automatisch worden weergegeven onder **RESOURCE**. Klik **op Voorwaarde toevoegen**en selecteer Alle **beheerbewerkingen** onder **Signaallogica configureren**.

   ![Signaalnaam selecteren voor Stream Analytics-waarschuwing](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Wijzig **onder Signaallogica configureren** **gebeurtenisniveau** in **Alles** en **wijzigde de status** in **Mislukt**. Gebeurtenis **verlaten geïnitieerd door** leeg en selecteer **Gereed**.

   ![Signaallogica configureren voor Stream Analytics-waarschuwing](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Selecteer een bestaande actiegroep of maak een nieuwe groep. In dit voorbeeld is een nieuwe actiegroep genaamd **TIDashboardGroupActions** gemaakt met een **e-mailactie** die een e-mail verzendt naar gebruikers met de **rol Eigenaar** Azure Resource Manager.

   ![Een waarschuwing instellen voor een Azure Streaming Analytics-taak](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. De **RESOURCE,** **VOORWAARDE**en **ACTIEGROEPEN** moeten elk een vermelding hebben. Houd er rekening mee dat om de waarschuwingen te kunnen afvuren, aan de gestelde voorwaarden moet worden voldaan. U kunt bijvoorbeeld het gemiddelde meten van een metrische waarde over de afgelopen 15 minuten, op basis van metingen om de 5 minuten.

   ![Waarschuwingregel Stream Analytics maken](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Voeg een naam van de **waarschuwingsregel**, **Beschrijving**en uw **resourcegroep** toe aan de **waarschuwingsgegevens** en klik op **Waarschuwingsregel maken** om de regel voor uw streamanalytics-taak te maken.

   ![Waarschuwingregel Stream Analytics maken](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Scenario's om te controleren

De volgende waarschuwingen worden aanbevolen voor het bewaken van de prestaties van uw Stream Analytics-taak. Deze statistieken moeten elke minuut worden geëvalueerd gedurende de laatste periode van 5 minuten.

|Gegevens|Voorwaarde|Tijdsaggregatie|Drempelwaarde|Corrigerende maatregelen|
|-|-|-|-|-|
|SU% Gebruik|Groter dan|Maximum|80|Er zijn meerdere factoren die het gebruik van SU% verhogen. U schalen met queryparallelisatie of het aantal streaming-eenheden verhogen. Zie [Query-parallellisatie gebruiken in Azure Stream Analytics](stream-analytics-parallelization.md) voor meer informatie.|
|Runtime-fouten|Groter dan|Totaal|0|Bestudeer de activiteits- of diagnostische logboeken en breng de juiste wijzigingen aan in de invoer, query of uitvoer.|
|Watermerk vertraging|Groter dan|Maximum|Wanneer de gemiddelde waarde van deze statistiek in de afgelopen 15 minuten groter is dan de tolerantie voor late aankomst (in seconden). Als u de tolerantie voor late aankomst niet hebt gewijzigd, is de standaardinstelling ingesteld op 5 seconden.|Probeer het aantal SU's te verhogen of uw query te parallellopen. Zie [Streamingeenheden begrijpen en aanpassen voor](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job)meer informatie over SU's. Zie Parallellen van [query's gebruiken in Azure Stream Analytics](stream-analytics-parallelization.md)voor meer informatie over het parallellopen van uw query.|
|Fouten in dedeserialisatie van invoer|Groter dan|Totaal|0|Bestudeer de activiteits- of diagnostische logboeken en breng de juiste wijzigingen aan in de invoer. Zie Azure Stream Analytics [oplossen met diagnostische logboeken voor](stream-analytics-job-diagnostic-logs.md) meer informatie over diagnostische logboeken|

## <a name="get-help"></a>Help opvragen

Zie [Waarschuwingsmeldingen ontvangen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)voor meer informatie over het configureren van waarschuwingen in de Azure-portal.  

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-get-started.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

