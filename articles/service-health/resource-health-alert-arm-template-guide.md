---
title: Sjabloon om waarschuwingen voor resourcestatus te maken
description: Maak programmatisch waarschuwingen die u op de hoogte stellen wanneer uw Azure-bronnen niet meer beschikbaar zijn.
ms.topic: conceptual
ms.date: 9/4/2018
ms.openlocfilehash: c01934cc88dc29d0503abfafc203ab0f04bf1761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062900"
---
# <a name="configure-resource-health-alerts-using-resource-manager-templates"></a>Waarschuwingen voor bronstatus configureren met behulp van ResourceBeheer-sjablonen

In dit artikel ziet u hoe u waarschuwingen voor resourcestatusactiviteitslogboeken programmatisch maken met Azure Resource Manager-sjablonen en Azure PowerShell.

Azure Resource Health houdt u op de hoogte van de huidige en historische status van uw Azure-resources. Azure Resource Health-waarschuwingen kunnen u in bijna realtime op de hoogte stellen wanneer deze resources een wijziging in hun status hebben. Als u waarschuwingen voor resourcestatus programmatisch maakt, kunnen gebruikers waarschuwingen in bulk maken en aanpassen.

> [!NOTE]
> Waarschuwingen voor resourcestatus zijn momenteel in preview.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u de instructies op deze pagina wilt volgen, moet u van tevoren een paar dingen instellen:

1. U moet de [Azure PowerShell-module](https://docs.microsoft.com/powershell/azure/install-Az-ps) installeren
2. U moet [een actiegroep maken of opnieuw gebruiken](../azure-monitor/platform/action-groups.md) die is geconfigureerd om u hiervan op de hoogte te stellen

## <a name="instructions"></a>Instructies
1. Meld u met PowerShell aan bij Azure met uw account en selecteer het abonnement waarmee u wilt communiceren

        Login-AzAccount
        Select-AzSubscription -Subscription <subscriptionId>

    > U kunt `Get-AzSubscription` de abonnementen waartoe u toegang hebt, aanbieden.

2. De volledige Azure Resource Manager-id voor uw actiegroep zoeken en opslaan

        (Get-AzActionGroup -ResourceGroupName <resourceGroup> -Name <actionGroup>).Id

3. Een resourcebeheersjabloon maken en opslaan `resourcehealthalert.json` voor waarschuwingen voor resourcestatus als ([zie details hieronder](#resource-manager-template-options-for-resource-health-alerts))

4. Een nieuwe Azure Resource Manager-implementatie maken met deze sjabloon

        New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <resourceGroup> -TemplateFile <path\to\resourcehealthalert.json>

5. U wordt gevraagd de bron-id voor waarschuwingsnaam en actiegroep in te voeren die u eerder hebt gekopieerd:

        Supply values for the following parameters:
        (Type !? for Help.)
        activityLogAlertName: <Alert Name>
        actionGroupResourceId: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.insights/actionGroups/<actionGroup>

6. Als alles succesvol is verlopen, krijgt u een bevestiging in PowerShell

        DeploymentName          : ExampleDeployment
        ResourceGroupName       : <resourceGroup>
        ProvisioningState       : Succeeded
        Timestamp               : 11/8/2017 2:32:00 AM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                Name                     Type       Value
                                ===============          =========  ==========
                                activityLogAlertName     String     <Alert Name>
                                activityLogAlertEnabled  Bool       True
                                actionGroupResourceId    String     /...
        
        Outputs                 :
        DeploymentDebugLogLevel :

Houd er rekening mee dat als u van plan bent dit proces volledig te automatiseren, u alleen de resourcemanagersjabloon moet bewerken om niet om de waarden in stap 5 te vragen.

## <a name="resource-manager-template-options-for-resource-health-alerts"></a>Resourcebeheersjabloonopties voor waarschuwingen voor resourcestatus

U deze basissjabloon gebruiken als uitgangspunt voor het maken van waarschuwingen voor resourcestatus. Deze sjabloon werkt zoals geschreven en meldt u zich aan om waarschuwingen te ontvangen voor alle nieuw geactiveerde gebeurtenissen in resourcestatus in alle bronnen in een abonnement.

> Aan de onderkant van dit artikel hebben we ook een meer complexe waarschuwingssjabloon opgenomen die de signaal-ruisverhouding voor Waarschuwingen voor ResourceHealth moet verhogen in vergelijking met deze sjabloon.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": true,
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "ResourceHealth"
            },
            {
              "field": "status",
              "equals": "Active"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

Echter, een brede waarschuwing als deze wordt over het algemeen niet aanbevolen. Lees hieronder hoe we deze waarschuwing kunnen uitspelen om ons te concentreren op de gebeurtenissen waar we om geven.

### <a name="adjusting-the-alert-scope"></a>Het waarschuwingsbereik aanpassen

Waarschuwingen voor resourcestatus kunnen worden geconfigureerd om gebeurtenissen op drie verschillende scopes te controleren:

 * Abonnementsniveau
 * Resourcegroepniveau
 * Resourceniveau

De waarschuwingssjabloon is geconfigureerd op abonnementsniveau, maar als u uw waarschuwing wilt configureren om u alleen op de hoogte te `scopes` stellen van bepaalde bronnen of bronnen binnen een bepaalde resourcegroep, hoeft u alleen de sectie in de bovenstaande sjabloon te wijzigen.

Voor een bereik op resourcegroepniveau moet de sectie scopes er als volgt uitzien:
```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>"
],
```

En voor een bereik op resourceniveau moet de scopesectie er als volgt uitzien:

```json
"scopes": [
    "/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/<resource>"
],
```

Bijvoorbeeld: `"/subscriptions/d37urb3e-ed41-4670-9c19-02a1d2808ff9/resourcegroups/myRG/providers/microsoft.compute/virtualmachines/myVm"`

> U naar de Azure Portal gaan en de URL bekijken wanneer u uw Azure-bron bekijkt om deze tekenreeks te krijgen.

### <a name="adjusting-the-resource-types-which-alert-you"></a>De resourcetypen aanpassen die u waarschuwen

Waarschuwingen op abonnements- of resourcegroepniveau kunnen verschillende soorten bronnen bevatten. Als u wilt beperken waarschuwingen om alleen afkomstig zijn van een `condition` bepaalde subset van resourcetypen, u definiëren dat in het gedeelte van de sjabloon als volgt:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.COMPUTE/VIRTUALMACHINES",
                    "containsAny": null
                },
                {
                    "field": "resourceType",
                    "equals": "MICROSOFT.STORAGE/STORAGEACCOUNTS",
                    "containsAny": null
                },
                ...
            ]
        }
    ]
},
```

Hier gebruiken `anyOf` we de wrapper om de waarschuwing voor resourcestatus te laten voldoen aan een van de voorwaarden die we opgeven, zodat waarschuwingen kunnen worden gegenereerd die specifieke resourcetypen targeten.

### <a name="adjusting-the-resource-health-events-that-alert-you"></a>De gebeurtenissen resourcestatus aanpassen die u waarschuwen
Wanneer resources een gezondheidsgebeurtenis ondergaan, kunnen ze door een reeks fasen `Active`gaan `In Progress` `Updated`die `Resolved`de status van de gezondheidsgebeurtenis vertegenwoordigen: , , en .

U alleen een melding ontvangen wanneer een resource niet in orde wordt, `status` in `Active`welk geval u uw waarschuwing wilt configureren om alleen op de hoogte te stellen wanneer de bron. Als u echter ook op de andere fasen op de hoogte wilt worden gesteld, u deze gegevens toevoegen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "status",
                    "equals": "Active"
                },
                {
                    "field": "status",
                    "equals": "In Progress"
                },
                {
                    "field": "status",
                    "equals": "Resolved"
                },
                {
                    "field": "status",
                    "equals": "Updated"
                }
            ]
        }
    ]
}
```

