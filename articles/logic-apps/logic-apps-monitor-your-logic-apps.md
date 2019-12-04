---
title: Status controleren, logboek registratie instellen en waarschuwingen ontvangen
description: Status controleren, diagnostische gegevens registreren en waarschuwingen instellen voor Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/21/2017
ms.openlocfilehash: ca1e2836ddfb38de640a5a90cf53e416b8923c04
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74791912"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>Status controleren, diagnostische logboek registratie instellen en waarschuwingen voor Azure Logic Apps inschakelen

Nadat u een [logische app hebt gemaakt en uitgevoerd](../logic-apps/quickstart-create-first-logic-app-workflow.md) kunt u de uitvoeringsgeschiedenis, triggergeschiedenis, status en prestaties ervan controleren. Voor real-time controle van gebeurtenissen en uitgebreidere fout opsporing stelt u [Diagnostische logboek registratie](#azure-diagnostics) in voor uw logische app. Op deze manier kunt u [gebeurtenissen vinden en bekijken](#find-events), zoals trigger-, uitvoerings-, en actiegebeurtenissen. U kunt deze [diagnostische gegevens ook gebruiken met andere services](#extend-diagnostic-data), zoals Azure Storage en Azure Event Hubs. 

Als u meldingen over mislukte of andere mogelijke problemen wilt ontvangen, stelt u [waarschuwingen](#add-azure-alerts)in. U kunt bijvoorbeeld een waarschuwing maken die detecteert dat er gedurende een uur meer dan vijf uitvoeringen mislukken. U kunt ook programmatisch bewaking, tracering en logboek registratie instellen met behulp van [Azure Diagnostics gebeurtenis instellingen en eigenschappen](#diagnostic-event-properties).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>Uitvoerings-en trigger geschiedenis voor uw logische app weer geven

1. Als u uw logische app wilt vinden in de [Azure Portal](https://portal.azure.com), kiest u in het hoofd menu van Azure **alle services**. Typ ' Logic apps ' in het zoekvak en kies **Logic apps**.

   ![Uw logische app zoeken](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   In de Azure Portal worden alle Logic apps weer gegeven die zijn gekoppeld aan uw Azure-abonnement. 

2. Selecteer uw logische app en kies vervolgens **overzicht**.

   De Azure Portal bevat de uitvoerings geschiedenis en trigger geschiedenis voor uw logische app. Bijvoorbeeld:

   ![Geschiedenis van uitvoeringen van logische apps en trigger geschiedenis](media/logic-apps-monitor-your-logic-apps/overview.png)

   * Met de **geschiedenis wordt** de uitvoering van de logische app weer gegeven. 
   * In de **trigger geschiedenis** worden alle trigger activiteiten voor uw logische app weer gegeven.

   Zie [problemen met uw logische app oplossen](../logic-apps/logic-apps-diagnosing-failures.md)voor status beschrijvingen.

   > [!TIP]
   > Als u de verwachte gegevens niet kunt vinden, kiest u op de werk balk **vernieuwen**.

3. Als u de stappen van een specifieke uitvoering wilt bekijken, selecteert u onder uitvoerings **geschiedenis**de uitvoering. 

   In de weer gave monitor wordt elke stap in die uitvoering weer gegeven. Bijvoorbeeld:

   ![Acties voor een specifieke uitvoering](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. Kies **Details uitvoeren**voor meer informatie over de uitvoering. Deze informatie bevat een overzicht van de stappen, status, invoer en uitvoer voor de uitvoering. 

   ![Kies Details uitvoeren](media/logic-apps-monitor-your-logic-apps/run-details.png)

   U kunt bijvoorbeeld de **correlatie-id**van het run ophalen, die u mogelijk nodig hebt wanneer u de [REST API gebruikt voor Logic apps](https://docs.microsoft.com/rest/api/logic).

5. Als u meer wilt weten over een specifieke stap, kiest u die stap. U kunt nu Details bekijken, zoals invoer, uitvoer en eventuele fouten die zijn opgetreden voor die stap. Bijvoorbeeld:

   ![Details van stap](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > Alle runtime Details en-gebeurtenissen zijn versleuteld in de Logic Apps-service. Ze worden alleen ontsleuteld wanneer een gebruiker vraagt om die gegevens weer te geven. U kunt ook de toegang tot deze gebeurtenissen beheren met [op rollen gebaseerde Access Control van Azure (RBAC)](../role-based-access-control/overview.md).

6. Ga terug naar het deel venster **overzicht** om meer informatie over een specifieke trigger gebeurtenis te krijgen. Selecteer onder **trigger geschiedenis**de trigger gebeurtenis. U kunt nu Details bekijken, zoals invoer en uitvoer, bijvoorbeeld:

   ![Uitvoer Details van trigger gebeurtenis](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>Diagnostische logboek registratie inschakelen voor uw logische app

Voor uitgebreidere fout opsporing met runtime Details en-gebeurtenissen kunt u diagnostische logboek registratie instellen met [Azure monitor-logboeken](../log-analytics/log-analytics-overview.md). Azure Monitor is een service in azure waarmee u uw Cloud-en on-premises omgevingen kunt bewaken om de beschik baarheid en prestaties te behouden. 

Voordat u begint, moet u beschikken over een Log Analytics-werk ruimte. Meer informatie [over het maken van een log Analytics-werk ruimte](../azure-monitor/learn/quick-create-workspace.md).

1. Zoek en selecteer uw logische app in de [Azure Portal](https://portal.azure.com). 

2. Klik in het menu Blade van de logische app onder **bewaking**op **diagnostische gegevens** > **Diagnostische instellingen**.

   ![Ga naar bewaking, diagnostische gegevens en diagnostische instellingen](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. Kies onder instellingen **voor** **Diagnostische gegevens**.

   ![Diagnostische logboeken inschakelen](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. Selecteer nu de Log Analytics werk ruimte en gebeurtenis categorie voor logboek registratie zoals weer gegeven:

   1. Selecteer **verzenden naar log Analytics**. 
   2. Klik onder **log Analytics**op **configureren**. 
   3. Onder **OMS-werk ruimten**selecteert u de werk ruimte die moet worden gebruikt voor logboek registratie.
      > [!NOTE]
      > OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.
   4. Selecteer onder **logboek**de categorie **WorkflowRuntime** .
   5. Kies het interval voor metrische gegevens.
   6. Als u bent klaar, kiest u **Opslaan**.

   ![Log Analytics werk ruimte en gegevens selecteren voor logboek registratie](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

U kunt nu gebeurtenissen en andere gegevens vinden voor trigger gebeurtenissen, gebeurtenissen uitvoeren en actie gebeurtenissen.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>Gebeurtenissen en gegevens voor uw logische app zoeken

Volg deze stappen om gebeurtenissen in uw logische app te zoeken en weer te geven, zoals trigger gebeurtenissen, uitvoer gebeurtenissen en actie gebeurtenissen.

1. Kies in het [Azure Portal](https://portal.azure.com) **alle services**. Zoek naar ' Log Analytics ' en kies **log Analytics** , zoals hier wordt weer gegeven:

   ![Kies Log Analytics](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. Zoek en selecteer de Log Analytics-werk ruimte onder **log Analytics**. 

   ![Uw Log Analytics-werk ruimte selecteren](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. Klik onder **beheer**op **Logboeken zoeken**.

   ![Kies zoeken in Logboeken](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. Geef in het zoekvak een veld op dat u wilt zoeken en druk op **Enter**. Wanneer u begint te typen, ziet u mogelijke overeenkomsten en bewerkingen die u kunt gebruiken. 

   Als u bijvoorbeeld de tien belangrijkste gebeurtenissen wilt vinden die zijn opgetreden, voert u deze zoek opdracht in en selecteert u deze: **Zoek categorie = = "WorkflowRuntime" | limiet 10**

   ![Zoek reeks opgeven](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   Meer informatie over [het zoeken naar gegevens in azure monitor-logboeken](../log-analytics/log-analytics-log-searches.md).

5. Kies op de pagina resultaten in de linker balk de periode die u wilt weer geven.
Als u de query wilt verfijnen door een filter toe te voegen, kiest u **+ toevoegen**.

   ![Tijds duur kiezen voor query resultaten](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. Voer onder **filters toevoegen**de naam van het filter in zodat u het gewenste filter kunt vinden. Selecteer het filter en kies **+ toevoegen**.

   In dit voor beeld wordt het woord ' status ' gebruikt om mislukte gebeurtenissen onder **AzureDiagnostics**te vinden.
   Hier is het filter voor **status_s** al geselecteerd.

   ![Filter selecteren](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. Selecteer in de linker balk de filter waarde die u wilt gebruiken en klik **op Toep assen**.

   ![Selecteer filter waarde, kies Toep assen](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. Ga nu terug naar de query die u wilt maken. Uw query is bijgewerkt met uw geselecteerde filter en waarde. Uw vorige resultaten worden nu ook gefilterd.

   ![Teruggaan naar uw query met gefilterde resultaten](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. Klik op **Opslaan**om uw query op te slaan voor toekomstig gebruik. Meer informatie [over het opslaan van uw query](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query).

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Uitbreiden hoe en waar u Diagnostische gegevens met andere services gebruikt

Naast Azure Monitor-Logboeken kunt u uitbreiden hoe u de diagnostische gegevens van uw logische app gebruikt met andere Azure-Services, bijvoorbeeld: 

* [Azure Diagnostics-logboeken archiveren in Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure Diagnostics logboeken streamen naar Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 

U kunt vervolgens realtime-bewaking krijgen door telemetrie en analyses uit andere services te gebruiken, zoals [Azure stream Analytics](../stream-analytics/stream-analytics-introduction.md) en [Power bi](../azure-monitor/platform/powerbi.md). Bijvoorbeeld:

* [Gegevens streamen van Event Hubs naar Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analyseer streaminggegevens met Stream Analytics en maak een real-time analyse dashboard in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Op basis van de opties die u wilt instellen, moet u ervoor zorgen dat u eerst [een Azure-opslag account maakt](../storage/common/storage-create-storage-account.md) of [een Azure-Event hub maakt](../event-hubs/event-hubs-create.md). Selecteer vervolgens de opties waarvoor u de diagnostische gegevens wilt verzenden:

![Gegevens verzenden naar een Azure Storage-account of Event Hub](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> De Bewaar periode is alleen van toepassing wanneer u ervoor kiest om een opslag account te gebruiken.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>Waarschuwingen instellen voor uw logische app

Als u specifieke metrische gegevens of drempel waarden voor uw logische app wilt bewaken, stelt u [in azure waarschuwingen in](../azure-monitor/platform/alerts-overview.md). Meer informatie over [metrische gegevens in azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md). 

Voer de volgende stappen uit om waarschuwingen in te stellen zonder [Azure monitor logboeken](../log-analytics/log-analytics-overview.md). [Stel Azure monitor-logboeken](#azure-diagnostics) ook in voor meer geavanceerde waarschuwings criteria en-acties.

1. Kies in het menu Blade van de logische app onder **controle**de optie **diagnostische gegevens** > **waarschuwings regels** > **waarschuwing toevoegen** , zoals hier wordt weer gegeven:

   ![Een waarschuwing voor uw logische app toevoegen](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. Maak uw waarschuwing op de Blade **een waarschuwings regel toevoegen** , zoals wordt weer gegeven:

   1. Selecteer onder **resource**de logische app, als deze nog niet is geselecteerd. 
   2. Geef een naam en beschrijving op voor de waarschuwing.
   3. Selecteer een **metriek** of gebeurtenis die u wilt bijhouden.
   4. Selecteer een **voor waarde**, geef een **drempel waarde** op voor de metriek en selecteer de **periode** voor het bewaken van deze metriek.
   5. Selecteer of u een e-mail wilt verzenden voor de waarschuwing. 
   6. Geef andere e-mail adressen op voor het verzenden van de waarschuwing. 
   U kunt ook een webhook-URL opgeven waarnaar u de waarschuwing wilt verzenden.

   Deze regel verzendt bijvoorbeeld een waarschuwing wanneer vijf of meer uitvoeringen in een uur mislukken:

   ![Waarschuwings regel voor metrische gegevens maken](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> Als u een logische app wilt uitvoeren vanuit een waarschuwing, kunt u de [trigger voor aanvragen](../connectors/connectors-native-reqres.md) toevoegen aan uw werk stroom, waarmee u taken zoals deze voor beelden kunt uitvoeren:
> 
> * [Post naar toegestane vertraging](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Tekst verzenden](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Een bericht aan een wachtrij toevoegen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Instellingen en Details van de Azure Diagnostics gebeurtenis

Elke diagnostische gebeurtenis heeft details over uw logische app en die gebeurtenis, bijvoorbeeld de status, start tijd, eind tijd, enzovoort. Voor het programmatisch instellen van bewaking, tracering en logboek registratie kunt u deze gegevens gebruiken met de [rest API voor Azure Logic apps](https://docs.microsoft.com/rest/api/logic) en de [rest API voor Azure Diagnostics](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows).

De `ActionCompleted` gebeurtenis heeft bijvoorbeeld de `clientTrackingId`-en `trackedProperties`-eigenschappen die u kunt gebruiken voor het volgen en bewaken van:

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
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
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
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
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: als u dit niet opgeeft, wordt deze ID automatisch door Azure gegenereerd en worden gebeurtenissen gecorreleerd in een logische app-uitvoering, inclusief geneste werk stromen die vanuit de logische app worden aangeroepen. U kunt deze ID hand matig opgeven vanuit een trigger door een `x-ms-client-tracking-id`-header door te geven met uw aangepaste ID-waarde in de trigger-aanvraag. U kunt een trigger voor een aanvraag, een HTTP-trigger of een webhook gebruiken.

* `trackedProperties`: als u invoer of uitvoer in diagnostische gegevens wilt volgen, kunt u bijgehouden eigenschappen toevoegen aan acties in de JSON-definitie van de logische app. Bijgehouden eigenschappen kunnen alleen de invoer en uitvoer van één actie bijhouden, maar u kunt de `correlation` eigenschappen van gebeurtenissen gebruiken om te correleren over acties in een uitvoering.

  Als u een of meer eigenschappen wilt volgen, voegt u de sectie `trackedProperties` en de gewenste eigenschappen toe aan de definitie van de actie. Stel dat u gegevens wilt bijhouden zoals een ' Order-ID ' in uw telemetrie:

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```
  Hier volgt nog een voor beeld van het gebruik van de actie **variabele initialiseren** . In het voor beeld worden bijgehouden eigenschappen van de invoer van de actie toegevoegd, waarbij de invoer een matrix is, niet een record.  

  ``` json
  "actions": { 
   "Initialize_variable": { 
      "inputs": { 
         "variables": [{ 
            "name": "ConnectorName", 
            "type": "String", 
            "value": "SFTP-SSH" 
         }]
      },
      "runAfter": {},
      "trackedProperties": { 
         "Track1": "@action().inputs.variables[0].value"
      },
      "type": "InitializeVariable"
   } 
  }
  ```

## <a name="next-steps"></a>Volgende stappen

* [Implementatie van logische apps automatiseren](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [B2B-scenario's met Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B-berichten bewaken](../logic-apps/logic-apps-monitor-b2b-message.md)
