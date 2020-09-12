---
title: Resources implementeren in beheer groep
description: Hierin wordt beschreven hoe u resources kunt implementeren in het bereik van de beheer groep in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 2265f1d31176052c7e7c358ee8ed4cb06fb50ee7
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469792"
---
# <a name="create-resources-at-the-management-group-level"></a>Resources op het niveau van de beheer groep maken

Als uw organisatie is gerijpt, kunt u een Azure Resource Manager sjabloon (ARM-sjabloon) implementeren om resources te maken op het niveau van de beheer groep. U moet bijvoorbeeld [beleids regels](../../governance/policy/overview.md) of [toegangs beheer op basis van rollen (Azure RBAC)](../../role-based-access-control/overview.md) voor een beheer groep definiëren en toewijzen. Met beheer groeps niveau sjablonen kunt u declaratief beleid Toep assen en rollen toewijzen op het niveau van de beheer groep.

## <a name="supported-resources"></a>Ondersteunde resources

Niet alle resource typen kunnen worden geïmplementeerd op het niveau van de beheer groep. In deze sectie wordt een overzicht gegeven van de resource typen die worden ondersteund.

Gebruik voor Azure-blauw drukken:

* [artefacten](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blauw drukken](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [lager](/azure/templates/microsoft.blueprint/blueprints/versions)

Gebruik voor Azure-beleid:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [herstel](/azure/templates/microsoft.policyinsights/remediations)

Gebruik voor op rollen gebaseerd toegangs beheer:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Voor geneste sjablonen die worden geïmplementeerd op abonnementen of resource groepen, gebruikt u:

* [implementaties](/azure/templates/microsoft.resources/deployments)

Gebruik voor het beheren van uw resources:

* [Koptags](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Schema

Het schema dat u voor de implementaties van beheer groepen gebruikt, verschilt van het schema voor de implementatie van de resource groep.

Voor sjablonen gebruikt u:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Het schema voor een parameter bestand is hetzelfde voor alle implementatie bereiken. Gebruik voor parameter bestanden:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Implementatie opdrachten

De opdrachten voor het implementeren van beheer groepen verschillen van de opdrachten voor het implementeren van resource groepen.

Gebruik [AZ Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)voor Azure cli:

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Voor Azure PowerShell gebruikt u [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Gebruik voor REST API [implementaties-maken voor het bereik van de beheer groep](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van de implementatie

Voor implementaties op beheer groepniveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy.jsop** implementeert, maakt de standaard implementatie naam **azuredeploy**.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de fout code krijgt `InvalidDeploymentLocation` , moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="deployment-scopes"></a>Implementatie bereiken

Wanneer u implementeert in een beheer groep, kunt u de beheer groep die is opgegeven in de implementatie opdracht of andere beheer groepen in de Tenant richten. U kunt ook abonnementen of resource groepen binnen een beheer groep richten. De gebruiker die de sjabloon implementeert, moet toegang hebben tot het opgegeven bereik.

Resources die zijn gedefinieerd in de sectie resources van de sjabloon, worden toegepast op de beheer groep via de implementatie opdracht.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

Als u een andere beheer groep wilt instellen, voegt u een geneste implementatie toe en geeft u de `scope` eigenschap op.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template-with-resources-in-different-mg
                }
            }
        }
    ],
    "outputs": {}
}
```

Gebruik een geneste implementatie en de eigenschap om een abonnement in de beheer groep te richten `subscriptionId` . Als u een resource groep in het abonnement wilt richten, voegt u een andere geneste implementatie en de `resourceGroup` eigenschap toe.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template-with-resources-in-resource-group
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

Voor het gebruik van een implementatie van een beheer groep voor het maken van een resource groep in een abonnement en het implementeren van een opslag account voor die resource groep, Zie [implementeren naar abonnement en resource groep](#deploy-to-subscription-and-resource-group).

## <a name="use-template-functions"></a>Sjabloon functies gebruiken

Voor implementaties van een beheer groep gelden enkele belang rijke aandachtspunten bij het gebruik van sjabloon functies:

* De functie [resourceGroup ()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functie [Subscription ()](template-functions-resource.md#subscription) wordt **niet** ondersteund.
* De functies [Reference ()](template-functions-resource.md#reference) en [List ()](template-functions-resource.md#list) worden ondersteund.
* Gebruik niet de functie [resourceId ()](template-functions-resource.md#resourceid) voor resources die zijn geïmplementeerd in de beheer groep.

  Gebruik in plaats daarvan de functie [extensionResourceId ()](template-functions-resource.md#extensionresourceid) voor resources die zijn geïmplementeerd als uitbrei dingen van de beheer groep. Aangepaste beleids definities die worden geïmplementeerd in de beheer groep zijn uitbrei dingen van de beheer groep.

  Als u de resource-ID voor een aangepaste beleids definitie op het niveau van de beheer groep wilt ophalen, gebruikt u:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Gebruik de functie [tenantResourceId](template-functions-resource.md#tenantresourceid) voor Tenant bronnen die beschikbaar zijn in de beheer groep. Ingebouwde beleids definities zijn resources op Tenant niveau.

  Als u de resource-ID voor een ingebouwde beleids definitie wilt ophalen, gebruikt u:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

In het volgende voor beeld ziet u hoe u een beleid [definieert](../../governance/policy/concepts/definition-structure.md) op het niveau van de beheer groep en dit toewijst.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Implementeren naar het abonnement en de resource groep

Op basis van de implementatie van een beheer groep kunt u een abonnement in de beheer groep richten. In het volgende voor beeld wordt een resource groep gemaakt binnen een abonnement en wordt een opslag account geïmplementeerd op die resource groep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure-roltoewijzingen toevoegen met Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* U kunt ook sjablonen implementeren op [abonnements niveau](deploy-to-subscription.md) en [Tenant niveau](deploy-to-tenant.md).