Als u op de hoogte wilt worden gesteld van alle vier de stadia van gezondheidsgebeurtenissen, `status` u deze voorwaarde allemaal samen verwijderen en wordt u door de waarschuwing op de hoogte gebracht, ongeacht de eigenschap.

### <a name="adjusting-the-resource-health-alerts-to-avoid-unknown-events"></a>De waarschuwingen resourcestatus aanpassen om gebeurtenissen in 'Onbekende' te voorkomen

Azure Resource Health kan aan u de nieuwste status van uw resources rapporteren door ze voortdurend te controleren met behulp van testrunners. De relevante gerapporteerde gezondheidsstatussen zijn: "Beschikbaar", "Niet beschikbaar" en "Gedegradeerd". In situaties waarin de loper en de Azure-bron niet kunnen communiceren, wordt echter een status van 'Onbekend' gerapporteerd voor de resource en wordt dat beschouwd als een statusgebeurtenis 'Actief'.

Wanneer een resource echter 'Onbekend' rapporteert, is het waarschijnlijk dat de gezondheidsstatus niet is gewijzigd sinds het laatste nauwkeurige rapport. Als u waarschuwingen over 'Onbekende' gebeurtenissen wilt verwijderen, u die logica opgeven in de sjabloon:

```json
"condition": {
    "allOf": [
        ...,
        {
            "anyOf": [
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.currentHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
        {
            "anyOf": [
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Available",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Unavailable",
                    "containsAny": null
                },
                {
                    "field": "properties.previousHealthStatus",
                    "equals": "Degraded",
                    "containsAny": null
                }
            ]
        },
    ]
},
```

