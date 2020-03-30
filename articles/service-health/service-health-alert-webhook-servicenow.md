---
title: Azure-servicestatuswaarschuwingen verzenden met ServiceNow
description: Ontvang gepersonaliseerde meldingen over servicestatusgebeurtenissen naar uw ServiceNow-exemplaar.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654100"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Azure-servicestatuswaarschuwingen verzenden met ServiceNow met behulp van webhooks

In dit artikel ziet u hoe u Azure-servicestatuswaarschuwingen integreren met ServiceNow met behulp van een webhook. Nadat u webhook-integratie hebt ingesteld met uw ServiceNow-exemplaar, ontvangt u waarschuwingen via uw bestaande meldingsinfrastructuur wanneer azure-serviceproblemen van invloed zijn op u. Elke keer dat een Azure Service Health-waarschuwing wordt geactiveerd, wordt een webhook aanroept via de Api voor scripte rest van ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Een gescripte REST-API maken in ServiceNow

1.  Zorg ervoor dat je je hebt aangemeld en je bent aangemeld bij je [ServiceNow-account.](https://www.servicenow.com/)

1.  Navigeer naar de sectie **Systeemwebservices** in ServiceNow en selecteer **API's voor gescripte REST.**

    ![De sectie 'Scripted Web Service' in ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selecteer **Nieuw** om een nieuwe Scripted REST-service te maken.
 
    ![De knop 'Nieuwe API voor gescripte REST' in ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Voeg een **naam** toe aan uw REST-API en stel de **API-id** in op `azureservicehealth`.

1.  Selecteer **Indienen**.

    ![De "REST API-instellingen" in ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selecteer de REST-API die u hebt gemaakt en selecteer onder het tabblad **Resources** **Nieuw**.

    ![Het tabblad 'Resource' in ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Geef** uw `event` nieuwe resource een `POST`naam en wijzig de **HTTP-methode** in .

1.  Voeg in de sectie **Script** de volgende JavaScript-code toe:

    >[!NOTE]
    >U moet de `<secret>``<group>`waarde `<email>` en de waarde in het onderstaande script bijwerken.
    >* `<secret>`moet een willekeurige string zijn, zoals een GUID
    >* `<group>`moet de ServiceNow-groep zijn waaraan u het incident wilt toewijzen
    >* `<email>`moet de specifieke persoon zijn aan wie u het incident wilt toewijzen (optioneel)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Schakel op het tabblad Beveiliging het selectievakje **Verificatie vereist uit** en selecteer **Verzenden**. De `<secret>` ingestelde u beschermt deze API in plaats daarvan.

    ![Het selectievakje 'Verificatie vereist' in ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Terug in de sectie Api's voor gescripte REST vindt u het **BasisAPI-pad** voor uw nieuwe REST-API:

     ![Het 'Basis-API-pad' in ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  De volledige URL voor integratie ziet eruit als volgt:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Een waarschuwing maken met ServiceNow in de Azure-portal
### <a name="for-a-new-action-group"></a>Voor een nieuwe actiegroep:
1. Volg de stappen 1 tot en met 8 in [dit artikel](../azure-monitor/platform/alerts-activity-log-service-notifications.md) om een waarschuwing te maken met een nieuwe actiegroep.

1. Definiëren in de lijst **met acties:**

    a. **Actietype:** *Webhook*

    b. **Details:** De **URL servicenu-integratie** die u eerder hebt opgeslagen.

    c. **Naam:** De naam, alias of id van Webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de waarschuwing te maken.

### <a name="for-an-existing-action-group"></a>Voor een bestaande actiegroep:
1. Selecteer **Monitor**in de [Azure-portal](https://portal.azure.com/).

1. Selecteer **in** de sectie Instellingen de optie **Actiegroepen**.

1. Zoek en selecteer de actiegroep die u wilt bewerken.

1. Toevoegen aan de lijst **met acties:**

    a. **Actietype:** *Webhook*

    b. **Details:** De **URL servicenu-integratie** die u eerder hebt opgeslagen.

    c. **Naam:** De naam, alias of id van Webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de actiegroep bij te werken.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Uw webhook-integratie testen via een HTTP POST-verzoek
1. Maak de payload servicestatus die u wilt verzenden. U een voorbeeld service status webhook payload vinden bij [Webhooks voor Azure activity log alerts](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. U moet `200 OK` een antwoord ontvangen met het bericht 'Incident gemaakt'.

1. Ga naar [ServiceNow](https://www.servicenow.com/) om te bevestigen dat uw integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van webhookmeldingen voor bestaande probleembeheersystemen](service-health-alert-webhook-guide.md).
- Bekijk het [webhookschema voor de waarschuwing voor activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Meer informatie over [meldingen over servicestatus](../azure-monitor/platform/service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/platform/action-groups.md).