---
title: Waarschuwingen voor activiteiten logboeken ontvangen op Azure-service meldingen met behulp van de Resource Manager-sjabloon
description: Ontvang een melding via SMS, e-mail of webhook wanneer de Azure-service wordt uitgevoerd.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: 84c888195ab7e2f3288691948706d31160393d25
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85918910"
---
# <a name="quickstart-create-activity-log-alerts-on-service-notifications-using-an-arm-template"></a>Snelstartgids: waarschuwingen voor activiteiten logboeken maken voor service meldingen met behulp van een ARM-sjabloon

In dit artikel wordt beschreven hoe u waarschuwingen voor activiteiten logboeken instelt voor service status meldingen met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Service status meldingen worden opgeslagen in het [Azure-activiteiten logboek](../azure-monitor/platform/platform-logs-overview.md). Gezien het mogelijk grote volume aan gegevens dat in het activiteiten logboek is opgeslagen, is er een afzonderlijke gebruikers interface waarmee u waarschuwingen kunt bekijken en instellen voor service status meldingen.

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

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Als u de opdrachten vanaf uw lokale computer wilt uitvoeren, installeert u Azure CLI of de Azure PowerShell-modules. Zie [de Azure cli installeren](/cli/azure/install-azure-cli) en [Azure PowerShell installeren](/powershell/azure/install-az-ps)voor meer informatie.

## <a name="review-the-template"></a>De sjabloon controleren

Met de volgende sjabloon maakt u een actie groep met een e-mail doel en schakelt u alle service status meldingen in voor het doel abonnement. Sla deze sjabloon *op alsCreateServiceHealthAlert.jsop*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2019-06-01",
      "name": "[parameters('actionGroups_name')]",
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
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlerts_name')]",
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

De sjabloon definieert twee resources:

- [Micro soft. Insights/actionGroups](/azure/templates/microsoft.insights/actiongroups)
- [Micro soft. Insights/activityLogAlerts](/azure/templates/microsoft.insights/activityLogAlerts)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Implementeer de sjabloon met een standaard methode voor het [implementeren van een arm-sjabloon](../azure-resource-manager/templates/deploy-portal.md) , zoals de volgende voor beelden met CLI en Power shell. Vervang de voorbeeld waarden voor **resource groep** en **emailAddress** met de juiste waarden voor uw omgeving.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateServiceHealthAlert --resource-group my-resource-group --template-file CreateServiceHealthAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateServiceHealthAlert -ResourceGroupName my-resource-group -TemplateFile CreateServiceHealthAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer of de werk ruimte is gemaakt met behulp van een van de volgende opdrachten. Vervang de voorbeeld waarden voor de **resource groep** door de waarde die u hierboven hebt gebruikt.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name ServiceHealthActivityLogAlert
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name ServiceHealthActivityLogAlert
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om verder te gaan met volgende snelstarts en zelfstudies, kunt u deze resources het beste intact laten. Als u deze niet meer nodig hebt, verwijdert u de resource groep, waarmee de waarschuwings regel en de gerelateerde resources worden verwijderd. De resource groep verwijderen met behulp van Azure CLI of Azure PowerShell

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name my-resource-group
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Remove-AzResourceGroup -Name my-resource-group
```

---

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Aanbevolen procedures voor het instellen van Azure service Health-waarschuwingen](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa).
- Meer informatie over het [instellen van mobiele push meldingen voor Azure service Health](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw).
- Meer informatie over het [configureren van webhook-meldingen voor bestaande probleem beheersystemen](service-health-alert-webhook-guide.md).
- Meer informatie over [service status meldingen](service-notifications.md).
- Meer informatie over beperking van de [meldings frequentie](../azure-monitor/platform/alerts-rate-limiting.md).
- Controleer het [webhook-schema](../azure-monitor/platform/activity-log-alerts-webhook.md)van de waarschuwing voor het activiteiten logboek.
- Bekijk een [overzicht van waarschuwingen voor activiteiten logboeken](../azure-monitor/platform/alerts-overview.md)en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actie groepen](../azure-monitor/platform/action-groups.md).
