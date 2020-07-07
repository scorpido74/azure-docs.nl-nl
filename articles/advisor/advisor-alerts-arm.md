---
title: Azure Advisor waarschuwingen maken voor nieuwe aanbevelingen met behulp van Resource Manager-sjabloon
description: Azure Advisor waarschuwingen voor nieuwe aanbeveling maken
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/29/2020
ms.openlocfilehash: ef15891cc01d0481c6253023de262f14dce0ec81
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921078"
---
# <a name="quickstart-create-azure-advisor-alerts-on-new-recommendations-using-an-arm-template"></a>Snelstartgids: Azure Advisor-waarschuwingen maken voor nieuwe aanbevelingen met behulp van een ARM-sjabloon

In dit artikel wordt beschreven hoe u een waarschuwing instelt voor nieuwe aanbevelingen van Azure Advisor met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wanneer Azure Advisor een nieuwe aanbeveling voor een van uw resources detecteert, wordt een gebeurtenis opgeslagen in het [activiteiten logboek van Azure](/azure/azure-monitor/platform/activity-logs-overview). U kunt waarschuwingen instellen voor deze gebeurtenissen van Azure Advisor met behulp van een aanbevelings gerichte waarschuwing voor het maken van waarschuwingen. U kunt een abonnement en optioneel een resource groep selecteren om de resources op te geven waarvoor u waarschuwingen wilt ontvangen.

U kunt ook de typen aanbevelingen vaststellen met behulp van de volgende eigenschappen:

- Categorie
- Impact niveau
- Type aanbeveling

U kunt ook de actie configureren die wordt uitgevoerd wanneer een waarschuwing wordt geactiveerd door:  

- Een bestaande actie groep selecteren
- Een nieuwe actie groep maken

Raadpleeg [Actiegroepen maken en beheren](../azure-monitor/platform/action-groups.md) voor meer informatie over actiegroepen.

> [!NOTE]
> Advisor-waarschuwingen zijn momenteel alleen beschikbaar voor aanbevelingen voor hoge Beschik baarheid, prestaties en kosten. Beveiligings aanbevelingen worden niet ondersteund.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Als u de opdrachten vanaf uw lokale computer wilt uitvoeren, installeert u Azure CLI of de Azure PowerShell-modules. Zie [de Azure cli installeren](/cli/azure/install-azure-cli) en [Azure PowerShell installeren](/powershell/azure/install-az-ps)voor meer informatie.

## <a name="review-the-template"></a>De sjabloon controleren

Met de volgende sjabloon maakt u een actie groep met een e-mail doel en schakelt u alle service status meldingen in voor het doel abonnement. Sla deze sjabloon *op alsCreateAdvisorAlert.jsop*.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroups_name": {
      "defaultValue": "advisorAlert",
      "type": "string"
    },
    "activityLogAlerts_name": {
      "defaultValue": "AdvisorAlertsTest",
      "type": "string"
    },
    "emailAddress": {
      "defaultValue": "<email address>",
      "type": "string"
    }
  },
  "variables": {
    "alertScope": "[concat('/','subscriptions','/',subscription().subscriptionId)]"
  },
  "resources": [
    {
      "comments": "Action Group",
      "type": "microsoft.insights/actionGroups",
      "name": "[parameters('actionGroups_name')]",
      "apiVersion": "2019-06-01",
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
      "comments": "Azure Advisor Activity Log Alert",
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
              "equals": "Recommendation"
            },
            {
              "field": "properties.recommendationCategory",
              "equals": "Cost"
            },
            {
              "field": "properties.recommendationImpact",
              "equals": "Medium"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Advisor/recommendations/available/action"
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

Implementeer de sjabloon met een standaard methode voor het [implementeren van een arm-sjabloon](../azure-resource-manager/templates/deploy-portal.md) , zoals de volgende voor beelden met CLI en Power shell. Vervang de voorbeeld waarden voor de **resource groep**en het **emailAddress** met de juiste waarden voor uw omgeving. De naam van de werk ruimte moet uniek zijn voor alle Azure-abonnementen.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az login
az deployment group create --name CreateAdvisorAlert --resource-group my-resource-group --template-file CreateAdvisorAlert.json --parameters emailAddress='user@contoso.com'
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName my-subscription
New-AzResourceGroupDeployment -Name CreateAdvisorAlert -ResourceGroupName my-resource-group -TemplateFile CreateAdvisorAlert.json -emailAddress user@contoso.com
```

---

## <a name="validate-the-deployment"></a>De implementatie valideren

Controleer of de werk ruimte is gemaakt met behulp van een van de volgende opdrachten. Vervang de voorbeeld waarden voor de **resource groep** door de waarde die u hierboven hebt gebruikt.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az monitor activity-log alert show --resource-group my-resource-group --name AdvisorAlertsTest
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell
Get-AzActivityLogAlert -ResourceGroupName my-resource-group -Name AdvisorAlertsTest
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

- Bekijk een [overzicht van waarschuwingen voor activiteiten logboeken](../azure-monitor/platform/alerts-overview.md)en meer informatie over het ontvangen van waarschuwingen.
- Meer informatie over [actie groepen](../azure-monitor/platform/action-groups.md).
