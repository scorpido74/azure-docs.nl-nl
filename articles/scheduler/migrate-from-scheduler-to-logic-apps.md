---
title: Migreren van Azure Scheduler naar Azure Logic Apps
description: Meer informatie over hoe u taken vervangen in Azure Scheduler, dat wordt uitgeschakeld, met Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 90c3cc2e096b9b58465987bc53f718c5d06c6203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78899099"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Azure Scheduler-taken migreren naar Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vervangt Azure Scheduler, dat [wordt uitgeschakeld.](#retire-date) Als u wilt blijven werken met de taken die u in Scheduler hebt ingesteld, migreert u zo snel mogelijk naar Azure Logic Apps door dit artikel te volgen. 
>
> Scheduler is niet langer beschikbaar in de Azure-portal, maar de [REST API-](/rest/api/scheduler) en [Azure Scheduler PowerShell-cmdlets](scheduler-powershell-reference.md) blijven op dit moment beschikbaar, zodat u uw taken en taakverzamelingen beheren.

In dit artikel ziet u hoe u eenmalige en terugkerende taken plannen door geautomatiseerde werkstromen te maken met Azure Logic Apps, in plaats van met Azure Scheduler. Wanneer u geplande taken maakt met Logic Apps, krijgt u de volgende voordelen:

* Bouw uw taak op met behulp van een visuele ontwerper en [kant-en-klare connectors](../connectors/apis-list.md) van honderden services, zoals Azure Blob Storage, Azure Service Bus, Office 365 Outlook en SAP.

* Beheer elke geplande werkstroom als een eersteklas Azure-bron. U hoeft zich geen zorgen te maken over het concept van een *taakverzameling,* omdat elke logische app een afzonderlijke Azure-bron is.

* Voer meerdere eenmalige taken uit met één logische app.

* Stel schema's in die tijdzones ondersteunen en automatisch aanpassen aan de zomertijd (Zomertijd).

