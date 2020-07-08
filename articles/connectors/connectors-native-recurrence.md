---
title: Terugkerende taken en werk stromen plannen
description: Terugkerende geautomatiseerde taken en werk stromen plannen en uitvoeren met de trigger voor terugkeer patroon in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 18a58815ccd7bd229b6c1a27c92e903f22c8fd55
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322579"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Terugkerende taken en werk stromen maken, plannen en uitvoeren met de trigger voor terugkeer patroon in Azure Logic Apps

Als u taken, processen of taken regel matig op een specifiek schema wilt uitvoeren, kunt u de werk stroom van uw logische app starten met de ingebouwde trigger **schema voor terugkeer patroon** . U kunt een datum en tijd instellen, evenals een tijd zone voor het starten van de werk stroom en een terugkeer patroon voor het herhalen van die werk stroom. Als herhalingen om een of andere reden worden gemist, bijvoorbeeld door onderbrekingen of uitgeschakelde werk stromen, worden de gemiste terugkeer patronen niet door deze trigger verwerkt, maar worden tijdens het volgende geplande interval herhalingen opnieuw gestart. Zie voor meer informatie over de ingebouwde plannings triggers en-acties [planning en uitvoeren van terugkerende automatische, taken en werk stromen met Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Hier volgen enkele patronen die deze trigger ondersteunt samen met meer geavanceerde terugkeer patronen en complexe schema's:

* Voer onmiddellijk uit en herhaal elke *n* seconden, minuten, uren, dagen, weken of maanden.

* Begin op een specifieke datum en tijd en voer vervolgens elke *n* seconden, minuten, uren, dagen, weken of maanden uit en herhaal deze.

* Voer een of meer keer per dag uit en herhaal deze, bijvoorbeeld om 8:00 uur en 5:00 uur.

* Elke week uitvoeren en herhalen, maar alleen voor specifieke dagen, zoals zaterdag en zondag.

* Voer elke week uit en herhaal deze, maar alleen voor specifieke dagen en tijden, zoals maandag tot en met vrijdag om 8:00 uur en 5:00 uur.

Zie [terugkerende geautomatiseerde taken, processen en werk stromen plannen en uitvoeren met Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor verschillen tussen deze trigger en de activering van het schuif venster of voor meer informatie over het plannen van terugkerende werk stromen.

> [!TIP]
> Als u uw logische app wilt activeren en slechts één keer in de toekomst wilt uitvoeren, raadpleegt u [eenmalige taken uitvoeren](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Basis kennis over [Logic apps](../logic-apps/logic-apps-overview.md). Als u geen ervaring hebt met Logic apps, kunt u leren [hoe u uw eerste logische app maakt](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Trigger voor terugkeer patroon toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Een lege, logische app maken.

1. Wanneer Logic app Designer wordt weer gegeven, voert u in het zoekvak in `recurrence` als uw filter. Selecteer in de lijst triggers deze trigger als de eerste stap in de werk stroom van de logische app: **terugkeer patroon**

   ![Trigger voor terugkeer patroon selecteren](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Stel het interval en de frequentie voor het terugkeerpatroon in. In dit voor beeld stelt u deze eigenschappen in om elke week uw werk stroom uit te voeren.

   ![Interval en frequentie instellen](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Eigenschap | JSON-naam | Vereist | Type | Description |
   |----------|-----------|----------|------|-------------|
   | **Interval** | `interval` | Yes | Geheel getal | Een positief geheel getal dat aangeeft hoe vaak de werk stroom wordt uitgevoerd op basis van de frequentie. Dit zijn de minimale en maximale intervallen: <p>-Maand: 1-16 maanden <br>-Week: 1-71 weken <br>-Dag: 1-500 dagen <br>-Uur: 1-12000 uur <br>-Minuut: 1-72000 minuten <br>-Seconde: 1-9999999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie ' month ' is, is het terugkeer patroon elke 6 maanden. |
   | **Frequentie** | `frequency` | Ja | Tekenreeks | De tijds eenheid voor het terugkeer patroon: **tweede**, **minuut**, **uur**, **dag**, **week**of **maand** |
   ||||||

   > [!IMPORTANT]
   > Wanneer herhalingen geen geavanceerde plannings opties opgeven, worden toekomstige terugkeer patronen gebaseerd op de laatste uitvoerings tijd.
   > De begin tijden voor deze terugkeer patronen kunnen ontstaan door factoren als latentie tijdens opslag aanroepen. Gebruik een van de volgende opties om ervoor te zorgen dat uw logische app geen herhaling mist, met name wanneer de frequentie in dagen of langer is.
   > 
   > * Geef een begin tijd op voor het terugkeer patroon.
   > 
   > * Geef op na hoeveel uur en minuten het terugkeer patroon moet worden uitgevoerd met behulp van de eigenschappen **voor deze uren** en met **deze minuten** .
   > 
   > * Gebruik de [verschuivings venster trigger](../connectors/connectors-native-sliding-window.md)in plaats van de terugkeer patroon trigger.

1. Als u geavanceerde plannings opties wilt instellen, opent u de lijst **nieuwe para meter toevoegen** . De opties die u selecteert, worden weer gegeven op de trigger na selectie.

   ![Geavanceerde plannings opties](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Eigenschap | JSON-naam | Vereist | Type | Description |
   |----------|-----------|----------|------|-------------|
   | **Tijdzone** | `timeZone` | No | Tekenreeks | Is alleen van toepassing wanneer u een start tijd opgeeft, omdat deze trigger geen [UTC-offset](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Selecteer de tijd zone die u wilt Toep assen. |
   | **Begin tijd** | `startTime` | No | Tekenreeks | Geef een begin datum en-tijd op in de volgende indeling: <p>JJJJ-MM-DDTuu: mm: SS als u een tijd zone selecteert <p>-of- <p>JJJJ-MM-DDTuu: mm: ssZ als u geen tijd zone selecteert <p>Als u bijvoorbeeld 18 september 2020 om 2:00 uur wilt, geeft u "2020-09-18T14:00:00" op en selecteert u een tijd zone zoals Pacific (standaard tijd). U kunt ook ' 2020-09-18T14:00:00Z ' opgeven zonder tijd zone. <p>**Opmerking:** Deze begin tijd heeft een maximum van 49 jaar in de toekomst en moet voldoen aan de [ISO 8601 date time-specificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [UTC datum tijd notatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), maar zonder een [UTC-afwijking](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijd zone selecteert, moet u de letter ' Z ' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de equivalente [zeemijl tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de start tijd het eerste voorval, terwijl voor complexe schema's de trigger niet eerder dan de begin tijd wordt geactiveerd. [*Wat zijn de manieren waarop ik de begin datum en-tijd kan gebruiken?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **Deze dagen** | `weekDays` | No | Teken reeks of teken reeks matrix | Als u week selecteert, kunt u een of meer dagen selecteren wanneer u de werk stroom wilt uitvoeren: **maandag**, **dinsdag**, **woensdag**, **donderdag**, **vrijdag**, **zaterdag**en **zondag** |
   | **Deze uren** | `hours` | No | Geheel getal of gehele matrix | Als u dag of week selecteert, kunt u een of meer gehele getallen van 0 tot en met 23 selecteren als de uren van de dag waarop u de werk stroom wilt uitvoeren. <p><p>Als u bijvoorbeeld "10", "12" en "14" opgeeft, krijgt u 10 uur, 12 uur en 2 uur voor de uren van de dag, maar wordt het aantal minuten van de dag berekend op basis van het moment waarop het terugkeer patroon start. Als u specifieke minuten van de dag wilt instellen, bijvoorbeeld 10:00 AM, 12:00 PM en 2:00 uur, geeft u deze waarden op met behulp van de eigenschap **in deze minuten** . |
   | **Deze minuten** | `minutes` | No | Geheel getal of gehele matrix | Als u dag of week selecteert, kunt u een of meer gehele getallen van 0 tot en met 59 selecteren als de minuten van het uur wanneer u de werk stroom wilt uitvoeren. <p>U kunt bijvoorbeeld "30" opgeven als het minuut merk en het vorige voor beeld gebruiken voor uren van de dag, u krijgt 10:30 uur, 12:30 uur en 2:30 uur. |
   |||||

   Stel bijvoorbeeld dat vandaag vrijdag 4 september 2020. De volgende trigger voor terugkeer patroon wordt niet *eerder* geactiveerd dan de start datum en-tijd, die vrijdag 18 september 2020 om 8:00 uur PST. De terugkeer planning is echter alleen ingesteld voor 10:30 uur, 12:30 uur en 2:30 uur op elke maandag. De eerste keer dat de trigger wordt geactiveerd en een werk stroom exemplaar van een logische app maakt, bevindt zich op maandag om 10:30 uur. Voor meer informatie over hoe start tijden werken, raadpleegt u deze [voor beelden voor de start tijd](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Toekomstige uitvoeringen gebeuren op dezelfde dag om 12:30 uur en 2:30 PM. Elk terugkeer patroon maakt een eigen werk stroom exemplaar. Daarna herhaalt de volledige planning de volgende maandag opnieuw. [*Wat zijn enkele andere voor beelden van gevallen?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Voor beeld van geavanceerde planning](./media/connectors-native-recurrence/recurrence-trigger-advanced-schedule-options.png)

   > [!NOTE]
   > De trigger toont een preview voor uw opgegeven terugkeer patroon alleen wanneer u "dag" of "week" als frequentie selecteert.

1. Bouw nu uw resterende werk stroom met andere acties. Zie [Connect oren voor Azure Logic apps](../connectors/apis-list.md)voor meer acties die u kunt toevoegen.

## <a name="workflow-definition---recurrence"></a>Werk stroom definitie-terugkeer patroon

In de definitie van de onderliggende werk stroom van uw logische app, die gebruikmaakt van JSON, kunt u de definitie van de [terugkeer patroon](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) weer geven met de opties die u hebt gekozen. Als u deze definitie wilt bekijken, kiest u in de werk balk ontwerpen de optie **code weergave**. Als u wilt terugkeren naar de ontwerp functie, kiest u op de werk balk ontwerpen en **Designer**.

In dit voor beeld ziet u hoe een definitie van een terugkeer patroon kan worden weer gegeven in een onderliggende werk stroom definitie:

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
         "startTime": "2020-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Werk stromen onderbreken met vertragings acties](../connectors/connectors-native-delay.md)
* [Connectors voor Logic Apps](../connectors/apis-list.md)
