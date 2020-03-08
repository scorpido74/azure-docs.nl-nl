---
title: Resources implementeren in beheer groep
description: Hierin wordt beschreven hoe u resources kunt implementeren in het bereik van de beheer groep in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: ae561468531b0c3fa584a02793c58ee64ca3610f
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78894879"
---
# <a name="create-resources-at-the-management-group-level"></a>Resources op het niveau van de beheer groep maken

Doorgaans implementeert u Azure-resources in een resource groep in uw Azure-abonnement. U kunt echter ook resources maken op:

* [abonnements niveau](deploy-to-subscription.md)
* niveau beheer groep (in dit artikel)
* [Tenant niveau](deploy-to-tenant.md)

U kunt implementaties op beheer groeps niveau gebruiken om acties uit te voeren die zinvol zijn op dat niveau, zoals [het toewijzen van toegangs beheer op basis van rollen of het](../../role-based-access-control/overview.md) Toep assen van [beleid](../../governance/policy/overview.md).

## <a name="supported-resources"></a>Ondersteunde resources

U kunt de volgende resource typen implementeren op het niveau van de beheer groep:

* [implementaties](/azure/templates/microsoft.resources/deployments) : voor geneste sjablonen die worden geïmplementeerd op abonnementen of resource groepen.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Het schema dat u voor de implementaties van beheer groepen gebruikt, verschilt van het schema voor de implementatie van de resource groep.

Voor sjablonen gebruikt u:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Gebruik voor parameter bestanden:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentParameters.json#
```

## <a name="deployment-commands"></a>Implementatie opdrachten

De opdrachten voor het implementeren van beheer groepen verschillen van de opdrachten voor het implementeren van resource groepen.

Voor Azure PowerShell gebruikt u [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment). 

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -ManagementGroupId "myMG" `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json
```

Gebruik voor REST API [implementaties-maken voor het bereik van de beheer groep](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van de implementatie

Voor implementaties op beheer groepniveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy. json** implementeert, wordt er een standaard implementatie naam van **azuredeploy**gemaakt.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de fout code `InvalidDeploymentLocation`krijgt, moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloon functies gebruiken

Voor implementaties van een beheer groep gelden enkele belang rijke aandachtspunten bij het gebruik van sjabloon functies:

* De functie [resourceGroup ()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functie [Subscription ()](template-functions-resource.md#subscription) wordt **niet** ondersteund.
* De functies [Reference ()](template-functions-resource.md#reference) en [List ()](template-functions-resource.md#list) worden ondersteund.
* De functie [resourceId ()](template-functions-resource.md#resourceid) wordt ondersteund. Gebruik deze om de resource-ID op te halen voor resources die worden gebruikt bij implementaties op beheer groepniveau. Geef geen waarde op voor de para meter van de resource groep.

  Als u bijvoorbeeld de resource-ID voor een beleids definitie wilt ophalen, gebruikt u:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  De geretourneerde Resource-ID heeft de volgende indeling:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Beleidsregels maken

### <a name="define-policy"></a>Beleid definiëren

In het volgende voor beeld ziet u hoe u een beleid kunt [definiëren](../../governance/policy/concepts/definition-structure.md) op het niveau van de beheer groep.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>Beleid toewijzen

In het volgende voor beeld wordt een bestaande beleids definitie toegewezen aan de beheer groep. Als het beleid para meters accepteert, geeft u ze als een object. Als het beleid geen para meters heeft, gebruikt u het standaard lege object.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="template-sample"></a>Voor beeld van sjabloon

* [Maak een resource groep, een beleid en een beleids toewijzing](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Volgende stappen

* Zie [toegang tot Azure-resources beheren met RBAC en Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings. json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)(Engelstalig) voor een voor beeld van de implementatie van werk ruimte-instellingen voor Azure Security Center.
* Zie [ontwerp sjablonen](template-syntax.md)voor meer informatie over het maken van Azure Resource Manager sjablonen.
* Zie [sjabloon functies](template-functions.md)voor een lijst met de beschik bare functies in een sjabloon.