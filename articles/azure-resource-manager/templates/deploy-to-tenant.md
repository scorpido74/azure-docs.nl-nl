---
title: Resources implementeren voor tenant
description: Beschrijft hoe u resources implementeert op de tenantscope in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460259"
---
# <a name="create-resources-at-the-tenant-level"></a>Resources maken op tenantniveau

Naarmate uw organisatie volwassen wordt, moet u mogelijk [beleid](../../governance/policy/overview.md) of [op rollen gebaseerde toegangsbesturingselementen](../../role-based-access-control/overview.md) voor uw Azure AD-tenant definiëren en toewijzen. Met sjablonen op tenantniveau u beleid declaratief toepassen en rollen toewijzen op globaal niveau.

## <a name="supported-resources"></a>Ondersteunde resources

U de volgende resourcetypen op tenantniveau implementeren:

* [implementaties](/azure/templates/microsoft.resources/deployments) - voor geneste sjablonen die worden geïmplementeerd in beheergroepen of abonnementen.
* [beleidToewijzingen](/azure/templates/microsoft.authorization/policyassignments)
* [beleidsdefinities](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinities](/azure/templates/microsoft.authorization/policysetdefinitions)
* [rolOpdrachten](/azure/templates/microsoft.authorization/roleassignments)
* [rolDefinities](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Het schema dat u gebruikt voor tenantimplementaties is anders dan het schema voor implementaties van resourcegroepen.

Gebruik voor sjablonen:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Het schema voor een parameterbestand is hetzelfde voor alle implementatiescopes. Gebruik voor parameterbestanden:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Vereiste toegang

De hoofdprincipal die de sjabloon implementeert, moet machtigingen hebben om resources te maken op het tenantbereik. De opdrachtgever moet toestemming hebben om`Microsoft.Resources/deployments/*`de implementatieacties uit te voeren ( ) en om de resources te maken die in de sjabloon zijn gedefinieerd. Als u bijvoorbeeld een beheergroep wilt maken, moet de hoofdgever toestemming voor contribuanten hebben bij de tenantscope. Als u roltoewijzingen wilt maken, moet de opdrachtgever toestemming voor de eigenaar hebben.

De globale beheerder voor de Azure Active Directory heeft niet automatisch toestemming om rollen toe te wijzen. Als u sjabloonimplementaties op de tenantscope wilt inschakelen, moet de globale beheerder de volgende stappen uitvoeren:

1. Verhoog accounttoegang zodat de globale beheerder rollen kan toewijzen. Zie [Toegang verheffen om alle Azure-abonnementen en beheergroepen te beheren voor](../../role-based-access-control/elevate-access-global-admin.md)meer informatie.

1. Wijs eigenaar of bijdrager toe aan de principal die de sjablonen moet implementeren.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

De principal heeft nu de vereiste machtigingen om de sjabloon te implementeren.

## <a name="deployment-commands"></a>Implementatieopdrachten

De opdrachten voor tenantimplementaties zijn anders dan de opdrachten voor implementaties van resourcegroepen.

Gebruik voor Azure CLI [het maken van az-implementatietenant:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Gebruik voor Azure PowerShell [Nieuwe AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Voor REST API gebruikt u [Implementaties - Tenantbereik maken of bijwerken](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van implementatie

Voor implementaties op tenantniveau moet u een locatie voor de implementatie opgeven. De locatie van de implementatie staat los van de locatie van de resources die u implementeert. De implementatielocatie geeft aan waar implementatiegegevens moeten worden opgeslagen.

U een naam opgeven voor de implementatie of de standaardimplementatienaam gebruiken. De standaardnaam is de naam van het sjabloonbestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy.json** implementeert, wordt een standaardimplementatienaam van **azuredeploy gemaakt.**

Voor elke implementatienaam is de locatie onveranderlijk. U geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de `InvalidDeploymentLocation`foutcode krijgt, gebruikt u een andere naam of dezelfde locatie als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloonfuncties gebruiken

Voor tenantimplementaties zijn er enkele belangrijke overwegingen bij het gebruik van sjabloonfuncties:

* De functie [resourceGroup()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functie [abonnement()](template-functions-resource.md#subscription) wordt **niet** ondersteund.
* De [functies referentie()](template-functions-resource.md#reference) en [list()](template-functions-resource.md#list) worden ondersteund.
* Gebruik de functie [tenantResourceId()](template-functions-resource.md#tenantresourceid) om de resource-id op te halen voor resources die op tenantniveau zijn geïmplementeerd.

  Als u bijvoorbeeld de resource-id voor een beleidsdefinitie wilt opvragen, gebruikt u het als:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  De geretourneerde resource-id heeft de volgende indeling:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Beheergroep maken

Met [de volgende sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) wordt een beheergroep gemaakt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Rol toewijzen

In [de volgende sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) wordt een rol aan de tenantscope toegeten.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Toegang tot Azure-resources beheren met RBAC- en Azure Resource Manager-sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* U ook sjablonen implementeren op [abonnementsniveau](deploy-to-subscription.md) of [managementgroepniveau.](deploy-to-management-group.md)