In dit voorbeeld stellen we alleen melding van gebeurtenissen waarbij de huidige en vorige status geen 'Onbekend' heeft. Deze wijziging kan een nuttige aanvulling zijn als uw waarschuwingen rechtstreeks naar uw mobiele telefoon of e-mail worden verzonden. 

Houd er rekening mee dat het mogelijk is dat de eigenschappen currentHealthStatus en previousHealthStatus in sommige gebeurtenissen nietig zijn. Wanneer er bijvoorbeeld een bijgewerkte gebeurtenis optreedt, is het waarschijnlijk dat de status van de resource niet is gewijzigd sinds het laatste rapport, alleen dat er aanvullende gebeurtenisgegevens beschikbaar zijn (bijvoorbeeld oorzaak). Daarom kan het gebruik van de bovenstaande component ertoe leiden dat sommige waarschuwingen niet worden geactiveerd, omdat de waarden properties.currentHealthStatus en properties.previousHealthStatus worden ingesteld op null.

### <a name="adjusting-the-alert-to-avoid-user-initiated-events"></a>De waarschuwing aanpassen om door gebruikers geïnitieerde gebeurtenissen te voorkomen

Resourcestatusgebeurtenissen kunnen worden geactiveerd door door het platform geïnitieerde en door de gebruiker geïnitieerde gebeurtenissen. Het kan zinvol zijn om alleen een melding te verzenden wanneer de statusgebeurtenis wordt veroorzaakt door het Azure-platform.

Het is eenvoudig om uw waarschuwing te configureren om te filteren op alleen dit soort gebeurtenissen:

```json
"condition": {
    "allOf": [
        ...,
        {
            "field": "properties.cause",
            "equals": "PlatformInitiated",
            "containsAny": null
        }
    ]
}
```
Houd er rekening mee dat het mogelijk is dat het veld Oorzaak in sommige gebeurtenissen nietig is. Dat wil zeggen dat er een statusovergang plaatsvindt (bijvoorbeeld beschikbaar voor niet-beschikbaar) en de gebeurtenis wordt onmiddellijk geregistreerd om vertragingen bij meldingen te voorkomen. Daarom kan het gebruik van de bovenstaande clausule ertoe leiden dat een waarschuwing niet wordt geactiveerd, omdat de waarde van de eigenschap properties.clause wordt ingesteld op null.

## <a name="complete-resource-health-alert-template"></a>Waarschuwingssjabloon Bronstatus voltooien

Met behulp van de verschillende aanpassingen beschreven in de vorige sectie, hier is een voorbeeld sjabloon die is geconfigureerd om het signaal te maximaliseren naar ruis verhouding. Houd rekening met de hierboven genoemde kanttekeningen waar de huidigeHealthStatus, vorigeHealthStatus en oorzaak eigenschapwaarden in sommige gebeurtenissen nietig kunnen zijn.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "activityLogAlertName": {
            "type": "string",
            "metadata": {
                "description": "Unique name (within the Resource Group) for the Activity log alert."
            }
        },
        "actionGroupResourceId": {
            "type": "string",
            "metadata": {
                "description": "Resource Id for the Action group."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Insights/activityLogAlerts",
            "apiVersion": "2017-04-01",
            "name": "[parameters('activityLogAlertName')]",
            "location": "Global",
            "properties": {
                "enabled": true,
                "scopes": [
                    "[subscription().id]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ResourceHealth",
                            "containsAny": null
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.currentHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Available",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Unavailable",
                                    "containsAny": null
                                },
                                {
                                    "field": "properties.previousHealthStatus",
                                    "equals": "Degraded",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "properties.cause",
                                    "equals": "PlatformInitiated",
                                    "containsAny": null
                                }
                            ]
                        },
                        {
                            "anyOf": [
                                {
                                    "field": "status",
                                    "equals": "Active",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Resolved",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "In Progress",
                                    "containsAny": null
                                },
                                {
                                    "field": "status",
                                    "equals": "Updated",
                                    "containsAny": null
                                }
                            ]
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[parameters('actionGroupResourceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

U weet echter het beste welke configuraties voor u effectief zijn, dus gebruik de tools die u in deze documentatie wordt geleerd om uw eigen aanpassing te maken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over resourcestatus:
-  [Overzicht van Azure Resource Health](Resource-health-overview.md)
-  [Resourcetypen en statuscontroles die beschikbaar zijn via Azure Resource Health](resource-health-checks-resource-types.md)


Waarschuwingen voor servicestatus maken:
-  [Waarschuwingen configureren voor servicestatus](../azure-monitor/platform/alerts-activity-log-service-notifications.md) 
-  [Gebeurtenisschema azure-activiteitslogboek](../azure-monitor/platform/activity-log-schema.md)
