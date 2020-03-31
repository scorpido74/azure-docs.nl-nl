---
title: Azure-servicestatuswaarschuwingen verzenden met OpsGenie via webhooks
description: Ontvang gepersonaliseerde meldingen over servicestatusgebeurtenissen naar uw OpsGenie-exemplaar.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: def12d5e7b1b93b8370cd7be61538fca53531ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654134"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Azure-servicestatuswaarschuwingen verzenden met OpsGenie via webhooks

In dit artikel ziet u hoe u Azure-servicestatuswaarschuwingen met OpsGenie instellen met behulp van een webhook. Met de Azure Service Health Integration van [OpsGenie](https://www.opsgenie.com/)u Azure Service Health-waarschuwingen doorsturen naar OpsGenie. OpsGenie kan de juiste mensen bepalen om te melden op basis van vluchtschema's, met behulp van e-mail, sms,sms, telefoongesprekken, iOS-& Android-pushmeldingen en escalerende waarschuwingen totdat de waarschuwing is erkend of gesloten.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Url van servicestatusintegratie maken in OpsGenie
1.  Zorg ervoor dat je je hebt aangemeld en je bent aangemeld bij je [OpsGenie](https://www.opsgenie.com/) account.

1.  Navigeer naar de sectie **Integraties** in OpsGenie.

    ![De sectie "Integraties" in OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Selecteer de knop **Azure Service Health-integratie.**

    ![De knop 'Azure Service Health' in OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Geef** uw waarschuwing een naam en geef het veld **Toegewezen aan team** op.

1.  Vul de andere velden **in,** zoals Geadresseerden , **Ingeschakeld**en **Meldingen onderdrukken**.

1.  Kopieer en sla de **URL integratie** `apiKey` op, die al uw toegevoegd aan het einde zou moeten bevatten.

    ![De "Integratie URL" in OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Integratie **opslaan selecteren**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Een waarschuwing maken met OpsGenie in de Azure-portal
### <a name="for-a-new-action-group"></a>Voor een nieuwe actiegroep:
1. Volg de stappen 1 tot en met 8 in [Een waarschuwing maken voor een servicestatusmelding voor een nieuwe actiegroep met behulp van de Azure-portal.](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

1. Definiëren in de lijst **met acties:**

    a. **Actietype:** *Webhook*

    b. **Details:** De URL **van OpsGenie-integratie die** u eerder hebt opgeslagen.

    c. **Naam:** De naam, alias of id van Webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de waarschuwing te maken.

### <a name="for-an-existing-action-group"></a>Voor een bestaande actiegroep:
1. Selecteer **Monitor**in de [Azure-portal](https://portal.azure.com/).

1. Selecteer **in** de sectie Instellingen de optie **Actiegroepen**.

1. Zoek en selecteer de actiegroep die u wilt bewerken.

1. Toevoegen aan de lijst **met acties:**

    a. **Actietype:** *Webhook*

    b. **Details:** De URL **van OpsGenie-integratie die** u eerder hebt opgeslagen.

    c. **Naam:** De naam, alias of id van Webhook.

1. Selecteer **Opslaan** wanneer u klaar bent om de actiegroep bij te werken.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Uw webhook-integratie testen via een HTTP POST-verzoek
1. Maak de payload servicestatus die u wilt verzenden. U een voorbeeld service status webhook payload vinden bij [Webhooks voor Azure activity log alerts](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Maak als volgt een HTTP POST-aanvraag:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. U moet `200 OK` een antwoord ontvangen met het bericht van status 'geslaagd'.

1. Ga naar [OpsGenie](https://www.opsgenie.com/) om te bevestigen dat uw integratie is ingesteld.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [configureren van webhookmeldingen voor bestaande probleembeheersystemen](service-health-alert-webhook-guide.md).
- Bekijk het [webhookschema voor de waarschuwing voor activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Meer informatie over [meldingen over servicestatus](../azure-monitor/platform/service-notifications.md).
- Meer informatie over [actiegroepen](../azure-monitor/platform/action-groups.md).