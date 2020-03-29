---
title: De volgende actie in werkstromen uitstellen
description: Wacht met het uitvoeren van de volgende actie in logische app-werkstromen met de acties Vertraging of Vertraging tot in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74787333"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>De volgende actie in Azure Logic Apps uitstellen

Als u uw logica-app een bepaalde tijd wilt laten wachten voordat de volgende actie wordt uitgevoerd, u de ingebouwde **actie Vertraging - Actie plannen** voordat een actie in de werkstroom van uw logische app wordt uitgevoerd, worden uitgevoerd. U ook de ingebouwde vertraging toevoegen **tot - Actie plannen** om te wachten tot een specifieke datum en tijd voordat u de volgende actie uitvoert. Zie [Terugkerende geautomatiseerde, taken en werkstromen plannen en uitvoeren met Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)voor meer informatie over de ingebouwde acties en triggers voor het plannen.

* **Vertraging:** wacht op het opgegeven aantal tijdeenheden, zoals seconden, minuten, uren, dagen, weken of maanden, voordat de volgende actie wordt uitgevoerd.

* **Vertraging tot**: Wacht tot de opgegeven datum en tijd voordat de volgende actie wordt uitgevoerd.

Hier volgen enkele voorbeelden van manieren om deze acties te gebruiken:

* Wacht tot een doordeweekse dag om een statusupdate via e-mail te verzenden.

* Stel uw werkstroom uit totdat een HTTP-gesprek is voltooid voordat gegevens worden hervat en opgehaald.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u geen abonnement hebt, u [zich aanmelden voor een gratis Azure-account.](https://azure.microsoft.com/free/)

* Basiskennis over [logische apps.](../logic-apps/logic-apps-overview.md) Voordat u een actie gebruiken, moet uw logische app eerst beginnen met een trigger. U elke gewenste trigger gebruiken en andere acties toevoegen voordat u een vertragingsactie toevoegt. In dit onderwerp wordt een Office 365 Outlook-trigger gebruikt. Als u nieuw bent in logische apps, leest u [hoe u uw eerste logische app maakt.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>De actie Vertraging toevoegen

1. Kies in de Logic App Designer onder de stap waarin u de vertragingsactie wilt toevoegen, de optie **Nieuwe stap**.

   Als u de vertragingsactie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl die de stappen verbindt. Kies het plusteken (+) dat wordt weergegeven en selecteer **Vervolgens Een actie toevoegen**.

1. Voer in het zoekvak 'vertraging' in als filter. Selecteer in de lijst met acties de optie: **Vertraging**

   ![Actie 'Vertraging' toevoegen](./media/connectors-native-delay/add-delay-action.png)

1. Geef de tijd op om te wachten voordat de volgende actie wordt uitgevoerd.

   ![Stel de tijd in voor de vertraging](./media/connectors-native-delay/delay-time-intervals.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|-----------|----------|------|-------------|
   | Count | count | Ja | Geheel getal | Het aantal tijdeenheden dat moet worden uitgesteld |
   | Eenheid | eenheid | Ja | Tekenreeks | De tijdseenheid, bijvoorbeeld: `Minute` `Hour`, `Day` `Second` `Week`, , , of`Month` |
   ||||||

1. Voeg alle andere acties toe die u in uw werkstroom wilt uitvoeren.

1. Wanneer u klaar bent, slaat u uw logica-app op.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>De actie Vertraging tot en met

1. Kies in de Logic App Designer onder de stap waarin u de vertragingsactie wilt toevoegen, de optie **Nieuwe stap**.

   Als u de vertragingsactie tussen de stappen wilt toevoegen, verplaatst u de aanwijzer over de pijl die de stappen verbindt. Kies het plusteken (+) dat wordt weergegeven en selecteer **Vervolgens Een actie toevoegen**.

1. Voer in het zoekvak 'vertraging' in als filter. Selecteer in de lijst met acties de optie: **Vertraging tot**

   ![Actie 'Uitstellen tot' toevoegen](./media/connectors-native-delay/add-delay-until-action.png)

1. Geef de einddatum en tijd op voor wanneer u de werkstroom wilt hervatten.

   ![Tijdstempel opgeven voor wanneer de vertraging moet worden beëindigd](./media/connectors-native-delay/delay-until-timestamp.png)

   | Eigenschap | JSON-naam | Vereist | Type | Beschrijving |
   |----------|-----------|----------|------|-------------|
   | Tijdstempel | tijdstempel | Ja | Tekenreeks | De einddatum en de tijd voor het hervatten van de werkstroom met deze indeling: <p>YYYY-MM-DDThh:mm:ssZ <p>Als u bijvoorbeeld 18 september 2017 om 14:00 uur wilt, geeft u '2017-09-18T14:00:00Z' op. <p>**Let op:** Deze tijdnotatie moet de [ISO 8601-datumtijdspecificatie](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) in [utc-datumtijdnotatie](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)volgen, maar zonder [utc-verschuiving](https://en.wikipedia.org/wiki/UTC_offset). Zonder tijdzone moet u de letter "Z" aan het einde toevoegen zonder spaties. Deze "Z" verwijst naar de gelijkwaardige [nautische tijd](https://en.wikipedia.org/wiki/Nautical_time). |
   ||||||

1. Voeg alle andere acties toe die u in uw werkstroom wilt uitvoeren.

1. Wanneer u klaar bent, slaat u uw logica-app op.

## <a name="next-steps"></a>Volgende stappen

* [Terugkerende taken en werkstromen maken, plannen en uitvoeren met de recidieftrigger](../connectors/connectors-native-recurrence.md)
* [Connectors voor Logic Apps](../connectors/apis-list.md)