---
title: Resources implementeren voor Tenant
description: Hierin wordt beschreven hoe u resources implementeert in het Tenant bereik in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79460259"
---
# <a name="create-resources-at-the-tenant-level"></a>Resources maken op Tenant niveau

Als uw organisatie is gerijpt, moet u mogelijk [beleid](../../governance/policy/overview.md) of op [rollen gebaseerde toegangs beheer](../../role-based-access-control/overview.md) voor uw Azure AD-Tenant definiëren en toewijzen. Met sjablonen op Tenant niveau kunt u declaratief beleid Toep assen en rollen toewijzen op globaal niveau.

## <a name="supported-resources"></a>Ondersteunde resources

U kunt de volgende bron typen implementeren op Tenant niveau:

* [implementaties](/azure/templates/microsoft.resources/deployments) : voor geneste sjablonen die worden geïmplementeerd op beheer groepen of-abonnementen.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Schema

Het schema dat u voor Tenant implementaties gebruikt, wijkt af van het schema voor implementaties van resource groepen.

Voor sjablonen gebruikt u:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

Het schema voor een parameter bestand is hetzelfde voor alle implementatie bereiken. Gebruik voor parameter bestanden:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Vereiste toegang

De principal-implementatie van de sjabloon moet machtigingen hebben om resources te maken in het Tenant bereik. De principal moet gemachtigd zijn om de implementatie acties (`Microsoft.Resources/deployments/*`) uit te voeren en om de resources te maken die in de sjabloon zijn gedefinieerd. Als u bijvoorbeeld een beheer groep wilt maken, moet de principal de machtiging Inzender hebben op het Tenant bereik. Als u roltoewijzingen wilt maken, moet de principal eigenaar toestemming hebben.

De globale beheerder voor de Azure Active Directory is niet automatisch gemachtigd om rollen toe te wijzen. De globale beheerder moet de volgende stappen uitvoeren om sjabloon implementaties in te scha kelen in het Tenant bereik:

1. De toegang tot het account verhogen zodat de globale beheerder rollen kan toewijzen. Zie [toegang verhogen voor het beheer van alle Azure-abonnementen en-beheer groepen](../../role-based-access-control/elevate-access-global-admin.md)voor meer informatie.

1. Wijs eigenaar of bijdrager toe aan de principal die de sjablonen moet implementeren.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

De principal heeft nu de vereiste machtigingen voor het implementeren van de sjabloon.

## <a name="deployment-commands"></a>Implementatie opdrachten

De opdrachten voor Tenant implementaties wijken af van de opdrachten voor het implementeren van resource groepen.

Gebruik [AZ Deployment Tenant Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)voor Azure cli:

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Voor Azure PowerShell gebruikt u [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Gebruik voor REST API [implementaties-maken of bijwerken in het Tenant bereik](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Locatie en naam van de implementatie

Voor implementaties op Tenant niveau moet u een locatie opgeven voor de implementatie. De locatie van de implementatie is gescheiden van de locatie van de resources die u implementeert. De implementatie locatie geeft aan waar de implementatie gegevens moeten worden opgeslagen.

U kunt een naam opgeven voor de implementatie of de naam van de standaard implementatie gebruiken. De standaard naam is de naam van het sjabloon bestand. Als u bijvoorbeeld een sjabloon met de naam **azuredeploy. json** implementeert, wordt er een standaard implementatie naam van **azuredeploy**gemaakt.

Voor elke implementatie naam is de locatie onveranderbaar. U kunt geen implementatie op één locatie maken wanneer er een bestaande implementatie met dezelfde naam op een andere locatie is. Als u de fout code `InvalidDeploymentLocation`krijgt, moet u een andere naam of dezelfde locatie gebruiken als de vorige implementatie voor die naam.

## <a name="use-template-functions"></a>Sjabloon functies gebruiken

Voor Tenant implementaties gelden enkele belang rijke aandachtspunten bij het gebruik van sjabloon functies:

* De functie [resourceGroup ()](template-functions-resource.md#resourcegroup) wordt **niet** ondersteund.
* De functie [Subscription ()](template-functions-resource.md#subscription) wordt **niet** ondersteund.
* De functies [Reference ()](template-functions-resource.md#reference) en [List ()](template-functions-resource.md#list) worden ondersteund.
* Gebruik de functie [tenantResourceId ()](template-functions-resource.md#tenantresourceid) om de resource-id op te halen voor resources die worden geïmplementeerd op Tenant niveau.

  Als u bijvoorbeeld de resource-ID voor een beleids definitie wilt ophalen, gebruikt u:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  De geretourneerde Resource-ID heeft de volgende indeling:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Beheergroep maken

Met de [volgende sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) maakt u een beheer groep.

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

De [volgende sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) wijst een rol toe aan het Tenant bereik.

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

* Zie [toegang tot Azure-resources beheren met RBAC en Azure Resource Manager sjablonen](../../role-based-access-control/role-assignments-template.md)voor meer informatie over het toewijzen van rollen.
* U kunt ook sjablonen implementeren op [abonnements niveau](deploy-to-subscription.md) of op het niveau van de [beheer groep](deploy-to-management-group.md).
