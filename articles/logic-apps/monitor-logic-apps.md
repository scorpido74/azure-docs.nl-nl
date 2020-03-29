---
title: Status bewaken, geschiedenis weergeven en waarschuwingen instellen
description: Logische apps oplossen door de uitvoeringsstatus te controleren, triggergeschiedenis te controleren en waarschuwingen in Azure Logic Apps in te schakelen
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907771"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Runstatus controleren, triggergeschiedenis controleren en waarschuwingen instellen voor Azure Logic Apps

Nadat u een logische app hebt [gemaakt en uitgevoerd,](../logic-apps/quickstart-create-first-logic-app-workflow.md)u de uitvoeringsstatus van die logische app, [de uitvoering van de geschiedenis,](#review-runs-history) [de triggergeschiedenis](#review-trigger-history)en de prestaties controleren. Als u meldingen wilt ontvangen over storingen of andere mogelijke problemen, stelt u [waarschuwingen](#add-azure-alerts)in. U bijvoorbeeld een waarschuwing maken die detecteert 'wanneer meer dan vijf runs in een uur mislukken'.

Voor realtime gebeurtenisbewaking en rijkere foutopsporing stelt u diagnostische logboekregistratie in voor uw logische app met Azure [Monitor-logboeken.](../azure-monitor/overview.md) Met deze Azure-service u uw cloud- en on-premises omgevingen bewaken, zodat u hun beschikbaarheid en prestaties gemakkelijker behouden. U vervolgens gebeurtenissen zoeken en bekijken, zoals triggergebeurtenissen, uitvoeren van gebeurtenissen en actiegebeurtenissen. Door deze informatie op te slaan in [Azure Monitor-logboeken,](../azure-monitor/platform/data-platform-logs.md)u [logboekquery's](../azure-monitor/log-query/log-query-overview.md) maken waarmee u deze informatie vinden en analyseren. U deze diagnostische gegevens ook gebruiken met andere Azure-services, zoals Azure Storage en Azure Event Hubs. Zie [Logische apps controleren met Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)voor meer informatie.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Beoordeling voert geschiedenis uit

Elke keer dat de trigger wordt geactiveerd voor een item of gebeurtenis, maakt en voert de Engine Logic Apps een afzonderlijke werkstroominstantie uit voor elk item of gebeurtenis. Standaard wordt elke werkstroominstantie parallel uitgevoerd, zodat geen enkele werkstroom hoeft te wachten voordat een run wordt gestart. U bekijken wat er tijdens die uitvoering is gebeurd, inclusief de status voor elke stap in de werkstroom plus de ingangen en uitvoer voor elke stap.

1. Zoek en open uw logische app in de Logic App Designer in de [Azure-portal.](https://portal.azure.com)

   Voer in het belangrijkste Azure-zoekvak de `logic apps`optie Logic Apps in en selecteer **vervolgens Logische apps**.

   ![De service 'Logische apps' zoeken en selecteren](./media/monitor-logic-apps/find-your-logic-app.png)

   De Azure-portal toont alle logische apps die zijn gekoppeld aan uw Azure-abonnementen. U deze lijst filteren op naam, abonnement, resourcegroep, locatie, enzovoort.

   ![Logische apps weergeven die zijn gekoppeld aan abonnementen](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecteer uw logische app en selecteer **Overzicht**.

   In het overzichtsvenster wordt onder **Geschiedenis uitvoert**, alle eerdere, huidige en eventuele wachtuitvoeringen voor uw logische app weergegeven. Als in de lijst veel runs worden weergegeven en u de gewenste vermelding niet vinden, u de lijst filteren. Als u de verwachte gegevens niet vindt, probeert u **Vernieuwen** op de werkbalk te selecteren.

   ![Overzicht, geschiedenis en andere logische app-informatie](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Hier volgen de mogelijke statussen voor een logische app-run:

   | Status | Beschrijving |
   |--------|-------------|
   | **Geannuleerd** | De werkstroom werd uitgevoerd, maar heeft een annuleringsverzoek ontvangen |
   | **Mislukt** | Ten minste één actie is mislukt en er zijn geen latere acties in de werkstroom ingesteld om de fout af te handelen |
   | **Wordt uitgevoerd** | De werkstroom wordt momenteel uitgevoerd. <p>Deze status kan ook worden weergegeven voor werkstromen met een beperkt tarief of als gevolg van het huidige prijsplan. Zie voor meer informatie de [actielimieten op de prijspagina](https://azure.microsoft.com/pricing/details/logic-apps/). Als u [diagnostische logboekregistratie](../logic-apps/monitor-logic-apps.md)instelt, u informatie krijgen over eventuele throttle-gebeurtenissen die zich voordoen. |
   | **Geslaagd** | Alle acties zijn geslaagd. <p>**Opmerking:** Als er fouten zijn opgetreden in een specifieke actie, is een latere actie in de werkstroom die fout afgehandeld. |
   | **Wachten** | De werkstroom is niet gestart of wordt onderbroken, bijvoorbeeld vanwege een eerdere werkstroom die nog steeds wordt uitgevoerd. |
   |||

1. Als u de stappen en andere informatie voor een specifieke uitvoering wilt bekijken, selecteert u onder **Uitvoeringen geschiedenis**die run.

   ![Selecteer een specifieke run die u wilt bekijken](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   In het **deelvenster Uitvoeren van de logische app** wordt elke stap in de geselecteerde run, de runstatus van elke stap en de tijd die nodig is voor elke stap weergegeven:

   ![Elke actie in de specifieke run](./media/monitor-logic-apps/logic-app-run-pane.png)

   Als u deze informatie in lijstvorm wilt weergeven, selecteert u op de werkbalk **Logica-app uitvoeren** de optie **Details uitvoeren**.

   ![Selecteer op de werkbalk 'Details uitvoeren'](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   In de weergave Details uitvoeren worden elke stap, hun status en andere informatie weergegeven.

   ![Details over elke stap in de run bekijken](./media/monitor-logic-apps/review-logic-app-run-details.png)

   U bijvoorbeeld de eigenschap **Correlatie-ID** van de run krijgen, die u mogelijk nodig hebt wanneer u de [REST-API voor Logische Apps gebruikt.](https://docs.microsoft.com/rest/api/logic)

1. Als u meer informatie wilt krijgen over een specifieke stap, selecteert u een van de opties:

   * Selecteer in het **deelvenster Logica-app de** stap, zodat de vorm wordt uitgebreid. U nu informatie bekijken, zoals invoer, uitvoer en eventuele fouten die in die stap zijn opgetreden, bijvoorbeeld:

     ![In het uitvoerensvenster van logische apps u de stap mislukt weergeven](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Selecteer in het deelvenster Details van de **Logische app** uitvoeren de gewenste stap.

     ![In het deelvenster Details uitvoeren de optie mislukte weergave worden uitgevoerd](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     U nu informatie zoals ingangen en uitvoer voor die stap bekijken, bijvoorbeeld:

   > [!NOTE]
   > Alle runtime-details en gebeurtenissen worden versleuteld binnen de Logic Apps-service. Ze worden alleen gedecodeerd wanneer een gebruiker verzoekt om die gegevens te bekijken. U [invoer en uitvoer verbergen in de rungeschiedenis](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) of de toegang van gebruikers tot deze informatie beheren met Behulp van [RBAC (Azure Role-Based Access Control).](../role-based-access-control/overview.md)

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Triggergeschiedenis bekijken

Elke logische app-run begint met een trigger. De triggergeschiedenis bevat alle triggerpogingen die uw logische app heeft gemaakt en informatie over de ingangen en uitvoer voor elke triggerpoging.

1. Zoek en open uw logische app in de Logic App Designer in de [Azure-portal.](https://portal.azure.com)

   Voer in het belangrijkste Azure-zoekvak de `logic apps`optie Logic Apps in en selecteer **vervolgens Logische apps**.

   ![De service 'Logische apps' zoeken en selecteren](./media/monitor-logic-apps/find-your-logic-app.png)

   De Azure-portal toont alle logische apps die zijn gekoppeld aan uw Azure-abonnementen. U deze lijst filteren op naam, abonnement, resourcegroep, locatie, enzovoort.

   ![Logische apps weergeven die zijn gekoppeld aan abonnementen](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selecteer uw logische app en selecteer **Overzicht**.

1. Selecteer **Overzicht**in het menu van uw logische app . Selecteer in de sectie **Samenvatting** onder **Evaluatie**de optie **Triggergeschiedenis weergeven**.

   ![Triggergeschiedenis voor uw logische app weergeven](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   In het triggergeschiedenisvenster worden alle triggerpogingen weergegeven die uw logische app heeft uitgevoerd. Elke keer dat de trigger wordt geactiveerd voor een item of gebeurtenis, maakt de Logische Apps-engine een aparte logische app-instantie die de werkstroom uitvoert. Standaard wordt elke instantie parallel uitgevoerd, zodat geen enkele werkstroom hoeft te wachten voordat een run wordt gestart. Dus als uw logische app wordt geactiveerd op meerdere items tegelijk, wordt er voor elk item een triggeritem met dezelfde datum en tijd weergegeven.

   ![Meerdere triggerpogingen voor verschillende items](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Hier zijn de mogelijke statussen voor een triggerpoging:

   | Status | Beschrijving |
   |--------|-------------|
   | **Mislukt** | Er is een fout opgetreden. Als u gegenereerde foutberichten wilt controleren op een mislukte trigger, selecteert u de triggerpoging en kiest **u Uitvoer**. U bijvoorbeeld ingangen vinden die niet geldig zijn. |
   | **Overgeslagen** | De trigger controleerde het eindpunt, maar vond geen gegevens. |
   | **Geslaagd** | De trigger controleerde het eindpunt en vond beschikbare gegevens. Meestal verschijnt er ook een "Ontslagen" status naast deze status. Als dit niet het zo is, kan de triggerdefinitie een voorwaarde of `SplitOn` opdracht hebben waar niet aan is voldaan. <p>Deze status kan van toepassing zijn op een handmatige trigger, recidief trigger, of polling trigger. Een trigger kan met succes worden uitgevoerd, maar de run zelf kan nog steeds mislukken wanneer de acties onverwerkte fouten genereren. |
   |||

   > [!TIP]
   > U de trigger opnieuw controleren zonder te wachten op de volgende herhaling. Selecteer op de werkbalk overzicht **de trigger uitvoeren**en selecteer de trigger, waardoor een controle wordt geactiveerd. Of selecteer **Uitvoeren** op de werkbalk Logic Apps Designer.

1. Als u informatie over een specifieke triggerpoging wilt weergeven, selecteert u in het triggervenster de gebeurtenis trigger. Als in de lijst veel triggerpogingen worden weergegeven en u de gewenste vermelding niet vinden, u de lijst filteren. Als u de verwachte gegevens niet vindt, probeert u **Vernieuwen** op de werkbalk te selecteren.

   ![Specifieke triggerpoging weergeven](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   U nu informatie over de geselecteerde triggergebeurtenis bekijken, bijvoorbeeld:

   ![Specifieke triggerinformatie weergeven](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Bewakingswaarschuwingen instellen

Als u waarschuwingen wilt ontvangen op basis van specifieke statistieken of overschreden drempelwaarden voor uw logische app, stelt u [waarschuwingen](../azure-monitor/platform/alerts-overview.md)in Azure Monitor in. Meer informatie over [statistieken in Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). Voer deze stappen uit om waarschuwingen in te stellen zonder [Azure Monitor te](../log-analytics/log-analytics-overview.md)gebruiken.

1. Selecteer in het menu van uw logische app onder **Controleren**de optie**Nieuwe waarschuwingsregel** **waarschuwen** > .

   ![Een waarschuwing toevoegen voor uw logische app](./media/monitor-logic-apps/add-new-alert-rule.png)

1. Selecteer in het deelvenster **Regel maken** onder **Resource**uw logische app als deze nog niet is geselecteerd. Selecteer **Onder Voorwaarde**de optie **Toevoegen,** zodat u de voorwaarde definiëren die de waarschuwing activeert.

   ![Een voorwaarde voor de regel toevoegen](./media/monitor-logic-apps/add-condition-for-rule.png)

1. Zoek en selecteer in het deelvenster **Signaallogica configureren** het signaal waarvoor u een waarschuwing wilt ontvangen. U het zoekvak gebruiken of de signalen alfabetisch sorteren, de kolomkop van de **naam signaal** selecteren.

   Als u bijvoorbeeld een waarschuwing wilt verzenden wanneer een trigger mislukt, voert u de volgende stappen uit:

   1. Zoek en selecteer in de kolom **De naam van het signaal** het signaal Triggers **mislukt.**

      ![Signaal selecteren voor het maken van een waarschuwing](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Stel op het informatievenster dat wordt geopend voor het geselecteerde signaal onder **Waarschuwingslogica**uw voorwaarde in, bijvoorbeeld:

   1. Selecteer Bij **Operator** **Groter dan of gelijk aan**.

   1. Selecteer Voor **aggregatietype** **Aantal**.

   1. Voer **voor drempelwaarde**. `1`

   1. Controleer **onder de voorvertoning Voorwaarde**of uw voorwaarde correct wordt weergegeven.

   1. Stel **onder Geëvalueerd op basis**van , het interval en de frequentie in voor het uitvoeren van de waarschuwingsregel. Selecteer **bij Aggregatiegranulariteit (periode)** de periode voor het groeperen van de gegevens. Selecteer **bij Frequentie van evaluatie**hoe vaak u de voorwaarde wilt controleren.

   1. Wanneer u klaar bent, selecteert u **Gereed**.

   Hier is de afgewerkte voorwaarde:

   ![Voorwaarde instellen voor waarschuwing](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   Op de pagina **Regel maken** wordt nu de voorwaarde weergegeven die u hebt gemaakt en de kosten voor het uitvoeren van die waarschuwing.

   ![Nieuwe waarschuwing op de pagina Regel maken](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Geef een naam, optionele beschrijving en ernstniveau op voor uw waarschuwing. Laat de **regel Inschakelen staan wanneer de instelling voor het maken** is ingeschakeld of schakel uit totdat u klaar bent om de regel in te schakelen.

1. Wanneer u klaar bent, selecteert u **Waarschuwingsregel maken**.

> [!TIP]
> Als u een logische app wilt uitvoeren vanuit een waarschuwing, u de [trigger voor aanvragen](../connectors/connectors-native-reqres.md) opnemen in uw werkstroom, waarmee u taken zoals deze voorbeelden uitvoeren:
> 
> * [Post naar Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Een sms verzenden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Een bericht toevoegen aan een wachtrij](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Volgende stappen

* [Logische apps bewaken met Azure Monitor](../logic-apps/monitor-logic-apps-log-analytics.md)