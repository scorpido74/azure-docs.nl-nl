---
title: Resources implementeren in beheergroep
description: Beschrijft hoe u resources implementeert in het bereik van de beheergroep in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460310"
---
# <a name="create-resources-at-the-management-group-level"></a>Resources maken op het niveau van de beheergroep

Naarmate uw organisatie volwassen wordt, moet u mogelijk [beleid](../../governance/policy/overview.md) of [op rollen gebaseerde toegangscontroles](../../role-based-access-control/overview.md) voor een beheergroep definiëren en toewijzen. Met sjablonen op managementgroepniveau u beleid declaratief toepassen en rollen toewijzen op het niveau van de beheergroep.

## <a name="supported-resources"></a>Ondersteunde resources

U de volgende resourcetypen implementeren op het niveau van de beheergroep:

* [implementaties](/azure/templates/microsoft.resources/deployments) - voor geneste sjablonen die worden geïmplementeerd op abonnementen of resourcegroepen.
* [beleidToewijzingen](/azure/templates/microsoft.authorization/policyassignments)
* [beleidsdefinities](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinities](/azure/templates/microsoft.authorization/policysetdefinitions)
* [rolOpdrachten](/azure/templates/microsoft.authorization/roleassignments)
* [rolDefinities](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Het schema dat u gebruikt voor implementaties van beheergroepen is anders dan het schema voor implementaties van resourcegroepen.

Gebruik voor sjablonen:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

Het schema voor een parameterbestand is hetzelfde voor alle implementatiescopes. Gebruik voor parameterbestanden:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Implementatieopdrachten

De opdrachten voor implementaties van beheergroepen zijn anders dan de opdrachten voor implementaties van resourcegroepen.

Voor Azure CLI gebruikt u [az-implementatiemg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

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

Voor REST API gebruikt u [Implementaties - Create At Management Group Scope](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van implementatie

Voor implementaties op managementgroepniveau moet u een locatie voor de implementatie opgeven. De locatie van de implementatie staat los van de locatie van de resources die u implementeert. De implementatielocatie geeft aan waar implementatiegegevens moeten worden opgeslagen.

U een naam opgeven voor de implementatie of de standaardimplementatienaam gebruiken. De standaardnaam is de naam van het sjabloonbestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy.json** implementeert, wordt een standaardimplementatienaam van **azuredeploy gemaakt.**

Voor elke implementatienaam is de locatie onveranderlijk. U geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de `InvalidDeploymentLocation`foutcode krijgt, gebruikt u een andere naam of dezelfde locatie als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloonfuncties gebruiken

Voor implementaties van beheergroepen zijn er enkele belangrijke overwegingen bij het gebruik van sjabloonfuncties:

* De functie [resourceGroup()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functie [abonnement()](template-functions-resource.md#subscription) wordt **niet** ondersteund.
* De [functies referentie()](template-functions-resource.md#reference) en [list()](template-functions-resource.md#list) worden ondersteund.
* De functie [resourceId()](template-functions-resource.md#resourceid) wordt ondersteund. Gebruik deze om de resource-id op te halen voor resources die worden gebruikt bij implementaties op managementgroepniveau. Geef geen waarde op voor de parameter resourcegroep.

  Als u bijvoorbeeld de resource-id voor een beleidsdefinitie wilt opvragen, gebruikt u het als:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  De geretourneerde resource-id heeft de volgende indeling:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Beleidsregels maken

### <a name="define-policy"></a>Beleid definiëren

In het volgende voorbeeld ziet u hoe u een beleid op het niveau van de beheergroep [definiëren.](../../governance/policy/concepts/definition-structure.md)

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

In het volgende voorbeeld wordt een bestaande beleidsdefinitie aan de beheergroep toegedeeld. Als het beleid parameters neemt, geeft u deze op als object. Als het beleid geen parameters aanneemt, gebruikt u het standaard lege object.

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

## <a name="template-sample"></a>Voorbeeld van sjabloon

* [Maak een resourcegroep, een beleid en een beleidstoewijzing](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>Volgende stappen

* Zie [Toegang tot Azure-resources beheren met RBAC- en Azure Resource Manager-sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* Zie [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)voor een voorbeeld van het implementeren van werkruimte-instellingen voor Azure Security Center.
* U ook sjablonen implementeren op [abonnementsniveau](deploy-to-subscription.md) en [tenantniveau.](deploy-to-tenant.md)
