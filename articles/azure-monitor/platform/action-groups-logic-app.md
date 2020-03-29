---
title: Complexe acties activeren met Azure Monitor-waarschuwingen
description: Meer informatie over het maken van een actie voor logische apps om Azure Monitor-waarschuwingen te verwerken.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206233"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Complexe acties activeren met Azure Monitor-waarschuwingen

In dit artikel ziet u hoe u een logische app instelt en activeert om een gesprek in Microsoft Teams te maken wanneer een waarschuwing wordt geactiveerd.

## <a name="overview"></a>Overzicht

Wanneer een Azure Monitor-waarschuwing wordt geactiveerd, wordt een [actiegroep aanroept.](../../azure-monitor/platform/action-groups.md) Met actiegroepen u een of meer acties activeren om anderen op de hoogte te stellen van een waarschuwing en deze ook te verhelpen.

Het algemene proces is:

-   Maak de logische app voor het betreffende waarschuwingstype.

-   Importeer een voorbeeldpayload voor het betreffende waarschuwingstype in de logische app.

-   Definieer het gedrag van de logische app.

-   Kopieer het HTTP-eindpunt van de logische app naar een Azure-actiegroep.

Het proces is vergelijkbaar als u wilt dat de logische app een andere actie uitvoert.

## <a name="create-an-activity-log-alert-administrative"></a>Een waarschuwing voor een activiteitenlogboek maken: Beheer

1.  Selecteer in de Azure-portal **Een resource maken** in de linkerbovenhoek.

2.  Zoeken naar en selecteer **Logic App**en selecteer vervolgens **Maken**.

3.  Geef uw logische app een **naam,** kies een **resourcegroep,** enzovoort.

    ![Een logische app maken](media/action-groups-logic-app/create-logic-app-dialog.png "Een logische app maken")

4.  Selecteer **Maken** om de logische app te maken. Een pop-upbericht geeft aan dat de logische app is gemaakt. Selecteer **Resource starten** om de Logic Apps Designer te **openen**.

5.  Selecteer de trigger: **Wanneer een HTTP-aanvraag is ontvangen.**

    ![Triggers voor logische app](media/action-groups-logic-app/logic-app-triggers.png "Triggers voor logische app")

6.  Selecteer **Bewerken** om de HTTP-aanvraagtrigger te wijzigen.

    ![HTTP-aanvraagtriggers](media/action-groups-logic-app/http-request-trigger-shape.png "HTTP-aanvraagtriggers")

7.  Selecteer **Voorbeeldnettolading om een schema te genereren**.

    ![Gebruik een monsterpayload](media/action-groups-logic-app/use-sample-payload-button.png "Gebruik een monsterpayload")

8.  Kopieer en plak het volgende voorbeeldpayload in het dialoogvenster:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. In **de Logic App Designer** wordt een pop-upvenster weergegeven om u eraan te herinneren dat het verzoek dat naar de logische app wordt verzonden, de koptekst **Inhoudstype** moet instellen op **toepassing/json**. Sluit het pop-upvenster. Met de azure monitor-waarschuwing wordt de koptekst ingesteld.

    ![De koptekst Inhoudstype instellen](media/action-groups-logic-app/content-type-header.png "De koptekst Inhoudstype instellen")

10. Selecteer **+** **Nieuwe stap** en kies Een actie **toevoegen**.

    ![Een actie toevoegen](media/action-groups-logic-app/add-action.png "Een actie toevoegen")

