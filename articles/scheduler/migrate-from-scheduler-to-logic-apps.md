---
title: Migreren van Azure scheduler naar Azure Logic Apps
description: Meer informatie over hoe u taken kunt vervangen in azure scheduler, die buiten gebruik worden gesteld, met Azure Logic Apps
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/23/2019
ms.openlocfilehash: 6b80cbd16ac78f7f347bef9ab8e22c4d67d31058
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301033"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Azure scheduler-taken migreren naar Azure Logic Apps

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) vervangt Azure scheduler, die buiten gebruik wordt [gesteld](#retire-date). Als u wilt blijven werken met de taken die u in scheduler hebt ingesteld, gaat u naar Azure Logic Apps zo snel mogelijk door dit artikel te volgen.

In dit artikel wordt beschreven hoe u eenmalige en terugkerende taken kunt plannen door automatische werk stromen te maken met Azure Logic Apps, in plaats van met Azure scheduler. Wanneer u geplande taken met Logic Apps maakt, krijgt u de volgende voor delen:

* Bouw uw taak met behulp van een visuele ontwerper en [kant-en-klare connectors](../connectors/apis-list.md) van honderden Services, zoals Azure Blob Storage, Azure service bus, Office 365 Outlook en SAP.

* Elke geplande werk stroom beheren als een Azure-resource voor de eerste klasse. U hoeft zich geen zorgen te maken over het concept van een *taak verzameling* omdat elke logische app een afzonderlijke Azure-resource is.

* Meerdere eenmalige taken uitvoeren met behulp van één logische app.

* Schema's instellen die tijd zones ondersteunen en automatisch aanpassen aan zomer tijd (DST).

Zie [Wat is Azure Logic apps?](../logic-apps/logic-apps-overview.md) of probeer uw eerste logische app te maken in deze Snelstartgids: [Maak uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

* Als u uw logische app wilt activeren door HTTP-aanvragen te verzenden, gebruikt u een hulp programma zoals de [postman desktop-app](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Eenmalige taken plannen

U kunt meerdere eenmalige taken uitvoeren door slechts één logische app te maken. 

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app in de ontwerp functie voor logische apps. 

   Volg [voor de basis stappen Quick Start: Maak uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Typ in het zoekvak ' wanneer een HTTP-aanvraag ' als uw filter. Selecteer in de lijst triggers deze trigger: **Wanneer een HTTP-aanvraag wordt ontvangen** 

   ![Trigger voor aanvraag toevoegen](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Voor de aanvraag trigger kunt u optioneel een JSON-schema opgeven, waarmee de logica ontwerper de structuur van de invoer van de binnenkomende aanvraag begrijpt en de uitvoer gemakkelijker kan worden geselecteerd in uw werk stroom.

   Als u een schema wilt opgeven, voert u het schema in het vak **JSON-schema van aanvraag tekst** in, bijvoorbeeld: 

   ![Schema aanvragen](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Als u geen schema hebt, maar u een voor beeld van een nettolading in JSON-indeling hebt, kunt u een schema genereren op basis van die nettolading.

   1. Selecteer in de trigger voor de aanvraag een **voor beeld-nettolading gebruiken om een schema te genereren**.

   1. Geef bij **een voor beeld van een JSON-nettolading op of plak hier**de voor beeld-nettolading op en selecteer vervolgens **gereed**, bijvoorbeeld:

      ![Voorbeeld lading](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Selecteer **volgende stap**onder de trigger. 

1. Voer in het zoekvak ' delay until ' in als uw filter. Selecteer in de lijst acties deze actie: **Vertraging tot**

   Met deze actie wordt de werk stroom van uw logische app onderbroken tot een opgegeven datum en tijd.

   ![Actie ' vertraging tot ' toevoegen](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Voer het tijds tempel in voor wanneer u de werk stroom van de logische app wilt starten. 

   Wanneer u in het vak **Time Stamp** klikt, wordt de lijst met dynamische inhoud weer gegeven, zodat u eventueel een uitvoer van de trigger kunt selecteren.

   ![Details van "vertraging tot" opgeven](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Voeg andere acties toe die u wilt uitvoeren door te kiezen uit [honderden kant-en-klare connectors](../connectors/apis-list.md). 

   U kunt bijvoorbeeld een HTTP-actie toevoegen die een aanvraag verzendt naar een URL of acties die werken met opslag wachtrijen, Service Bus wachtrijen of Service Bus onderwerpen: 

   ![HTTP-actie](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Wanneer u klaar bent, slaat u de logische app op.

   ![Uw logische app opslaan](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Wanneer u de logische app voor het eerst opslaat, wordt de eind punt-URL voor de aanvraag trigger van uw logische app weer gegeven in het vak **http post-URL** . 
   Als u uw logische app wilt aanroepen en invoer wilt verzenden naar uw logische app voor verwerking, gebruikt u deze URL als oproep bestemming.

   ![Eind punt-URL voor aanvraag opslaan](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Kopieer deze eind punt-URL en sla deze op, zodat u later een hand matige aanvraag kunt verzenden die uw logische app activeert. 

## <a name="start-a-one-time-job"></a>Een eenmalige taak starten

Als u een eenmalige taak hand matig wilt uitvoeren of activeren, stuurt u een aanroep naar de eind punt-URL voor de aanvraag trigger van uw logische app. In deze aanroep geeft u de invoer of Payload op die u wilt verzenden, wat u mogelijk eerder hebt beschreven door een schema op te geven. 

Als u bijvoorbeeld de Postman-app gebruikt, kunt u een POST-aanvraag maken met de instellingen die vergelijkbaar zijn met dit voor beeld en vervolgens **verzenden** selecteren om de aanvraag te doen.

| Aanvraagmethode | URL | Hoofdtekst | Headers |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **uitgang** <p>**JSON(application/json)** <p>Voer in het vak **onbewerkt** de lading in die u in de aanvraag wilt verzenden. <p>**Opmerking**: Met deze instelling worden de waarden van de **headers** automatisch geconfigureerd. | **Sleutel**: Content-Type <br>**Waarde**: Application/JSON |
|||||

![Aanvraag verzenden om uw logische app hand matig te activeren](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Nadat u de oproep hebt verzonden, wordt de reactie van de logische app weer gegeven onder het vak onbewerkt op het tabblad **hoofd tekst** . 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Als u de taak later wilt annuleren, selecteert u het tabblad **headers** . Zoek en kopieer de waarde van de header **x-MS-workflow-run-id** in het antwoord. 
>
> ![Antwoord](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Een eenmalige taak annuleren

In Logic Apps wordt elke eenmalige taak uitgevoerd als een run-exemplaar van een enkele logische app. Als u een eenmalige taak wilt annuleren, kunt u [werk stroom uitvoeringen gebruiken-annuleren](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) in de Logic apps rest API. Wanneer u een aanroep naar de trigger verzendt, geeft u de [werk stroom-run-id](#workflow-run-id)op.

## <a name="schedule-recurring-jobs"></a>Plan terugkerende taken

1. Maak in de [Azure Portal](https://portal.azure.com)een lege logische app in de ontwerp functie voor logische apps. 

   Volg [voor de basis stappen Quick Start: Maak uw eerste logische app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Voer in het zoekvak ' recurrence ' in als uw filter. Selecteer in de lijst triggers deze trigger: **Terugkeerpatroon** 

   ![De trigger ' recurrence ' toevoegen](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Stel een meer geavanceerde planning in, indien gewenst.

   ![Geavanceerd schema](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Zie [terugkerende taken en werk stromen maken en uitvoeren met Azure Logic apps](../connectors/connectors-native-recurrence.md)voor meer informatie over geavanceerde plannings opties.

1. U kunt andere acties toevoegen door te kiezen uit [honderden gebruiks klare](../connectors/apis-list.md). Selecteer **volgende stap**onder de trigger. Zoek de gewenste acties en selecteer deze.

   U kunt bijvoorbeeld een HTTP-actie toevoegen die een aanvraag verzendt naar een URL of acties die werken met opslag wachtrijen, Service Bus wachtrijen of Service Bus onderwerpen: 

   ![HTTP-actie](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Wanneer u klaar bent, slaat u de logische app op.

   ![Uw logische app opslaan](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Geavanceerde installatie

Hier volgen andere manieren waarop u uw taken kunt aanpassen.

### <a name="retry-policy"></a>Beleid voor opnieuw proberen

Als u wilt bepalen hoe een actie wordt uitgevoerd in uw logische app wanneer er onregelmatige fouten optreden, kunt u het [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies) instellen in de instellingen van elke actie, bijvoorbeeld:

1. Open het menu met weglatings tekens ( **...** ) van de actie en selecteer **instellingen**.

   ![Actie-instellingen openen](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selecteer het gewenste beleid voor opnieuw proberen. Zie [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies)voor meer informatie over elk beleid.

   ![Beleid voor opnieuw proberen selecteren](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Uitzonde ringen en fouten verwerken

Als de standaard actie in azure scheduler niet kan worden uitgevoerd, kunt u een alterative-actie uitvoeren die de fout status verbiedt. In Azure Logic Apps kunt u ook dezelfde taak uitvoeren.

1. In de ontwerp functie voor logische apps, boven de actie die u wilt verwerken, plaatst u de muis aanwijzer op de pijl tussen de stappen en selecteert u **een parallelle vertakking toevoegen**. 

   ![Parallelle vertakking toevoegen](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Zoek en selecteer de actie die u wilt uitvoeren in plaats van de alternatieve actie.

   ![Parallelle actie toevoegen](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Open op de alternatieve actie het menu met weglatings tekens ( **...** ) en selecteer **uitvoeren na**.

   ![Uitvoeren na configureren](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Schakel het selectie vakje in voor de eigenschap **is geslaagd** . Selecteer deze eigenschappen: **mislukt**, **wordt overgeslagen**en er is **een time-out opgetreden**

   ![Eigenschappen van ' uitvoeren na ' instellen](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Wanneer u klaar bent, selecteert u **Gereed**.

Zie voor meer informatie over het afhandelen van uitzonde [ringen afhandelen en de eigenschap RunAfter](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Veelgestelde vragen

<a name="retire-date"></a>

**V**: Wanneer wordt Azure scheduler buiten gebruik gesteld? <br>
**A**: Azure scheduler is gepland om op 31 december 2019 volledig te worden buiten gebruik gesteld. Voor belang rijke stappen die u moet nemen vóór deze datum en een gedetailleerde tijd lijn raadpleegt u [pensioen datum voor scheduler uitbreiden tot 31 December 2019](https://azure.microsoft.com/en-us/updates/extending-retirement-date-of-scheduler/). Zie voor algemene updates [Azure updates-scheduler](https://azure.microsoft.com/updates/?product=scheduler).

**V**: Wat gebeurt er met mijn taak verzamelingen en-taken nadat de service buiten gebruik is gesteld? <br>
**A**: Alle scheduler-taak verzamelingen en-taken stoppen met uitvoeren en worden verwijderd uit het systeem.

**V**: Moet ik een back-up maken of andere taken uitvoeren voordat ik mijn scheduler-taken Migreer naar Logic Apps? <br>
**A**: Als best practice, moet u altijd een back-up maken van uw werk. Controleer of de Logic apps die u hebt gemaakt, op de verwachte manier worden uitgevoerd voordat u de scheduler-taken verwijdert of uitschakelt. 

**V**: Is er een hulp programma waarmee ik mijn taken kan migreren van scheduler naar Logic Apps? <br>
**A**: Elke scheduler-taak is uniek, dus er bestaat geen hulp programma met één grootte dat past. Er zijn echter verschillende scripts die u kunt aanpassen aan uw behoeften. Ga terug naar de beschik baarheid van scripts en probeer het later opnieuw.

**V**: Waar kan ik ondersteuning krijgen voor het migreren van mijn scheduler-taken? <br>
**A**: Hier volgen enkele manieren om ondersteuning te krijgen: 

**Azure-portal**

Als uw Azure-abonnement een betaald ondersteunings plan heeft, kunt u een aanvraag voor technische ondersteuning in het Azure Portal maken. Anders kunt u een andere ondersteunings optie selecteren.

1. Selecteer in [](https://portal.azure.com) het hoofd menu van Azure Portal **Help + ondersteuning**.

1. Selecteer in het menu **ondersteuning** de optie **nieuwe ondersteunings aanvraag**. Geef deze informatie op over uw aanvraag:

   | Eigenschap | Value |
   |---------|-------|
   | **Probleem type** | **Documentatie** |
   | **Abonnement** | <*your-Azure-subscription*> |
   | **Service** | Selecteer scheduler onder bewaking van **& beheer**. Als u **scheduler**niet kunt vinden, selecteert u eerst **alle services** . |
   ||| 

1. Selecteer de gewenste ondersteunings optie. Als u een betaald ondersteunings abonnement hebt, selecteert u **volgende**.

**Community**

* [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Volgende stappen

* [Regel matig actieve taken en werk stromen maken met Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Zelfstudie: Verkeer controleren met een logische app op basis van een planning](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
