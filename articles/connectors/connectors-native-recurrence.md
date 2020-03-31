---
title: Terugkerende taken en werkstromen plannen
description: Terugkerende geautomatiseerde taken en werkstromen plannen en uitvoeren met de recidieftrigger in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: a9c167c5767a4156147e13a1e4ae21162e506474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445858"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Terugkerende taken en werkstromen maken, plannen en uitvoeren met de recidieftrigger in Azure Logic Apps

Als u taken, processen of taken regelmatig wilt uitvoeren volgens een specifieke planning, u uw logische app-werkstroom starten met de ingebouwde **recidief - Trigger plannen.** U een datum en tijd instellen, evenals een tijdzone voor het starten van de werkstroom en een herhaling voor het herhalen van die werkstroom. Als herhalingen om welke reden dan ook worden gemist, blijft deze trigger terugkerend bij het volgende geplande interval. Zie [Terugkerende geautomatiseerde, taken en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor meer informatie over de ingebouwde triggers en acties voor het plannen.

Hier volgen enkele patronen die deze trigger ondersteunt, samen met meer geavanceerde herhalingen en complexe schema's:

* Voer onmiddellijk uit en herhaal elk *n* aantal seconden, minuten, uren, dagen, weken of maanden.

* Begin op een specifieke datum en tijd en voer elk *n-aantal* seconden, minuten, uren, dagen, weken of maanden uit en herhaal deze.

* Ren en herhaal op een of meer keren per dag, bijvoorbeeld om 8:00 en 17:00 uur.

* Ren en herhaal elke week, maar alleen voor specifieke dagen, zoals zaterdag en zondag.

* Ren en herhaal elke week, maar alleen voor specifieke dagen en tijden, zoals maandag tot en met vrijdag om 8:00 en 17:00 uur.

Zie [Terugkerende geautomatiseerde taken, processen en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor verschillen tussen deze trigger en de trigger voor schuifvenster of voor meer informatie over het plannen van terugkerende werkstromen.

> [!TIP]
> Zie [Taken één keer](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)uitvoeren als u uw logische app wilt activeren en slechts één keer wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basiskennis over [logische apps.](../logic-apps/logic-apps-overview.md) Als u nieuw bent in logische apps, leest u [hoe u uw eerste logische app maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-recurrence-trigger"></a>Recidief-trigger toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Een lege, logische app maken.

1. Nadat Logic App Designer is weergegeven, `recurrence` voert u in het zoekvak in als filter. Selecteer deze trigger in de lijst triggers als de eerste stap in de werkstroom van uw logische app: **Herhaling**

   ![De trigger 'Herhaling' selecteren](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Stel het interval en de frequentie voor het terugkeerpatroon in. Stel in dit voorbeeld deze eigenschappen in om uw werkstroom elke week uit te voeren.

   ![Interval en frequentie instellen](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|-----------|----------|------|-------------|
   | **Interval** | `interval` | Ja | Geheel getal | Een positief geheel getal dat beschrijft hoe vaak de werkstroom wordt uitgevoerd op basis van de frequentie. Hier zijn de minimale en maximale intervallen: <p>- Maand: 1-16 maanden </br>- Dag: 1-500 dagen </br>- Uur: 1-12.000 uur </br>- Minuut: 1-72.000 minuten </br>- Tweede: 1-9.999.999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie 'Maand' is, is de herhaling om de 6 maanden. |
   | **Frequentie** | `frequency` | Ja | Tekenreeks | De eenheid van de tijd voor de herhaling: **Tweede**, **Minuut**, **Uur**, **Dag,** **Week,** of **Maand** |
   ||||||

   > [!IMPORTANT]
   > Wanneer herhalingen geen geavanceerde planningsopties opgeven, zijn toekomstige herhalingen gebaseerd op de laatste uitvoeringstijd.
   > De begintijden voor deze herhalingen kunnen afdwalen als gevolg van factoren zoals latentie tijdens opslagoproepen. Gebruik een van de volgende opties om ervoor te zorgen dat uw logica-app geen herhaling mist, vooral wanneer de frequentie binnen dagen of langer is:
   > 
   > * Geef een begintijd op voor de herhaling.
   > 
   > * Geef de uren en minuten op voor wanneer u de herhaling moet uitvoeren met de eigenschappen **Op deze uren** en Bij deze **minuten.**
   > 
   > * Gebruik de [trigger schuifvenster](../connectors/connectors-native-sliding-window.md)in plaats van de recidieftrigger.

1. Als u geavanceerde planningsopties wilt instellen, opent u de lijst **Nieuwe parameter toevoegen.** Alle opties die u selecteert, worden na selectie op de trigger weergegeven.

   ![Geavanceerde planningsopties](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|-----------|----------|------|-------------|
   | **Tijdzone** | `timeZone` | Nee | Tekenreeks | Geldt alleen wanneer u een begintijd opgeeft omdat deze trigger geen [UTC-verschuiving](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Selecteer de tijdzone die u wilt toepassen. |
   | **Begintijd** | `startTime` | Nee | Tekenreeks | Geef een begindatum en -tijd op in deze indeling: <p>YYYY-MM-DDThh:mm:ss als u een tijdzone selecteert <p>-of- <p>YYYY-MM-DDThh:mm:ssZ als u geen tijdzone selecteert <p>Als u bijvoorbeeld 18 september 2017 om 14:00 uur wilt, geeft u '2017-09-18T14:00:00' op en selecteert u een tijdzone zoals Pacific Standard Time. Of geef '2017-09-18T14:00:00Z' op zonder tijdzone. <p>**Let op:** Deze begintijd heeft een maximum van 49 jaar in de toekomst en moet de [ISO 8601-datumtijdspecificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [utc-datumtijdnotatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)volgen, maar zonder [utc-offset](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijdzone selecteert, moet u de letter 'Z' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de gelijkwaardige [nautische tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de begintijd de eerste gebeurtenis, terwijl voor complexe schema's de trigger niet eerder dan de begintijd wordt geactiveerd. [*Op welke manieren kan ik de begindatum en -tijd gebruiken?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Deze dagen** | `weekDays` | Nee | Tekenreeks- of tekenreeksarray | Als u 'Week' selecteert, u een of meer dagen selecteren waarop u de werkstroom wilt uitvoeren: **maandag,** **dinsdag,** **woensdag,** **donderdag,** **vrijdag,** **zaterdag**en **zondag** |
   | **Deze uren** | `hours` | Nee | Gehele of gehele array | Als u 'Dag' of 'Week' selecteert, u een of meer gehele getallen van 0 tot 23 selecteren als de uren van de dag voor wanneer u de werkstroom wilt uitvoeren. <p><p>Als u bijvoorbeeld '10', '12' en '14' opgeeft, krijgt u 10.00 uur, 12.00 en 14.00 uur voor de uren van de dag, maar worden de minuten van de dag berekend op basis van wanneer de herhaling begint. Als u de minuten van de dag wilt instellen, geeft u de waarde op voor de eigenschap **Bij deze minuten.** |
   | **Deze minuten** | `minutes` | Nee | Gehele of gehele array | Als u 'Dag' of 'Week' selecteert, u een of meer gehele getallen van 0 tot 59 selecteren als de minuten van het uur waarop u de werkstroom wilt uitvoeren. <p>U bijvoorbeeld '30' opgeven als het minutenteken en met het vorige voorbeeld voor uren van de dag, krijgt u 10:30, 12:30 uur en 14:30 uur. |
   |||||

   Stel dat het vandaag maandag 4 september 2017 is. De volgende Recidief-trigger wordt niet *eerder* geactiveerd dan de begindatum en -tijd, namelijk maandag 18 september 2017 om 8:00 UUR PST. Het herhalingsschema is echter ingesteld op alleen maandag om 10:30, 12:30 uur en 14:30 uur. Dus de eerste keer dat de trigger wordt geactiveerd en een logische app-werkstroominstantie maakt, is om 10:30 uur. Zie deze [voorbeelden van begintijden](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time)voor meer informatie over hoe begintijden werken.

   Toekomstige runs gebeuren om 12:30 uur en 14:30 uur op dezelfde dag. Elke herhaling maakt zijn eigen werkstroominstantie. Daarna herhaalt het hele schema zich volgende week maandag opnieuw. [*Wat zijn enkele andere voorbeelden?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Voorbeeld van geavanceerde planning](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > De trigger toont alleen een voorbeeld voor uw opgegeven herhaling wanneer u 'Dag' of 'Week' als frequentie selecteert.

1. Bouw nu uw resterende werkstroom samen met andere acties. Zie [Connectors voor Azure Logic Apps voor](../connectors/apis-list.md)meer acties die u toevoegen.

## <a name="workflow-definition---recurrence"></a>Werkstroomdefinitie - Herhaling

In de onderliggende werkstroomdefinitie van uw logica-app, die JSON gebruikt, u de definitie van de [recidieftrigger](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) bekijken met de opties die u hebt gekozen. Als u deze definitie wilt weergeven, kiest u op de werkbalk Code de optie **Codeweergave**. Als u wilt terugkeren naar de ontwerper, kiest u op de werkbalk **designer, Designer**.

In dit voorbeeld ziet u hoe een definitie van een recidieftrigger eruit kan zien in een onderliggende werkstroomdefinitie:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Werkstromen onderbreken met vertragingsacties](../connectors/connectors-native-delay.md)
* [Connectors voor Logic Apps](../connectors/apis-list.md)
