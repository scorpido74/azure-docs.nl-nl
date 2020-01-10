---
title: Waarschuwingen voor activiteiten logboeken ontvangen op Azure-service meldingen
description: Ontvang een melding via SMS, e-mail of webhook wanneer de Azure-service wordt uitgevoerd.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749321"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Waarschuwingen voor activiteiten logboek maken voor service meldingen
## <a name="overview"></a>Overzicht

In dit artikel wordt beschreven hoe u waarschuwingen voor activiteiten logboeken instelt voor service status meldingen met behulp van de Azure Portal.  

Service status meldingen worden opgeslagen in het [Azure-activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md) , gezien de mogelijk grote hoeveelheid informatie die is opgeslagen in het activiteiten logboek, is er een afzonderlijke gebruikers interface waarmee u waarschuwingen voor service status meldingen gemakkelijker kunt weer geven en instellen. 

U kunt een waarschuwing ontvangen wanneer Azure service status meldingen verzendt naar uw Azure-abonnement. U kunt de waarschuwing configureren op basis van:

- De klasse van service status meldingen (Service problemen, gepland onderhoud, status advies).
- Het abonnement is betrokken.
- De betrokken service (s).
- De betrokken regio (en).

> [!NOTE]
> Met service status meldingen wordt geen waarschuwing verzonden over de resource status gebeurtenissen.

U kunt ook configureren naar wie de waarschuwing moet worden verzonden:

- Selecteer een bestaande actie groep.
- Een nieuwe actie groep maken (die kan worden gebruikt voor toekomstige waarschuwingen).

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen.

Zie [Resource Manager-sjablonen](../azure-monitor/platform/alerts-activity-log.md)voor meer informatie over het configureren van waarschuwingen voor service status meldingen met behulp van Azure Resource Manager sjablonen.

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Bekijk een video over het instellen van uw eerste Azure Service Health waarschuwing

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Waarschuwing en nieuwe actie groep met Azure Portal
1. Selecteer in de [portal](https://portal.azure.com) **service Health**.

    ![De ' Service Health-Service](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. Selecteer in de sectie **waarschuwingen** **Health Alerts**.

    ![Het tabblad status meldingen](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Selecteer **service status waarschuwing maken** en vul de velden in.

    ![De opdracht ' service Health alert maken '](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Selecteer het **abonnement**, de **Services**en de **regio's** waarvoor u een waarschuwing wilt ontvangen.

    ![Het dialoog venster waarschuwing voor activiteiten logboek toevoegen](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Dit abonnement wordt gebruikt om de waarschuwing voor activiteiten logboek op te slaan. De resource van de waarschuwing wordt geïmplementeerd voor dit abonnement en controleert gebeurtenissen in het activiteiten logboek.

1. Kies de **gebeurtenis typen** waarvoor u een waarschuwing wilt ontvangen: *service probleem*, *gepland onderhoud*en *status adviezen* 

1. Definieer uw waarschuwings Details door een naam en **Beschrijving**voor de **waarschuwings regel** in te voeren.

1. Selecteer de **resource groep** waar u de waarschuwing wilt opslaan.

1. Maak een nieuwe actie groep door **nieuwe actie groep**te selecteren. Voer een naam in het vak Naam van de **actie groep** in en voer een naam in het vak **korte naam** in. Naar de korte naam wordt verwezen in de meldingen die worden verzonden wanneer deze waarschuwing wordt geactiveerd.

    ![Een nieuwe actie groep maken](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Definieer een lijst met ontvangers door de ontvanger te voorzien van het volgende:

    a. **Naam**: Voer de naam, alias of id van de ontvanger in.

    b. **Actie type**: Selecteer SMS, e-mail, webhook, Azure-app en meer.

    c. **Details**: Voer een telefoon nummer, e-mail adres, webhook-URI, enzovoort in op basis van het gekozen actie type.

1. Selecteer **OK** om de actie groep te maken en maak vervolgens een **waarschuwings regel** om uw waarschuwing te volt ooien.

Binnen een paar minuten is de waarschuwing actief en begint deze te activeren op basis van de voor waarden die u hebt opgegeven tijdens het maken.

Meer informatie over het [configureren van webhook-meldingen voor bestaande probleem beheersystemen](service-health-alert-webhook-guide.md). Zie [webhooks voor Azure-activiteiten logboek waarschuwingen](../azure-monitor/platform/activity-log-alerts-webhook.md)voor informatie over het webhook-schema voor waarschuwingen voor activiteiten Logboeken.

>[!NOTE]
>De in deze stappen gedefinieerde actie groep kan opnieuw worden gebruikt als een bestaande actie groep voor alle toekomstige waarschuwings definities.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Waarschuwing met bestaande actie groep met Azure Portal

1. Volg de stappen 1 tot en met 6 in de vorige sectie om uw service status melding te maken. 

1. Klik onder **actie groep definiëren**op de knop **actie groep selecteren** . Selecteer de juiste actie groep.

1. Selecteer **toevoegen** om de actie groep toe te voegen en maak vervolgens een **waarschuwings regel** om uw waarschuwing te volt ooien.

Binnen een paar minuten is de waarschuwing actief en begint deze te activeren op basis van de voor waarden die u hebt opgegeven tijdens het maken.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Waarschuwing en nieuwe actie groep met behulp van de Azure Resource Manager sjablonen

Hier volgt een voor beeld van het maken van een actie groep met een e-mail doel en het inschakelen van alle service status meldingen voor het doel abonnement.

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

Nadat u een waarschuwing hebt gemaakt, wordt deze weer gegeven in de sectie **waarschuwingen** van de **monitor**. Selecteer de waarschuwing die u wilt beheren:

* Deze bewerken.
* Deze verwijderen.
* Schakel deze optie uit of in als u de ontvangst van meldingen voor de waarschuwing tijdelijk wilt stoppen of hervatten.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Aanbevolen procedures voor het instellen van Azure service Health-waarschuwingen](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Meer informatie over het [instellen van mobiele push meldingen voor Azure service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Meer informatie over het [configureren van webhook-meldingen voor bestaande probleem beheersystemen](service-health-alert-webhook-guide.md).
- Meer informatie over [service status meldingen](service-notifications.md).
- Meer informatie over beperking van de [meldings frequentie](../azure-monitor/platform/alerts-rate-limiting.md).
- Controleer het [webhook-schema](../azure-monitor/platform/activity-log-alerts-webhook.md)van de waarschuwing voor het activiteiten logboek.
- Bekijk een [overzicht van waarschuwingen voor activiteiten logboeken](../azure-monitor/platform/alerts-overview.md)en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actie groepen](../azure-monitor/platform/action-groups.md).
