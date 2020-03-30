---
title: Meldingen van activiteitenlogboeken ontvangen op Azure-servicemeldingen
description: Ontvang een melding via sms, e-mail of webhook wanneer Azure-service plaatsvindt.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749321"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Waarschuwingen voor activiteitenlogboek maken bij servicemeldingen
## <a name="overview"></a>Overzicht

In dit artikel ziet u hoe u waarschuwingen voor activiteitslogboeken instelt voor servicestatusmeldingen met behulp van de Azure-portal.  

Servicestatusmeldingen worden opgeslagen in het [Azure-activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md) Gezien de mogelijk grote hoeveelheid informatie die is opgeslagen in het activiteitenlogboek, is er een aparte gebruikersinterface om het eenvoudiger te maken om meldingen over servicestatusmeldingen te bekijken en in te stellen. 

U een waarschuwing ontvangen wanneer Azure servicestatusmeldingen naar uw Azure-abonnement verzendt. U de waarschuwing configureren op basis van:

- De klasse van service status kennisgeving (Service problemen, Gepland onderhoud, Gezondheid advisories).
- Het abonnement beïnvloed.
- De service(s) beïnvloed.
- De regio(s) getroffen.

> [!NOTE]
> Meldingen van servicestatus sturen geen waarschuwing met betrekking tot gebeurtenissen in de resourcestatus.

U ook configureren naar wie de waarschuwing moet worden verzonden:

- Selecteer een bestaande actiegroep.
- Maak een nieuwe actiegroep (die kan worden gebruikt voor toekomstige waarschuwingen).

Zie Actiegroepen maken en beheren voor meer informatie over [actiegroepen.](../azure-monitor/platform/action-groups.md)

Zie [Resourcebeheersjablonen](../azure-monitor/platform/alerts-activity-log.md)voor informatie over het configureren van waarschuwingen voor servicestatusmeldingen met azure resourcebeheersjablonen.

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Bekijk een video over het instellen van uw eerste Azure Service Health-waarschuwing

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Waarschuwing en nieuwe actiegroep met Azure-portal
1. Selecteer **Servicestatus**in de [portal](https://portal.azure.com).

    ![De dienst "Service Health"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Selecteer **gezondheidwaarschuwingen** in de sectie **Waarschuwingen**.

    ![Het tabblad 'Statuswaarschuwingen'](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecteer **Waarschuwing voor servicestatus maken** en vul de velden in.

    ![De opdracht 'Servicestatuswaarschuwing maken'](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecteer het **abonnement,** **services**en **regio's waarvoor** u wilt worden gewaarschuwd.

    ![Het dialoogvenster Waarschuwing voor activiteitslogboektoevoegen](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Dit abonnement wordt gebruikt om de waarschuwing voor het activiteitenlogboek op te slaan. De waarschuwingsbron wordt geïmplementeerd voor dit abonnement en bewaakt gebeurtenissen in het activiteitenlogboek ervoor.

1. Kies de **gebeurtenistypen** waarvoor u wilt worden gewaarschuwd: *Serviceprobleem,* *Gepland onderhoud*en *adviesadviseren voor gezondheid* 

1. Definieer uw waarschuwingsgegevens door een naam en **beschrijving** **van de waarschuwingsregel** in te voeren.

1. Selecteer de **resourcegroep** waar u de waarschuwing wilt opslaan.

1. Maak een nieuwe actiegroep door **Nieuwe actiegroep te**selecteren. Voer een naam in het vak **Groepsnaam van** de groep in en voer een naam in het vak **Korte naam** in. De korte naam wordt verwezen in de meldingen die worden verzonden wanneer deze waarschuwing wordt geactiveerd.

    ![Een nieuwe actiegroep maken](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definieer een lijst met ontvangers door de ontvanger te verstrekken:

    a. **Naam:** Voer de naam, alias of id van de ontvanger in.

    b. **Actietype:** selecteer SMS, e-mail, webhook, Azure-app en meer.

    c. **Details**: Voer op basis van het gekozen actietype een telefoonnummer, e-mailadres, webhook URI, enz.

1. Selecteer **OK** om de actiegroep te maken en maak vervolgens **een waarschuwingsregel** om uw waarschuwing te voltooien.

Binnen een paar minuten is de waarschuwing actief en begint te activeren op basis van de voorwaarden die u tijdens het maken hebt opgegeven.

Meer informatie over het [configureren van webhookmeldingen voor bestaande probleembeheersystemen](service-health-alert-webhook-guide.md). Zie [Webhooks for Azure activity log alerts voor](../azure-monitor/platform/activity-log-alerts-webhook.md)informatie over het webhook-schema voor waarschuwingen voor activiteitenlogboeken.

>[!NOTE]
>De actiegroep die in deze stappen is gedefinieerd, is herbruikbaar als een bestaande actiegroep voor alle toekomstige waarschuwingsdefinities.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Waarschuwing met bestaande actiegroep met Azure-portal

1. Volg de stappen 1 tot en met 6 in de vorige sectie om een servicestatusmelding te maken. 

1. Klik **onder Actiegroep definiëren**op de knop Actiegroep **selecteren.** Selecteer de juiste actiegroep.

1. Selecteer **Toevoegen** om de actiegroep toe te voegen en maak vervolgens **een waarschuwingsregel** om uw waarschuwing te voltooien.

Binnen een paar minuten is de waarschuwing actief en begint te activeren op basis van de voorwaarden die u tijdens het maken hebt opgegeven.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Waarschuwing en nieuwe actiegroep met behulp van de Azure Resource Manager-sjablonen

Het volgende is een voorbeeld dat een actiegroep met een e-maildoel maakt en alle servicestatusmeldingen voor het doelabonnement inschakelt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>De waarschuwingen beheren

Nadat u een waarschuwing hebt gemaakt, is deze zichtbaar in de sectie **Waarschuwingen** van **Monitor**. Selecteer de waarschuwing die u wilt beheren:

* Bewerk het.
* Verwijder het.
* Schakel deze uit of schakel deze in als u meldingen voor de waarschuwing tijdelijk wilt stoppen of hervatten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [aanbevolen procedures voor het instellen van Azure Service Health-waarschuwingen](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Meer informatie over het [instellen van mobiele pushmeldingen voor Azure Service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Meer informatie over het [configureren van webhookmeldingen voor bestaande probleembeheersystemen](service-health-alert-webhook-guide.md).
- Meer informatie over [meldingen over servicestatus](service-notifications.md).
- Meer informatie over [het beperken van het aantal meldingen](../azure-monitor/platform/alerts-rate-limiting.md).
- Bekijk het [webhookschema voor de waarschuwing voor activiteitenlogboeken](../azure-monitor/platform/activity-log-alerts-webhook.md).
- Krijg een [overzicht van waarschuwingen voor activiteitenlogboeken](../azure-monitor/platform/alerts-overview.md)en ontdek hoe u waarschuwingen ontvangt.
- Meer informatie over [actiegroepen](../azure-monitor/platform/action-groups.md).
