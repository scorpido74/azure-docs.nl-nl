---
title: Problemen met werkstromen vaststellen en oplossen
description: Meer informatie over het oplossen en diagnosticeren van problemen, fouten en fouten in uw werkstromen in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905078"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Problemen met werkstromen in Azure Logic Apps vaststellen en oplossen

Uw logica-app genereert informatie waarmee u problemen in uw app diagnosticeren en opsporen. U een logische app diagnosticeren door elke stap in de werkstroom te bekijken via de Azure-portal. U ook enkele stappen toevoegen aan een werkstroom voor foutopsporing van runtime.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Triggergeschiedenis controleren

Elke logische app-run begint met een triggerpoging, dus als de trigger niet wordt geactiveerd, voert u de volgende stappen uit:

1. Controleer de status van de trigger door [de triggergeschiedenis te controleren.](../logic-apps/monitor-logic-apps.md#review-trigger-history) Als u meer informatie over de triggerpoging wilt weergeven, selecteert u die triggergebeurtenis, bijvoorbeeld:

   ![Triggerstatus en -geschiedenis weergeven](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Controleer de ingangen van de trigger om te bevestigen dat ze verschijnen zoals u verwacht. Selecteer **onder de koppeling Invoer**de koppeling, waarin het deelvenster **Invoer** wordt weergegeven.

   Triggeringangen bevatten de gegevens die de trigger verwacht en vereist om de werkstroom te starten. Als u deze ingangen bekijkt, u bepalen of de triggeringangen correct zijn en of aan de voorwaarde is voldaan, zodat de werkstroom kan worden voortgezet.

   De `feedUrl` eigenschap heeft bijvoorbeeld een onjuiste RSS-feedwaarde:

   ![Triggeringangen controleren op fouten](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Controleer de eventuele triggers-uitvoer om te bevestigen dat ze worden weergegeven zoals u verwacht. Selecteer **onder de koppeling Uitvoer**de koppeling, waarin het deelvenster **Uitvoer** wordt weergegeven.

   Triggeruitvoer bevat de gegevens die de trigger doorgeeft aan de volgende stap in uw werkstroom. Als u deze uitvoer bekijkt, u bepalen of de juiste of verwachte waarden worden doorgegeven aan de volgende stap in uw werkstroom, bijvoorbeeld:

   ![Triggeroutputs controleren op fouten](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Als u inhoud vindt die u niet herkent, leest u meer over [verschillende inhoudstypen](../logic-apps/logic-apps-content-type.md) in Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>De uitvoeringen van de uitvoering controleren

Elke keer dat de trigger wordt geactiveerd voor een item of gebeurtenis, maakt en voert de Engine Logic Apps een afzonderlijke werkstroominstantie uit voor elk item of gebeurtenis. Als een run mislukt, volgt u deze stappen om te controleren wat er tijdens die uitvoering is gebeurd, inclusief de status voor elke stap in de werkstroom plus de ingangen en uitvoer voor elke stap.

1. Controleer de runstatus van de werkstroom door [de uitvoeringsgeschiedenis te controleren.](../logic-apps/monitor-logic-apps.md#review-runs-history) Als u meer informatie wilt weergeven over een mislukte run, inclusief alle stappen in die run in hun status, selecteert u de mislukte run.

   ![Run-geschiedenis weergeven en mislukte run selecteren](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Nadat alle stappen in de run worden weergegeven, vouwt u de eerste mislukte stap uit.

   ![Eerste mislukte stap uitvouwen](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Controleer de ingangen van de mislukte stap om te bevestigen of ze worden weergegeven zoals u verwacht.

1. Bekijk de details voor elke stap in een specifieke run. Selecteer **onder De geschiedenis Van Uitvoering**de run die u wilt onderzoeken.

   ![Beoordeling voert geschiedenis uit](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Details bekijken van een run van een logische app](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Als u de ingangen, uitvoer en eventuele foutberichten voor een specifieke stap wilt onderzoeken, kiest u die stap zodat de vorm wordt uitgebreid en de details worden weergegeven. Bijvoorbeeld:

   ![Details van de stappen bekijken](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Fouten in runtime opsporen

Als u wilt helpen met foutopsporing, u diagnostische stappen toevoegen aan een werkstroom voor logische apps, samen met het controleren van de trigger en het uitvoeren van de geschiedenis. U bijvoorbeeld stappen toevoegen die de [Webhook Tester-service](https://webhook.site/) gebruiken, zodat u HTTP-aanvragen inspecteren en de exacte grootte, vorm en indeling bepalen.

1. Ga naar de [Webhook Tester-site](https://webhook.site/) en kopieer de gegenereerde unieke URL.

1. Voeg in uw logische app een HTTP POST-actie toe plus de hoofdinhoud die u wilt testen, bijvoorbeeld een expressie of een andere stapuitvoer.

1. Plak uw URL van Webhook Tester in de actie HTTP POST.

1. Als u wilt controleren hoe een aanvraag wordt gevormd wanneer deze wordt gegenereerd vanuit de Logic Apps-engine, voert u de logische app uit en bezoekt u de Website van Webhook Tester opnieuw voor meer details.

## <a name="next-steps"></a>Volgende stappen

* [Uw logica-app controleren](../logic-apps/monitor-logic-apps.md)