Zie Wat is Azure Logic Apps voor meer [informatie?](../logic-apps/logic-apps-overview.md) [Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Als u uw logische app wilt activeren door HTTP-aanvragen te verzenden, gebruikt u een hulpprogramma zoals de [bureaubladapp Postman.](https://www.getpostman.com/apps)

## <a name="migrate-by-using-a-script"></a>Migreren met een script

Elke scheduler-taak is uniek, dus er bestaat geen one-size-fits-all tool voor het migreren van Scheduler-taken naar Azure Logic Apps. U [dit script](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) echter bewerken om aan uw behoeften te voldoen.

## <a name="schedule-one-time-jobs"></a>Eenmalige taken plannen

U meerdere eenmalige taken uitvoeren door slechts één logische app te maken.

1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app in Logic App Designer.

   Volg [Snelstart voor](../logic-apps/quickstart-create-first-logic-app-workflow.md)de basisstappen: Uw eerste logische app maken .

1. Voer in het `when a http request` zoekvak de trigger van Aanvraag in om de trigger van aanvraag te zoeken. Selecteer deze trigger in de lijst triggers: **Wanneer een HTTP-aanvraag wordt ontvangen**

   ![Trigger 'Verzoek' toevoegen](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Voor de trigger Van aanvraag u optioneel een JSON-schema opnemen, waarmee de Logic App Designer inzicht krijgt in de structuur voor de ingangen die zijn opgenomen in de binnenkomende aanroep naar de trigger Van aanvraag en dat de uitvoer gemakkelijker voor u wordt gemaakt om later in uw werkstroom te selecteren.

   Voer in het vak **JSON-schema van aanvraaginstantie** het schema in:

   ![Schema aanvragen](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Als u geen schema hebt, maar wel een monsterpayload in JSON-indeling hebt, u een schema genereren van die payload.

   1. Selecteer in de trigger Van aanvraag de optie **Voorbeeldpayload gebruiken om schema te genereren**.

   1. Geef **onder Een monster JSON-payload invoeren of plakken,** geef uw monsterpayload op en selecteer **Gereed,** bijvoorbeeld:

      ![Monsterpayload](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Selecteer Volgende **stap**onder de trigger .

1. Voer in het `delay until` zoekvak in als filter. Selecteer onder de lijst met acties de optie: **Vertraging tot**

   Met deze actie wordt de werkstroom van uw logische app onderbroken tot een bepaalde datum en tijd.

   ![Actie 'Uitstellen tot' toevoegen](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Voer de tijdstempel in voor wanneer u de werkstroom van de logische app wilt starten.

   Wanneer u in het vak **Tijdstempel** klikt, wordt de lijst met dynamische inhoud weergegeven, zodat u optioneel een uitvoer uit de trigger selecteren.

   ![Details "Uitstellen tot"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Voeg alle andere acties toe die u wilt uitvoeren door te selecteren uit [honderden kant-en-klare connectors.](../connectors/apis-list.md)

   U bijvoorbeeld een HTTP-actie opnemen die een verzoek naar een URL verzendt, of acties die werken met opslagwachtrijen, wachtrijen voor servicebussen of servicebusonderwerpen:

   ![HTTP-actie](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Wanneer u klaar bent, slaat u uw logica-app op.

   ![Uw logische app opslaan](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Wanneer u uw logische app voor de eerste keer opslaat, wordt de URL van het eindpunt voor de trigger Van uw logische app voor aanvragen weergegeven in het vak **HTTP-URL-post.** Wanneer u uw logische app wilt bellen en invoer naar uw logische app wilt verzenden voor verwerking, gebruikt u deze URL als aanroepbestemming.

   ![URL van het triggerpoint van aanvragen opslaan](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Kopieer en sla deze eindpunt-URL op, zodat u later een handmatig verzoek verzenden dat uw logische app activeert.

## <a name="start-a-one-time-job"></a>Een eenmalige taak starten

Als u een eenmalige taak handmatig wilt uitvoeren of activeren, stuurt u een oproep naar de URL van het eindpunt voor de trigger van Verzoek van uw logische app. Geef in deze aanroep de invoer of payload op die u eerder hebt beschreven door een schema op te geven.

Met de Postman-app u bijvoorbeeld een postaanvraag maken met de instellingen die vergelijkbaar zijn met dit voorbeeld en vervolgens **Verzenden** selecteren om het verzoek in te dienen.

| Aanvraagmethode | URL | Hoofdtekst | Headers |
|----------------|-----|------|---------|
| **POST** | <*EINDPUNT-URL*> | **Raw** <p>**JSON(toepassing/json)** <p>Voer in de **raw** box de payload in die u het verzoek wilt verzenden. <p>**Opmerking:** met deze instelling worden de waarden **kopteksten** automatisch geconfigureerd. | **Sleutel**: Inhoudstype <br>**Waarde**: toepassing/json |
|||||

![Verzoek verzenden om uw logische app handmatig te activeren](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Nadat u de oproep hebt verzonden, wordt het antwoord van uw logica-app weergegeven onder het **ruwe** vak op het tabblad **Hoofd.** 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Als u de taak later wilt annuleren, selecteert u het tabblad **Kopteksten.** Zoek en kopieer de waarde **x-ms-workflow-run-id-header** in het antwoord. 
>
> ![Antwoord](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Een eenmalige taak annuleren

In Logische apps wordt elke eenmalige taak uitgevoerd als een enkele logische app-instantie. Als u een eenmalige taak wilt annuleren, u [Werkstroomuitvoeringen gebruiken - Annuleren](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) in de API Voor de rest van de logica-apps. Wanneer u een oproep naar de trigger verzendt, geeft u de [werkstroom-run-id op](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Terugkerende taken plannen

1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app in Logic App Designer.

   Volg [Snelstart voor](../logic-apps/quickstart-create-first-logic-app-workflow.md)de basisstappen: Uw eerste logische app maken .

1. Voer in het zoekvak 'herhaling' in als filter. Selecteer deze trigger in de lijst triggers: **Herhaling**

   ![Trigger 'Herhaling' toevoegen](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Stel een meer geavanceerde planning in, als je wilt.

   ![Geavanceerd schema](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Zie [Terugkerende taken en werkstromen maken en uitvoeren met Azure Logic Apps](../connectors/connectors-native-recurrence.md)voor meer informatie over geavanceerde planningsopties.

1. Voeg andere acties toe die u wilt door te selecteren uit [honderden kant-en-klare](../connectors/apis-list.md)acties. Selecteer Volgende **stap**onder de trigger . Zoek en selecteer de gewenste acties.

   U bijvoorbeeld een HTTP-actie opnemen die een verzoek naar een URL verzendt, of acties die werken met opslagwachtrijen, wachtrijen voor servicebussen of servicebusonderwerpen:

   ![HTTP-actie](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Wanneer u klaar bent, slaat u uw logica-app op.

   ![Uw logische app opslaan](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Geavanceerde installatie

Hier volgen andere manieren waarop u uw vacatures aanpassen.

### <a name="retry-policy"></a>Beleid voor opnieuw proberen

Als u wilt bepalen hoe een actie probeert opnieuw te worden uitgevoerd in uw logische app wanneer er intermitterende fouten optreden, u het [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies) instellen in de instellingen van elke actie, bijvoorbeeld:

1. Open het menu Ellipsen (**... )** van de actie en selecteer **Instellingen**.

   ![Actie-instellingen openen](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecteer het gewenste beleid voor nieuwe poging. Zie [Beleid opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie over elk beleid .

   ![Beleid voor opnieuw proberen selecteren](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Omgaan met uitzonderingen en fouten

Als de standaardactie niet wordt uitgevoerd, u in Azure Scheduler een wijzigingsactie uitvoeren die de foutvoorwaarde aanpakt. In Azure Logic Apps u dezelfde taak ook uitvoeren.

1. Verplaats in Logic App Designer boven de actie die u wilt hanteren de aanwijzer over de pijl tussen de stappen en selecteer **Een parallelle vertakking toevoegen.**

   ![Parallelle vertakking toevoegen](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Zoek en selecteer de actie die u wilt uitvoeren in plaats daarvan als de alternatieve actie.

   ![Parallelle actie toevoegen](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Open bij de alternatieve actie het menu ellipsen (**... )** en selecteer Uitvoeren **configureren na**.

   ![Uitvoeren configureren na](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Maak de doos leeg voor de **succesvolle** eigenschap. Deze eigenschappen selecteren: **is mislukt,** **wordt overgeslagen**en **er is een time-out**

   ![Eigenschappen 'uitvoeren na' instellen](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Wanneer u klaar bent, selecteert u **Gereed**.

Zie Fouten en uitzonderingen afhandelen voor meer informatie over het [afhandelen van uitzonderingen en uitzonderingen , eigenschap RunAfter](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior).

## <a name="faq"></a>Veelgestelde vragen

<a name="retire-date"></a>

**V:** Wanneer gaat Azure Scheduler met pensioen? <br>
**A**: Azure Scheduler is gepland om volledig met pensioen te gaan op 31 december 2019. Zie [Verlenging van de pensioendatum voor Scheduler tot 31 december 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/)voor belangrijke stappen die vóór deze datum en een gedetailleerde tijdlijn moeten worden genomen. Zie [Azure-updates voor](https://azure.microsoft.com/updates/?product=scheduler)algemene updates voor algemene updates - Scheduler .

**V:** Wat gebeurt er met mijn taakverzamelingen en -taken nadat de service met pensioen is gegaan? <br>
**A:** Alle scheduler-taakverzamelingen en -taken worden niet meer uitgevoerd en worden uit het systeem verwijderd.

**V:** Moet ik een back-up maken of andere taken uitvoeren voordat ik mijn Scheduler-taken migreer naar Logische Apps? <br>
**A**: Als een best practice, altijd een back-up van uw werk. Controleer of de logische apps die u hebt gemaakt, worden uitgevoerd zoals verwacht voordat u uw Scheduler-taken verwijderde of uitschakelt.

**V:** Is er een tool waarmee ik mijn taken kan migreren van Scheduler naar Logic Apps? <br>
**A:** Elke Scheduler-taak is uniek, dus een one-size-fits-all-tool bestaat niet. Op basis van uw behoeften u dit script echter [bewerken om Azure Scheduler-taken te migreren naar Azure Logic Apps.](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration)

**V:** Waar kan ik ondersteuning krijgen voor het migreren van mijn Scheduler-taken? <br>
**A:** Hier zijn enkele manieren om ondersteuning te krijgen:

**Azure-portal**

Als uw Azure-abonnement een betaald ondersteuningsabonnement heeft, u een aanvraag voor technische ondersteuning maken in de Azure-portal. Anders u een andere ondersteuningsoptie selecteren.

1. Selecteer **help + ondersteuning**in het hoofdmenu van de [Azure-portal.](https://portal.azure.com)

1. Selecteer In het menu **Ondersteuning** de optie **Nieuw ondersteuningsverzoek**. Geef deze informatie over voor uw verzoek:

   | Eigenschap | Waarde |
   |---------|-------|
   | **Type probleem** | **Technisch** |
   | **Abonnement** | <*uw Azure-abonnement*> |
   | **Service** | Selecteer **Scheduler**onder **Monitoring & Management**. Als u **Scheduler**niet vinden, selecteert u **Alle services** eerst. |
   ||| 

1. Selecteer de gewenste ondersteuningsoptie. Als u een betaald ondersteuningsplan hebt, selecteert u **Volgende**.

**Community**

* [Azure Logic Apps-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Volgende stappen

* [Regelmatig actieve taken en werkstromen maken met Azure Logic Apps](../connectors/connectors-native-recurrence.md)