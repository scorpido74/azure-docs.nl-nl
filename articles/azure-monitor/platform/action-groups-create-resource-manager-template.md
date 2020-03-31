---
title: Actiegroepen maken met Resource Manager-sjablonen
description: Meer informatie over het maken van een actiegroep met behulp van een Azure Resource Manager-sjabloon.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 50ad9d57b24fab9ee57c2f9caae8f4c39d2681f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669077"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Een actiegroep maken met een resourcemanagersjabloon
In dit artikel ziet u hoe u een [Azure Resource Manager-sjabloon gebruikt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) om actiegroepen te configureren. Door sjablonen te gebruiken, u automatisch actiegroepen instellen die kunnen worden hergebruikt in bepaalde typen waarschuwingen. Deze actiegroepen zorgen ervoor dat alle juiste partijen worden gewaarschuwd wanneer een waarschuwing wordt geactiveerd.

De basisstappen zijn:

1. Maak een sjabloon als een JSON-bestand waarin wordt beschreven hoe u de actiegroep maakt.

2. Implementeer de sjabloon met behulp van [een implementatiemethode](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy).

Eerst beschrijven we hoe u een Resource Manager-sjabloon maakt voor een actiegroep waarbij de actiedefinities hard gecodeerd zijn in de sjabloon. Ten tweede beschrijven we hoe u een sjabloon maakt die de configuratiegegevens van de webhook als invoerparameters neemt wanneer de sjabloon wordt geïmplementeerd.

## <a name="resource-manager-templates-for-an-action-group"></a>Resourcebeheersjablonen voor een actiegroep

Als u een actiegroep wilt maken met een resourcemanagersjabloon, maakt u een resource van het type `Microsoft.Insights/actionGroups`. Vervolgens vult u alle gerelateerde eigenschappen in. Hier volgen twee voorbeeldsjablonen waarmee een actiegroep wordt gemaakt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]"
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [actiegroepen](../../azure-monitor/platform/action-groups.md).
* Meer informatie over [waarschuwingen](alerts-overview.md).
* Meer informatie over het toevoegen [van waarschuwingen met behulp van een sjabloon Resourcebeheer](../../azure-monitor/platform/alerts-activity-log.md).