11. Zoek naar en selecteer de Microsoft Teams-connector. Kies de actie **Microsoft Teams - Berichten plaatsen.**

    ![Acties van Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Acties van Microsoft Teams")

12. Configureer de actie Microsoft Teams. De **Logic Apps Designer** vraagt u om te verifiëren naar uw Office 365-account. Kies de **team-id** en **kanaal-id** waarnaar u het bericht wilt verzenden.

13. Configureer het bericht met behulp van een \<combinatie\> van statische tekst en verwijzingen naar de velden in de dynamische inhoud. Kopieer en plak de volgende tekst in het veld **Bericht:**

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Zoek en vervang \<vervolgens\> de velden door dynamische inhoudstags met dezelfde naam.

    > [!NOTE]
    > Er zijn twee dynamische velden met de naam **status**. Voeg beide velden toe aan het bericht. Gebruik het veld in de eigenschappenzak **activityLog** en verwijder het andere veld. Plaats de cursor boven het **statusveld** om de volledig gekwalificeerde veldverwijzing te bekijken, zoals in de volgende schermafbeelding wordt weergegeven:

    ![Microsoft Teams-actie: een bericht plaatsen](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams-actie: een bericht plaatsen")

14. **Selecteer** Opslaan om uw logica-app op te slaan boven aan de **Logic Apps Designer.**

15. Open uw bestaande actiegroep en voeg een actie toe om naar de logische app te verwijzen. Zie [Actiegroepen maken en beheren in de Azure-portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) om er een te maken als u geen bestaande actiegroep hebt. Vergeet niet om uw wijzigingen op te slaan.

    ![De actiegroep bijwerken](media/action-groups-logic-app/update-action-group.png "De actiegroep bijwerken")

De volgende keer dat een waarschuwing uw actiegroep aanroept, wordt uw logische app aangeroepen.

## <a name="create-a-service-health-alert"></a>Een servicestatuswaarschuwing maken

Azure Service Health-vermeldingen maken deel uit van het activiteitenlogboek. Het proces voor het maken van de waarschuwing is vergelijkbaar met [het maken van een waarschuwing voor het activiteitenlogboek,](#create-an-activity-log-alert-administrative)maar met een paar wijzigingen:

- Stap 1 tot en met 7 zijn hetzelfde.
- Gebruik voor stap 8 de volgende voorbeeldpayload voor de HTTP-aanvraagtrigger:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Stap 9 en 10 zijn hetzelfde.
-  Gebruik voor de stappen 11 tot en met 14 het volgende proces:

   1. Selecteer **+** **Nieuwe stap** en kies Een voorwaarde **toevoegen**. Stel de volgende voorwaarden in, zodat de logische app alleen wordt uitgevoerd wanneer de invoergegevens overeenkomen met de onderstaande waarden.  Wanneer u de versiewaarde in het tekstvak invoert, plaatst u er citaten omheen ("0.1.1") om ervoor te zorgen dat deze wordt geëvalueerd als een tekenreeks en niet als een numeriek type.  Het systeem geeft de aanhalingstekens niet weer als u terugkeert naar de pagina, maar de onderliggende code behoudt nog steeds het tekenreekstype.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health payload conditie"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health payload conditie")

   1. Volg in de **ware** voorwaarde de instructies in de stappen 11 tot en met 13 in [Een waarschuwing voor een activiteitenlogboek maken](#create-an-activity-log-alert-administrative) om de actie Microsoft Teams toe te voegen.

   1. Definieer het bericht met behulp van een combinatie van HTML en dynamische inhoud. Kopieer en plak de volgende inhoud in het veld **Bericht.** Vervang `[incidentType]`de `[trackingID]` `[title]`velden `[communication]` , en de velden door dynamische inhoudstags met dezelfde naam:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health ware toestand na actie"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Service Gezondheid ware toestand na actie")

   1. Geef voor de **foute** voorwaarde Als een handig bericht op:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health valse toestand na actie"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Service Gezondheid valse toestand na actie")

- Stap 15 is hetzelfde. Volg de instructies om uw logica-app op te slaan en uw actiegroep bij te werken.

## <a name="create-a-metric-alert"></a>Een metrische waarschuwing maken

Het proces voor het maken van een metrische waarschuwing is vergelijkbaar met [het maken van een waarschuwing voor het activiteitslogboek,](#create-an-activity-log-alert-administrative)maar met een paar wijzigingen:

- Stap 1 tot en met 7 zijn hetzelfde.
- Gebruik voor stap 8 de volgende voorbeeldpayload voor de HTTP-aanvraagtrigger:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Stap 9 en 10 zijn hetzelfde.
- Gebruik voor de stappen 11 tot en met 14 het volgende proces:

  1. Selecteer **+** **Nieuwe stap** en kies Een voorwaarde **toevoegen**. Stel de volgende voorwaarden in, zodat de logische app alleen wordt uitgevoerd wanneer de invoergegevens overeenkomen met deze waarden hieronder. Wanneer u de versiewaarde in het tekstvak invoert, plaatst u er citaten omheen ("2.0") om ervoor te zorgen dat deze wordt geëvalueerd als een tekenreeks en niet als een numeriek type.  Het systeem geeft de aanhalingstekens niet weer als u terugkeert naar de pagina, maar de onderliggende code behoudt nog steeds het tekenreekstype. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Metrische waarschuwingspayload voorwaarde"](media/action-groups-logic-app/metric-alert-payload-condition.png "Metrische waarschuwingslaadtoestand voorwaarde")

  1. Voeg in de **als werkelijke** voorwaarde een **voor elke** lus en de actie Microsoft Teams toe. Definieer het bericht met behulp van een combinatie van HTML en dynamische inhoud.

      !['Metrische waarschuwing true condition post action'](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Actie voor true condition-functie met metrische waarschuwing")

  1. Definieer in de **foute** voorwaarde Als een microsoft teams-actie om te communiceren dat de metrische waarschuwing niet overeenkomt met de verwachtingen van de logische app. Inclusief de JSON payload. Zie hoe u `triggerBody` verwijst naar `json()` de dynamische inhoud in de expressie.

      !["Metrische waarschuwing valse voorwaarde na actie"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Metrische waarschuwing valse voorwaarde na actie")

- Stap 15 is hetzelfde. Volg de instructies om uw logica-app op te slaan en uw actiegroep bij te werken.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Andere toepassingen bellen naast Microsoft Teams
Logic Apps heeft een aantal verschillende connectors waarmee u acties activeren in een breed scala aan toepassingen en databases. Slack, SQL Server, Oracle, Salesforce zijn slechts enkele voorbeelden. Zie [Logic App-connectors](../../connectors/apis-list.md)voor meer informatie over connectors.  

## <a name="next-steps"></a>Volgende stappen
* Krijg een [overzicht van azure activity log alerts](../../azure-monitor/platform/alerts-overview.md) en leer hoe u waarschuwingen ontvangt.  
* Meer informatie over het [configureren van waarschuwingen wanneer een Azure Service Health-melding wordt geplaatst.](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)
* Meer informatie over [actiegroepen](../../azure-monitor/platform/action-groups.md).

