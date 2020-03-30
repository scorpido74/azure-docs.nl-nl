---
title: Instellingen voor slimme detectieregels - Azure Application Insights
description: Beheer en configuratie van slimme detectieregels azure application insights automatiseren met Azure Resource Manager-sjablonen
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7ca4df620739b2ab55b8ba986031cc48fe87f1fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294905"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Slimme detectieregels voor Application Insights beheren met Azure Resource Manager-sjablonen

Slimme detectieregels in Application Insights kunnen worden beheerd en geconfigureerd met [Azure Resource Manager-sjablonen.](../../azure-resource-manager/templates/template-syntax.md)
Deze methode kan worden gebruikt bij het implementeren van nieuwe Resources Application Insights met Azure Resource Manager-automatisering of voor het wijzigen van de instellingen van bestaande resources.

## <a name="smart-detection-rule-configuration"></a>Configuratie van slimme detectieregel

U kunt de volgende instellingen configureren voor een regel voor Slimme detectie:
- Als de regel is ingeschakeld (de standaardinstelling is **waar**.)
- Als e-mails moeten worden verzonden naar gebruikers die zijn gekoppeld aan de rollen [Monitoring Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) and [Monitoring Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) van het abonnement wanneer een detectie wordt gevonden (de standaardwaarde is **waar**.)
- Eventuele extra e-mailontvangers die een melding moeten krijgen wanneer een detectie wordt gevonden.
    -  E-mailconfiguratie is niet beschikbaar voor regels voor slimme detectie die zijn gemarkeerd als _voorbeeld._

Als u het configureren van de regelinstellingen via Azure Resource Manager wilt toestaan, is de configuratie van de slimme detectieregel nu beschikbaar als een verzameling binnen de Application Insights-bron, genaamd **ProactiveDetectionConfigs.**
Voor maximale flexibiliteit kan elke slimme detectieregel worden geconfigureerd met unieke meldingsinstellingen.

## <a name="examples"></a>Voorbeelden

Hieronder vindt u enkele voorbeelden die laten zien hoe u de instellingen van slimme detectieregels configureert met Azure Resource Manager-sjablonen.
Alle voorbeelden verwijzen naar een Application Insights-bron met de naam _"myApplication"_ en naar de slimme detectieregel voor de lange afhankelijkheidsduur", die intern _"longdependencyduration"_ wordt genoemd.
Zorg ervoor dat u de naam van de toepassingsstatistieken-bron vervangt en de interne naam van de relevante slimme detectieregel opgeeft. Bekijk de onderstaande tabel voor een lijst met de bijbehorende interne Azure Resource Manager-namen voor elke slimme detectieregel.

### <a name="disable-a-smart-detection-rule"></a>Een slimme detectieregel uitschakelen

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

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Het verzenden van e-mailmeldingen uitschakelen voor een slimme detectieregel

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

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Extra e-mailontvangers toevoegen voor een slimme detectieregel

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


## <a name="smart-detection-rule-names"></a>Namen van slimme detectieregels

Hieronder vindt u een tabel met namen van slimme detectieregels zoals deze in de portal worden weergegeven, samen met hun interne namen, die moeten worden gebruikt in de sjabloon Azure Resource Manager.

> [!NOTE]
> Slimme detectieregels die zijn gemarkeerd als _voorbeeld,_ ondersteunen geen e-mailmeldingen. Daarom u alleen de _ingeschakelde_ eigenschap voor deze regels instellen. 

| Naam azure-portalregel | Interne naam
|:---|:---|
| Trage laadtijd van pagina's | slowpageloadtime |
| Trage reactietijd van de server | slowserverresponstijd |
| Lange afhankelijkheidsduur | duur van de langdurige afhankelijkheid |
| Degradatie in de responstijd van de server | degradationinserverresponsetime |
| Degradatie in afhankelijkheidsduur | degradatieinafhankelijkheidduur |
| Degradatie in traceringsverhouding (voorbeeld) | extension_traceseveritydetector |
| Abnormale stijging van het uitzonderingsvolume (voorbeeld) | extension_exceptionchangeextension |
| Potentieel geheugenlek gedetecteerd (voorbeeld) | extension_memoryleakextension |
| Mogelijk beveiligingsprobleem gedetecteerd (voorbeeld) | extension_securityextensionspackage |
| Abnormale stijging van het dagelijkse gegevensvolume (preview) | extension_billingdatavolumedailyspikeextension |

### <a name="failure-anomalies-alert-rule"></a>Waarschuwingsregel Foutanomalieën

Deze sjabloon Azure Resource Manager toont het configureren van een waarschuwingsregel foutanomalieën met een ernst van 2. Deze nieuwe versie van de waarschuwingsregel Foutafwijkingen maakt deel uit van het nieuwe Azure-waarschuwingsplatform en vervangt de klassieke versie die wordt uitgeschakeld als onderdeel van het [klassieke pensioenproces voor waarschuwingen](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

> [!NOTE]
> Foutanomalieën is een wereldwijde service, daarom wordt regellocatie gemaakt op de globale locatie.

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
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
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
> Deze azure resource manager-sjabloon is uniek voor de waarschuwingsregel Foutanomalieën en verschilt van de andere klassieke smartdetectieregels die in dit artikel worden beschreven. Als u foutafwijkingen handmatig wilt beheren, wordt dit gedaan in Azure Monitor Alerts, terwijl alle andere regels voor slimme detectie worden beheerd in het deelvenster Slimme detectie van de gebruikersinterface.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over automatisch detecteren:

- [Foutafwijkingen](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Geheugenlekken](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Prestatieafwijkingen](../../azure-monitor/app/proactive-performance-diagnostics.md)
