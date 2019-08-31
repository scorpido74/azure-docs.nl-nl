---
title: Instellingen voor detectie van Azure-toepassing Insights-regel configureren met Azure Resource Manager sjablonen | Microsoft Docs
description: Beheer en configuratie automatiseren van Azure-toepassing Insights-regels voor slimme detectie met Azure Resource Manager sjablonen
services: application-insights
documentationcenter: ''
author: harelbr
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.author: harelbr
ms.openlocfilehash: e7a54c2e207a27f3519375df09d0c930a92d52d6
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193717"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Application Insights Slimme detectie regels beheren met Azure Resource Manager sjablonen

Slimme detectie regels in Application Insights kunnen worden beheerd en geconfigureerd met behulp van [Azure Resource Manager sjablonen](../../azure-resource-manager/resource-group-authoring-templates.md).
Deze methode kan worden gebruikt bij het implementeren van nieuwe Application Insights resources met Azure Resource Manager Automation of voor het wijzigen van de instellingen van bestaande resources.

## <a name="smart-detection-rule-configuration"></a>Configuratie van de detectie regel voor Smart

U kunt de volgende instellingen configureren voor een regel voor Slimme detectie:
- Als de regel is ingeschakeld (de standaard waarde is **waar**.)
- Als er e-mail berichten moeten worden verzonden naar gebruikers die zijn gekoppeld aan de [bewakings lezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) van het abonnement en bij het bewaken van [Inzender](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) rollen wanneer een detectie wordt gevonden (de standaard waarde is **waar**.)
- Aanvullende e-mail ontvangers die een melding moeten ontvangen wanneer een detectie wordt gevonden.
    -  De e-mail configuratie is niet beschikbaar voor slimme detectie regels die als _Preview_zijn gemarkeerd.

Om het configureren van de regel instellingen via Azure Resource Manager toe te staan, is de configuratie van de detectie regel nu beschikbaar als binnenste resource binnen de Application Insights resource met de naam **ProactiveDetectionConfigs**.
Voor een maximale flexibiliteit kan elke Slimme detectie regel worden geconfigureerd met unieke meldings instellingen.

## 

## <a name="examples"></a>Voorbeelden

Hieronder ziet u enkele voor beelden van het configureren van de instellingen van Slimme detectie regels met Azure Resource Manager sjablonen.
Alle voor beelden verwijzen naar een Application Insights resource met de naam _' mijn toepassing '_ en de detectie regel voor lange afhankelijkheids duur, die intern de naam _' longdependencyduration '_ heeft.
Zorg ervoor dat u de naam van de Application Insights resource vervangt en de relevante interne naam voor de Slimme detectie regel opgeeft. Controleer de onderstaande tabel voor een lijst met de corresponderende interne Azure Resource Manager namen voor elke Slimme detectie regel.

### <a name="disable-a-smart-detection-rule"></a>Een regel voor slimme detectie uitschakelen

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Het verzenden van e-mail meldingen voor een slimme detectie regel uitschakelen

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Aanvullende e-mail ontvangers voor een slimme detectie regel toevoegen

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Waarschuwings regel voor fout afwijkingen v2 (niet-klassiek)

Deze Azure Resource Manager sjabloon demonstreert het configureren van een waarschuwings regel voor fout afwijkingen v2 met de ernst 2. Deze nieuwe versie van de waarschuwings regel voor afwijkingen van de fout maakt deel uit van het nieuwe Azure Alerting platform en vervangt de klassieke versie die buiten gebruik wordt gesteld als onderdeel van het buiten gebruik stellen van [klassieke waarschuwingen](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Detects a spike in the failure rate of requests or dependencies",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Deze Azure Resource Manager sjabloon is uniek voor de waarschuwings regel voor fout afwijkingen v2 en wijkt af van de andere klassieke regels voor slimme detectie die in dit artikel worden beschreven.   

## <a name="smart-detection-rule-names"></a>Namen van Slimme detectie regels

Hieronder ziet u een tabel met namen van Slimme detectie regels zoals ze worden weer gegeven in de portal, samen met hun interne namen, die moeten worden gebruikt in de sjabloon Azure Resource Manager.

> [!NOTE]
> Slimme detectie regels die als _Preview_ zijn gemarkeerd, bieden geen ondersteuning voor e-mail meldingen. Daarom kunt u de ingeschakelde eigenschap alleen instellen voor deze regels. 

| Naam van Azure Portal regel | Interne naam
|:---|:---|
| Langzame paginalaadtijd | slowpageloadtime |
| Langzame reactietijd voor de server | slowserverresponsetime |
| Lange duur van de afhankelijkheid | longdependencyduration |
| Degradatie van de serverreactietijd | degradationinserverresponsetime |
| Degradatie van afhankelijkheidsduur | degradationindependencyduration |
| Degradatie van de verhouding van de ernst van de tracering (preview-versie) | extension_traceseveritydetector |
| Uitzonderings volume met een abnormale toename (preview-versie) | extension_exceptionchangeextension |
| Mogelijke geheugenlek gedetecteerd (preview-versie) | extension_memoryleakextension |
| Mogelijk beveiligings probleem gedetecteerd (preview-versie) | extension_securityextensionspackage |
| Een abnormale toename in het dagelijks gegevens volume (preview-versie) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatisch detecteren van:

- [Foutafwijkingen](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Geheugen lekken](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestatieafwijkingen](../../azure-monitor/app/proactive-performance-diagnostics.md)
