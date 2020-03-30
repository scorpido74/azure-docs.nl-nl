---
title: Azure-servicestatuswaarschuwingen verzenden met PagerDuty
description: Ontvang gepersonaliseerde meldingen over servicestatusgebeurtenissen naar uw PagerDuty-exemplaar.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: bb449a5279f3cea55e6aec2f72edfd11fb26227a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654066"
---
# <a name="send-azure-service-health-alerts-with-pagerduty-using-webhooks"></a>Azure-servicestatuswaarschuwingen verzenden met PagerDuty via webhooks

In dit artikel ziet u hoe u Azure-servicestatusmeldingen via PagerDuty instellen met behulp van een webhook. Door het aangepaste Microsoft Azure-integratietype van [PagerDuty](https://www.pagerduty.com/)te gebruiken, u moeiteloos servicestatuswaarschuwingen toevoegen aan uw nieuwe of bestaande PagerDuty-services.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Een URL voor servicestatusintegratie maken in PagerDuty
1.  Zorg ervoor dat je je hebt aangemeld en je bent aangemeld bij je [PagerDuty-account.](https://www.pagerduty.com/)

1.  Navigeer naar de sectie **Services** in PagerDuty.

    ![De sectie 'Services' in PagerDuty](./media/webhook-alerts/pagerduty-services-section.png)

1.  Selecteer **Nieuwe service toevoegen** of open een bestaande service die u hebt ingesteld.

1.  Selecteer in de **integratie-instellingen**het volgende:

    a. **Integratietype:** Microsoft Azure

    b. **Integratienaam** \<: Naam\>

    ![De "Integratie-instellingen" in PagerDuty](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Vul alle andere vereiste velden in en selecteer **Toevoegen**.

1.  Open deze nieuwe integratie en kopieer en sla de **URL integratie**op.

    ![De "URL voor integratie" in PagerDuty](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Een waarschuwing maken met PagerDuty in de Azure-portal
### <a name="for-a-new-action-group"></a>Voor een nieuwe actiegroep:
1. Volg de stappen 1 tot en met 8 in [Een waarschuwing maken voor een servicestatusmelding voor een nieuwe actiegroep met behulp van de Azure-portal.](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

1. Definiëren in de lijst **met acties:**

    a. **Actietype:** *Webhook*

    b. **Details:** De URL van **PagerDuty-integratie** die u eerder hebt opgeslagen.

    c. **Naam:** De naam, alias of id van Webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de waarschuwing te maken.

### <a name="for-an-existing-action-group"></a>Voor een bestaande actiegroep:
1. Selecteer **Monitor**in de [Azure-portal](https://portal.azure.com/).

1. Selecteer **in** de sectie Instellingen de optie **Actiegroepen**.

1. Zoek en selecteer de actiegroep die u wilt bewerken.

1. Toevoegen aan de lijst **met acties:**

    a. **Actietype:** *Webhook*

    b. **Details:** De URL van **PagerDuty-integratie** die u eerder hebt opgeslagen.

    c. **Naam:** De naam, alias of id van Webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de actiegroep bij te werken.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Uw webhook-integratie testen via een HTTP POST-verzoek
1. Maak de payload servicestatus die u wilt verzenden. U een voorbeeld service status webhook payload vinden bij [Webhooks voor Azure activity log alerts](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Je moet `202 Accepted` een bericht ontvangen met een bericht met je 'gebeurtenis-id'.

1. Ga naar [PagerDuty](https://www.pagerduty.com/) om te controleren of uw integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van webhookmeldingen voor bestaande probleembeheersystemen](service-health-alert-webhook-guide.md).
- Bekijk het [webhookschema voor de waarschuwing voor activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Meer informatie over [meldingen over servicestatus](../azure-monitor/platform/service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/platform/action-groups.md).