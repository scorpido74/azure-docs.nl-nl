---
title: Azure Service Health-meldingen verzenden via webhooks
description: Stuur gepersonaliseerde meldingen over servicestatusgebeurtenissen naar uw bestaande probleembeheersysteem.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 2609a267bd151354f83482ab16c4b9345aa88cc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062858"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Een webhook gebruiken om statusmeldingen voor probleembeheersystemen te configureren

In dit artikel ziet u hoe u Azure Service Health-waarschuwingen configureert om gegevens via webhooks naar uw bestaande meldingssysteem te verzenden.

U waarschuwingen voor servicestatus configureren om u per sms of e-mail op de hoogte te stellen wanneer een Azure-service-incident u beïnvloedt.

Maar misschien beschikt u al over een bestaand extern meldingssysteem dat u het liefst gebruikt. Dit artikel identificeert de belangrijkste onderdelen van de webhook payload. En het beschrijft hoe u aangepaste waarschuwingen maken om u op de hoogte te stellen wanneer relevante serviceproblemen optreden.

Als u een vooraf geconfigureerde integratie wilt gebruiken, raadpleegt u:
* [Waarschuwingen configureren met ServiceNow](service-health-alert-webhook-servicenow.md)
* [Waarschuwingen configureren met PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Waarschuwingen configureren met OpsGenie](service-health-alert-webhook-opsgenie.md)

**Bekijk een inleidende video:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Een aangepaste melding configureren met behulp van de webhook-payload ServiceHealth
Om uw eigen aangepaste webhook-integratie in te stellen, moet u de JSON-payload ontleden die via de servicestatusmelding wordt verzonden.

Zie [een voorbeeld](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` webhook payload.

U bevestigen dat het een service `context.eventSource == "ServiceHealth"`status waarschuwing door te kijken naar. De volgende eigenschappen zijn het meest relevant:
- **data.context.activityLog.status**
- **data.context.activityLog.level**
- **data.context.activityLog.subscriptionId**
- **data.context.activityLog.properties.title**
- **data.context.activityLog.properties.impactStartTime**
- **data.context.activityLog.properties.communication**
- **data.context.activityLog.properties.impactedServices**
- **data.context.activityLog.properties.trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Een koppeling maken naar het dashboard Servicestatus voor een incident
U een directe koppeling maken naar uw servicestatusdashboard op een desktop of mobiel apparaat door een gespecialiseerde URL te genereren. Gebruik de *trackingId* en de eerste drie en laatste drie cijfers van uw *abonnementId* in deze indeling:

https<i></i>://app.azure.com/h/*&lt;&gt;trackingId*/*&lt;eerste drie en&gt; laatste drie cijfers van abonnementId*

Als uw *abonnementId* bijvoorbeeld bba14129-e895-429b-8809-278e836ecdb3 is en uw *trackingId* 0DET-URB is, is uw URL voor servicestatus:

https<i></i>://app.azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Het niveau gebruiken om de ernst van het probleem te detecteren
Van de laagste tot de hoogste ernst, het **niveau** eigenschap in de payload kan *informatief,* *waarschuwing*, *fout*, of *kritisch*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>De getroffen services ontschepen om de omvang van het incident te bepalen
Waarschuwingen voor servicestatus kunnen u informeren over problemen in meerdere regio's en services. Om volledige details te krijgen, moet u `impactedServices`de waarde van .

De inhoud die erin zit is een ontsnapte [JSON-tekenreeks](https://json.org/) die, wanneer deze niet ontsnapt is, een ander JSON-object bevat dat regelmatig kan worden ontleed. Bijvoorbeeld:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Wordt:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

In dit voorbeeld worden problemen weergegeven voor:
- "Alerts & Metrics" in Australië Oost en Australië Zuidoost.
- "App Service" in Australië Zuidoost.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Test uw webhook-integratie via een HTTP POST-aanvraag

Volg deze stappen:

1. Maak de payload servicestatus die u wilt verzenden. Zie een voorbeeld service status webhook payload bij [Webhooks voor Azure activity log alerts](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   U ontvangt een "2XX - Succesvol" antwoord.

1. Ga naar [PagerDuty](https://www.pagerduty.com/) om te controleren of uw integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Bekijk het [webhookschema voor de waarschuwing voor activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Meer informatie over [meldingen over servicestatus](../azure-monitor/platform/service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/platform/action-groups.md).