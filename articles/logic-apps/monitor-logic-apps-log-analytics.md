---
title: Logische apps bewaken met Azure Monitor-logboeken
description: Logische apps oplossen door Azure Monitor-logboeken in te stellen en diagnostische gegevens voor Azure Logic Apps te verzamelen
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270236"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Azure Monitor-logboeken instellen en diagnostische gegevens verzamelen voor Azure Logic Apps

Als u tijdens runtime uitgebreidere foutopsporingsgegevens over uw logische apps wilt krijgen, u [Azure Monitor-logboeken](../azure-monitor/platform/data-platform-logs.md) instellen en gebruiken om informatie over runtime-gegevens en -gebeurtenissen op te nemen en op te slaan, zoals triggergebeurtenissen, uitvoeren gebeurtenissen en actiegebeurtenissen in een [Log Analytics-werkruimte.](../azure-monitor/platform/resource-logs-collect-workspace.md) [Azure Monitor](../azure-monitor/overview.md) helpt u uw cloud- en on-premises omgevingen te bewaken, zodat u hun beschikbaarheid en prestaties gemakkelijker behouden. Met Azure Monitor-logboeken u [logboekquery's](../azure-monitor/log-query/log-query-overview.md) maken waarmee u deze informatie verzamelen en controleren. U deze diagnostische gegevens ook [gebruiken met andere Azure-services,](#extend-data)zoals Azure Storage en Azure Event Hubs.

Als u logboekregistratie voor uw logische app wilt instellen, u [Logboekanalyse inschakelen wanneer u uw logische app maakt](#logging-for-new-logic-apps)of de oplossing Voor het beheer van Logische apps [installeren](#install-management-solution) in uw logboekanalysewerkruimte voor bestaande logische apps. Deze oplossing biedt geaggregeerde informatie voor het uitvoeren van uw logische app en bevat specifieke details zoals status, uitvoeringstijd, herindieningsstatus en correlatie-id's. Als u vervolgens logboekregistratie en het maken van query's voor deze informatie wilt inschakelen, [stelt u Azure Monitor-logboeken in.](#set-up-resource-logs)

In dit artikel ziet u hoe u Log Analytics inschakelt wanneer u logische apps maakt, hoe u de oplossing Voor beheer van Logische apps installeert en instelt en hoe u query's voor Azure Monitor-logboeken instelt en maakt.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, hebt u een [Log Analytics-werkruimte](../azure-monitor/platform/resource-logs-collect-workspace.md)nodig. Als u geen werkruimte hebt, leest u [hoe u een werkruimte Log Analytics maakt.](../azure-monitor/learn/quick-create-workspace.md)

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Logboekanalyse inschakelen voor nieuwe logische apps

U Log Analytics inschakelen wanneer u uw logische app maakt.

1. Voer in de [Azure-portal](https://portal.azure.com)in het deelvenster waar u de informatie verstrekt om uw logische app te maken, de volgende stappen uit:

   1. Selecteer **Onder Logboekanalyse**de optie **Aan**.

   1. Selecteer in de lijst **Met logboekanalyses werkruimte** de werkruimte waar u de gegevens vanuit uw logische app wilt verzenden.

      ![Informatie over logische app opgeven](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      Nadat u deze stap hebt voltooid, maakt Azure uw logische app, die nu is gekoppeld aan uw Werkruimte Log Analytics. Met deze stap wordt ook automatisch de Logic Apps Management-oplossing in uw werkruimte geïnstalleerd.

1. Als u gereed bent, selecteert u **Maken**.

1. Nadat u uw logische app hebt uitgevoerd, gaat u verder met deze stappen om uw logische app [weer te geven.](#view-logic-app-runs)

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Logic Apps Management-oplossing installeren

Als u Log Analytics hebt ingeschakeld toen u uw logische app hebt gemaakt, slaat u deze stap over. U hebt de Oplossing voor Logic Apps Management al geïnstalleerd in uw Log Analytics-werkruimte.

1. Voer [Azure portal](https://portal.azure.com)in het zoekvak van `log analytics workspaces`azure portal de optie Log Analytics-werkruimten in en selecteer **vervolgens Logboekanalysewerkruimten**.

   ![Selecteer 'Log Analytics-werkruimten'](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. Selecteer **onder Werkruimten voor Logboekanalyse**uw werkruimte.

   ![Selecteer uw Logboekanalysewerkruimte](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. Selecteer in het deelvenster **Overzicht** onder Aan de slag met**monitoringoplossingen voor** **logboekanalyse** > configureren de optie **Oplossingen weergeven**.

   ![Selecteer in het overzichtsvenster 'Oplossingen weergeven'](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Selecteer **Onder Overzicht**de optie **Toevoegen**.

   ![Voeg in het overzichtsvenster nieuwe oplossing toe](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. Nadat de **Marketplace** is geopend, `logic apps management`voert u in het zoekvak **Logica-appsbeheer in**en selecteer .

   ![Selecteer in Marketplace 'Logic Apps Management'](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. Selecteer in het deelvenster oplossingsbeschrijving de optie **Maken**.

   ![Selecteer 'Maken' om de oplossing 'Logic Apps Management' toe te voegen](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Controleer en bevestig de werkruimte Log Analytics waar u de oplossing wilt installeren en selecteer Opnieuw **maken.**

   ![Selecteer 'Maken' voor 'Logic Apps Management'](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   Nadat Azure de oplossing heeft geïmplementeerd in de Azure-brongroep die uw Log Analytics-werkruimte bevat, wordt de oplossing weergegeven in het overzichtsvenster van uw werkruimte.

   ![Deelvenster Werkruimteoverzicht](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Azure Monitor-logboeken instellen

Wanneer u informatie over runtime-gebeurtenissen en gegevens opslaat in [Azure Monitor-logboeken,](../azure-monitor/platform/data-platform-logs.md)u [logboekquery's](../azure-monitor/log-query/log-query-overview.md) maken waarmee u deze informatie vinden en controleren.

1. Zoek en selecteer uw logische app in de [Azure-portal.](https://portal.azure.com)

1. Selecteer **diagnostische instellingen** > onder Controle onder **Controle****in**het menu van uw logische app .

   ![Selecteer onder 'Controleren' de optie 'Diagnostische instellingen' > 'Diagnostische instelling toevoegen'](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Voer de volgende stappen uit om de instelling te maken:

   1. Geef een naam op voor de instelling.

   1. Selecteer **Verzenden naar logboekanalyse**.

   1. Selecteer **voor Abonnement**het Azure-abonnement dat is gekoppeld aan uw Log Analytics-werkruimte.

   1. Selecteer **voor Log Analytics Workspace**de werkruimte die u wilt gebruiken.

   1. Selecteer **onder logboek**de categorie **WorkflowRuntime,** waarin de gebeurteniscategorie wordt opgegeven die u wilt opnemen.

   1. Als u alle statistieken wilt selecteren, selecteert u **AllMetrics**onder **metrische gegevens**.

   1. Selecteer **Opslaan** als u klaar bent.

   Bijvoorbeeld:

   ![Log Analytics-werkruimte en gegevens selecteren voor logboekregistratie](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Logica-app weergeven voert status uit

Nadat uw logische app is uitgevoerd, u de gegevens over deze uitvoeringen bekijken in uw Log Analytics-werkruimte.

1. Zoek en open uw Log Analytics-werkruimte in de [Azure-portal.](https://portal.azure.com)

1. Selecteer in het menu van uw werkruimte**logica-appsbeheer**voor **werkruimteoverzicht** > .

   ![Status en aantal logische apps uitvoeren](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Als de tegel Logic Apps Management niet onmiddellijk resultaten na een run weergeeft, probeert u **Vernieuwen** te selecteren of een korte tijd te wachten voordat u het opnieuw probeert.

   Hier worden uw logische app-uitvoeringen gegroepeerd op naam of op uitvoeringsstatus. Op deze pagina vindt u ook details over fouten in acties of triggers voor de logische app.

   ![Statusoverzicht voor uw logische app wordt uitgevoerd](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Als u alle uitvoeringen voor een specifieke logische app of -status wilt weergeven, selecteert u de rij voor die logische app of -status.

   Hier is een voorbeeld dat alle runs voor een specifieke logische app toont:

   ![Logica-app-uitvoeringen en status weergeven](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Voor acties waarbij u [bijgehouden eigenschappen instelt,](#extend-data)u deze eigenschappen ook weergeven door **Weergave** te selecteren in de kolom **Bijgehouden eigenschappen.** Als u de bijgehouden eigenschappen wilt doorzoeken, gebruikt u het kolomfilter.

   ![Bijgehouden eigenschappen voor een logische app weergeven](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Bijgehouden eigenschappen of voltooide gebeurtenissen kunnen 10-15 minuten vertraging oplopen voordat ze worden weergegeven in uw Log Analytics-werkruimte.
   > De mogelijkheid **opnieuw indienen** op deze pagina is momenteel ook niet beschikbaar.

1. Als u uw resultaten wilt filteren, u zowel client- als serverfiltering uitvoeren.

   * **Clientfilter:** Selecteer voor elke kolom de gewenste filters, bijvoorbeeld:

     ![Voorbeeldkolomfilters](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Serverfilter:** als u een specifiek tijdvenster wilt selecteren of het aantal runs wilt beperken dat wordt weergegeven, gebruikt u het bereikbesturingselement boven aan de pagina. Standaard worden er slechts 1.000 records tegelijk weergegeven.

     ![Het tijdvenster wijzigen](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Als u alle acties en hun gegevens voor een specifieke run wilt weergeven, selecteert u de rij voor een logische app-run.

   Hier is een voorbeeld dat alle acties en triggers voor een specifieke logische app-run weergeeft:

   ![Acties weergeven voor een logische app-run](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Diagnostische gegevens verzenden naar Azure Storage en Azure Event Hubs

Samen met Azure Monitor-logboeken u uitbreiden hoe u de diagnostische gegevens van uw logische app gebruikt met andere Azure-services, bijvoorbeeld:

* [Azure-bronlogboeken archiveren naar opslagaccount](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Azure-platformlogboeken streamen naar Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

U vervolgens realtime bewaking krijgen door telemetrie en analyses van andere services te gebruiken, zoals [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) en Power [BI.](../azure-monitor/platform/powerbi.md) Bijvoorbeeld:

* [Gegevens van gebeurtenishubs streamen naar Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream streaminggegevens met Stream Analytics en maak een realtime analysedashboard in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Op basis van de locaties waar u diagnostische gegevens wilt verzenden, moet u ervoor zorgen dat u eerst [een Azure-opslagaccount maakt](../storage/common/storage-create-storage-account.md) of [een Azure-gebeurtenishub maakt.](../event-hubs/event-hubs-create.md) U vervolgens de bestemmingen selecteren waar u die gegevens wilt verzenden. Bewaartermijnen zijn alleen van toepassing wanneer u een opslagaccount gebruikt.

![Gegevens verzenden naar Azure-opslagaccount of gebeurtenishub](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Diagnostische gebeurtenissen in Azure Monitor

Elke diagnostische gebeurtenis bevat details over uw logische app en die gebeurtenis, bijvoorbeeld de status, begintijd, eindtijd, enzovoort. Als u monitoring, tracking en logboekregistratie programmatisch wilt instellen, u deze informatie gebruiken met de [REST-API voor Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) en de [REST-API voor Azure Monitor.](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) U ook `clientTrackingId` `trackedProperties` de eigenschappen en eigenschappen gebruiken die worden weergegeven in 

* `clientTrackingId`: Als dit niet is verstrekt, genereert Azure deze id automatisch en correleert het gebeurtenissen in een logische app- run, inclusief geneste werkstromen die vanuit de logische app worden aangeroepen. U deze id handmatig opgeven in `x-ms-client-tracking-id` een trigger door een koptekst met de aangepaste ID-waarde in het triggerverzoek door te geven. U een trigger voor aanvragen, HTTP-trigger of webhook-trigger gebruiken.

* `trackedProperties`: Als u invoer of uitvoer in diagnostische `trackedProperties` gegevens wilt bijhouden, u een sectie aan een actie toevoegen met behulp van de Logic App Designer of rechtstreeks in de JSON-definitie van uw logische app. Bijgehouden eigenschappen kunnen slechts de ingangen en uitvoer van één `correlation` actie bijhouden, maar u de eigenschappen van gebeurtenissen gebruiken om te correleren tussen acties in een run. Als u meer dan één eigenschap, een `trackedProperties` of meer eigenschappen wilt bijhouden, voegt u de sectie en de eigenschappen die u wilt toevoegen aan de actiedefinitie.

  Hier volgt een voorbeeld dat laat zien hoe de definitie van variabele actie **initialiseren** bijgehouden eigenschappen van de invoer van de actie bevat waar de invoer een array is, niet een record.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  In dit voorbeeld worden meerdere bijgehouden eigenschappen weergegeven:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

In dit voorbeeld `ActionCompleted` ziet `clientTrackingId` u `trackedProperties` hoe de gebeurtenis de kenmerken en de kenmerken bevat:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Bewakings- en traceringsquery's maken](../logic-apps/create-monitoring-tracking-queries.md)
* [B2B-berichten bewaken met Azure Monitor-logboeken](../logic-apps/monitor-b2b-messages-log-analytics.md)
