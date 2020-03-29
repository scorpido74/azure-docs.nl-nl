---
title: Taken plannen om aaneengesloten gegevens te verwerken
description: Terugkerende taken maken en uitvoeren die aaneengesloten gegevens verwerken met schuifvensters in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74786908"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>Taken voor aaneengesloten gegevens plannen en uitvoeren met de trigger schuifvenster in Azure Logic Apps

Als u taken, processen of taken die gegevens in aaneengesloten segmenten moeten verwerken, regelmatig wilt uitvoeren, u de werkstroom van uw logische app starten met de trigger **voor schuifvenster.** U een datum en tijd instellen, evenals een tijdzone voor het starten van de werkstroom en een herhaling voor het herhalen van die werkstroom. Als herhalingen worden gemist om welke reden dan ook, deze trigger verwerkt die gemiste herhalingen. Gebruik bijvoorbeeld bij het synchroniseren van gegevens tussen uw database en back-upopslag de trigger schuifvenster, zodat de gegevens worden gesynchroniseerd zonder dat er hiaten ontstaan. Zie [Terugkerende geautomatiseerde, taken en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor meer informatie over de ingebouwde triggers en acties voor het plannen.

Hier volgen enkele patronen die deze trigger ondersteunt:

* Loop onmiddellijk en herhaal elk *n* aantal seconden, minuten of uren.

* Begin op een specifieke datum en tijd en voer vervolgens elk *n-aantal* seconden, minuten of uren uit en herhaal deze. Met deze trigger u een begintijd in het verleden opgeven, die alle herhalingen in het verleden uitvoert.

* Stel elke herhaling voor een bepaalde duur uit voordat u wordt uitgevoerd.

Zie [Terugkerende geautomatiseerde taken, processen en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor verschillen tussen deze trigger en de recidieftrigger of voor meer informatie over het plannen van terugkerende werkstromen.

> [!TIP]
> Zie [Taken één keer](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)uitvoeren als u uw logische app wilt activeren en slechts één keer wilt uitvoeren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, u [zich aanmelden voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Basiskennis over [logische apps.](../logic-apps/logic-apps-overview.md) Als u nieuw bent in logische apps, leest u [hoe u uw eerste logische app maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="add-sliding-window-trigger"></a>Trigger voor schuifvenster toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Een lege, logische app maken.

1. Nadat Logic App Designer is weergegeven, voert u in het zoekvak 'schuifvenster' in als filter. Selecteer deze trigger in de lijst triggers als de eerste stap in de werkstroom van uw logische app: **Schuifvenster**

   ![De trigger 'Schuifvenster' selecteren](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Stel het interval en de frequentie voor het terugkeerpatroon in. Stel in dit voorbeeld deze eigenschappen in om uw werkstroom elke week uit te voeren.

   ![Interval en frequentie instellen](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Eigenschap | Vereist | JSON-naam | Type | Beschrijving |
   |----------|----------|-----------|------|-------------|
   | **Interval** | Ja | interval | Geheel getal | Een positief geheel getal dat beschrijft hoe vaak de werkstroom wordt uitgevoerd op basis van de frequentie. Hier zijn de minimale en maximale intervallen: <p>- Uur: 1-12.000 uur </br>- Minuut: 1-72.000 minuten </br>- Tweede: 1-9.999.999 seconden<p>Als het interval bijvoorbeeld 6 is en de frequentie 'Uur' is, is de herhaling om de 6 uur. |
   | **Frequentie** | Ja | frequency | Tekenreeks | De tijdseenheid voor de herhaling: **Tweede,** **Minuut**of **Uur** |
   ||||||

   ![Geavanceerde herhalingsopties](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Open de lijst **Nieuwe parameter toevoegen voor** meer herhalingsopties. 
   Alle opties die u selecteert, worden na selectie op de trigger weergegeven.

   | Eigenschap | Vereist | JSON-naam | Type | Beschrijving |
   |----------|----------|-----------|------|-------------|
   | **Vertraging** | Nee | Vertraging | Tekenreeks | De duur om elke herhaling uit te stellen met behulp van de [datumspecificatie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations) |
   | **Tijdzone** | Nee | timeZone | Tekenreeks | Geldt alleen wanneer u een begintijd opgeeft omdat deze trigger geen [UTC-verschuiving](https://en.wikipedia.org/wiki/UTC_offset)accepteert. Selecteer de tijdzone die u wilt toepassen. |
   | **Begintijd** | Nee | startTime | Tekenreeks | Geef een begindatum en -tijd op in deze indeling: <p>YYYY-MM-DDThh:mm:ss als u een tijdzone selecteert <p>-of- <p>YYYY-MM-DDThh:mm:ssZ als u geen tijdzone selecteert <p>Als u bijvoorbeeld 18 september 2017 om 14:00 uur wilt, geeft u '2017-09-18T14:00:00' op en selecteert u een tijdzone zoals Pacific Standard Time. Of geef '2017-09-18T14:00:00Z' op zonder tijdzone. <p>**Let op:** Deze begintijd moet de [ISO 8601-datumtijdspecificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [utc-datumtijdnotatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)volgen, maar zonder [utc-verschuiving](https://en.wikipedia.org/wiki/UTC_offset). Als u geen tijdzone selecteert, moet u de letter 'Z' aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de gelijkwaardige [nautische tijd](https://en.wikipedia.org/wiki/Nautical_time). <p>Voor eenvoudige schema's is de begintijd de eerste gebeurtenis, terwijl voor gevorderde herhalingen de trigger niet eerder dan de begintijd wordt geactiveerd. [*Op welke manieren kan ik de begindatum en -tijd gebruiken?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Bouw nu uw resterende werkstroom samen met andere acties. Zie [Connectors voor Azure Logic Apps voor](../connectors/apis-list.md)meer acties die u toevoegen.

## <a name="workflow-definition---sliding-window"></a>Werkstroomdefinitie - Schuifvenster

In de onderliggende werkstroomdefinitie van uw logische app, die JSON gebruikt, u de triggerdefinitie schuifvenster bekijken met de opties die u hebt gekozen. Als u deze definitie wilt weergeven, kiest u op de werkbalk Code de optie **Codeweergave**. Als u wilt terugkeren naar de ontwerper, kiest u op de werkbalk **designer, Designer**.

In dit voorbeeld ziet u hoe een triggerdefinitie voor schuifvenster eruit kan zien in een onderliggende werkstroomdefinitie waarbij de vertraging voor elke herhaling vijf seconden bedraagt voor een herhaling per uur:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [De volgende actie in werkstromen uitstellen](../connectors/connectors-native-delay.md)
* [Connectors voor Logic Apps](../connectors/apis-list.md)