---
title: Het toevoegen van een labgebruiker automatiseren in Azure DevTest Labs | Microsoft Documenten
description: In dit artikel ziet u hoe u het toevoegen van een gebruiker aan een lab in Azure DevTest Labs automatiseert met Azure Resource Manager-sjablonen, PowerShell en CLI.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023619"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatiseer het toevoegen van een labgebruiker aan een lab in Azure DevTest Labs
Met Azure DevTest Labs u snel selfservice-dev-testomgevingen maken met behulp van de Azure-portal. Als u echter meerdere teams en verschillende DevTest Labs-instanties hebt, kan het automatiseren van het creatieproces tijd besparen. [Met Azure Resource Manager-sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/Environments) u laboratoria, vm's in laboratoria, aangepaste afbeeldingen, formules en gebruikers op een geautomatiseerde manier toevoegen. Dit artikel richt zich specifiek op het toevoegen van gebruikers aan een DevTest Labs-exemplaar.

Als u een gebruiker wilt toevoegen aan een lab, voegt u de gebruiker toe aan de rol **DevTest Labs-gebruikers** voor het lab. In dit artikel ziet u hoe u het toevoegen van een gebruiker aan een lab automatiseren op een van de volgende manieren:

- Azure Resource Manager-sjablonen
- Azure PowerShell-cmdlets 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen gebruiken
In de volgende sjabloon Resource Manager wordt een gebruiker opgegeven die moet worden toegevoegd aan de rol Van een lab voor de gebruiker van **DevTest Labs.** 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Als u de rol toekent in dezelfde sjabloon die het lab maakt, moet u een afhankelijkheid toevoegen tussen de bron voor roltoewijzing en het lab. Zie [Afhankelijkheden definiëren in](../azure-resource-manager/templates/define-resource-dependency.md) het artikel Azure Resource Manager Templates voor meer informatie.

### <a name="role-assignment-resource-information"></a>Brongegevens voor taaktoewijzing
De bron van de roltoewijzing moet het type en de naam opgeven.

Het eerste wat opvalt is dat het `Microsoft.Authorization/roleAssignments` type voor de resource niet is zoals het zou zijn voor een resourcegroep.  In plaats daarvan volgt `{provider-namespace}/{resource-type}/providers/roleAssignments`het resourcetype het patroon . In dit geval is `Microsoft.DevTestLab/labs/providers/roleAssignments`het resourcetype .

De naam van de roltoewijzing zelf moet wereldwijd uniek zijn.  De naam van de `{labName}/Microsoft.Authorization/{newGuid}`toewijzing gebruikt het patroon . De `newGuid` parameterwaarde voor de sjabloon is een parameterwaarde. Het zorgt ervoor dat de naam van de roltoewijzing uniek is. Omdat er geen sjabloonfuncties zijn voor het maken van GUIDs, moet u zelf een GUID genereren met behulp van een GUID-generatortool.  

In de sjabloon wordt de naam voor de `fullDevTestLabUserRoleName` roltoewijzing gedefinieerd door de variabele. De exacte regel van de sjabloon is:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Resourceeigenschappen voor taaktoewijzing
Een roltoewijzing zelf definieert drie eigenschappen. Het heeft `roleDefinitionId` `principalId`de `scope`, , en .

### <a name="role-definition"></a>Roldefinitie
De functiedefinitie-id is de tekenreeks-id voor de bestaande roldefinitie. De rol-ID bevindt zich in het formulier `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

De abonnements-ID wordt `subscription().subscriptionId` verkregen met behulp van sjabloonfunctie.  

Je moet de roldefinitie `DevTest Labs User` voor de ingebouwde rol krijgen. Als u de GUID voor de rol [DevTest Labs-gebruiker](../role-based-access-control/built-in-roles.md#devtest-labs-user) wilt krijgen, u de [REST-API voor roltoewijzingen](/rest/api/authorization/roleassignments) of de [cmdlet Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) gebruiken.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

De rol-ID wordt gedefinieerd in de `devTestLabUserRoleId`sectie variabelen en met de naam . In de sjabloon is de rol-ID ingesteld op: 1111111-0000-0000-1111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Hoofd-id
Hoofd-id is de object-id van de Active Directory-gebruiker, -groep of serviceprincipal die u als labgebruiker aan het lab wilt toevoegen. De sjabloon `ObjectId` gebruikt de parameter als parameter.

U de ObjectId krijgen met behulp van de cmdlets [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup of [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell. Deze cmdlets retourneren een enkele of lijsten met Active Directory-objecten met een ID-eigenschap, de object-id die u nodig hebt. In het volgende voorbeeld ziet u hoe u de object-id van één gebruiker bij een bedrijf krijgen.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

U ook de Azure Active Directory PowerShell-cmdlets gebruiken, waaronder [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)en [Get-MsolServicePrincipal.](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)

### <a name="scope"></a>Bereik
Scope geeft de resource- of resourcegroep op waarop de roltoewijzing moet worden toegepast. Voor resources is het bereik `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`in de vorm: . De sjabloon `subscription().subscriptionId` gebruikt de functie `subscription-id` om `resourceGroup().name` het onderdeel en `resource-group-name` de sjabloonfunctie in te vullen om het onderdeel in te vullen. Het gebruik van deze functies betekent dat het lab waaraan u een rol toevertrouwt, moet bestaan in het huidige abonnement en dezelfde resourcegroep waaraan de sjabloonimplementatie wordt uitgevoerd. Het laatste `resource-name`deel, is de naam van het lab. Deze waarde wordt ontvangen via de parameter sjabloon in dit voorbeeld. 

Het rolbereik in de sjabloon: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Het sjabloon implementeren
Maak eerst een parameterbestand (bijvoorbeeld azuredeploy.parameters.json) dat waarden doorgeeft voor parameters in de sjabloon Resourcemanager. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Gebruik vervolgens de powerdlet [Nieuw-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) om de sjabloon ResourceBeheer te implementeren. In de volgende voorbeeldopdracht wordt een persoon, groep of serviceprincipal toewijst aan de rol DevTest Labs-gebruiker voor een lab.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Het is belangrijk op te merken dat de naam van de groepsimplementatie en de GUID voor roltoewijzinguniek moeten zijn. Als u een resourcetoewijzing probeert te implementeren met een niet-unieke `RoleAssignmentUpdateNotPermitted` GUID, krijgt u een foutmelding.

Als u van plan bent de sjabloon meerdere keren te gebruiken om verschillende Active Directory-objecten toe te voegen aan de rol Van DevTest Labs-gebruikers voor uw lab, u overwegen dynamische objecten in uw PowerShell-opdracht te gebruiken. In het volgende voorbeeld wordt de cmdlet [Nieuw-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) gebruikt om de guid-implementatienaam en roltoewijzingguid van de resourcegroep dynamisch op te geven.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
Zoals besproken in de inleiding, maakt u een nieuwe Azure-roltoewijzing om een gebruiker toe te voegen aan de rol **DevTest Labs-gebruikers** voor het lab. In PowerShell doet u dit met de cmdlet [Nieuw-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Deze cmdlet heeft veel optionele parameters om flexibiliteit mogelijk te maken. Het `ObjectId` `SigninName`object `ServicePrincipalName` dat machtigingen wordt verleend, of kan worden opgegeven als het object dat machtigingen wordt verleend.  

Hier vindt u een voorbeeld van de opdracht Azure PowerShell waarmee een gebruiker wordt toegevoegd aan de rol DevTest Labs-gebruiker in het opgegeven lab.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Als u de resource wilt opgeven waaraan machtigingen worden `ResourceName`verleend, u worden opgegeven door een combinatie van , `ResourceType`of `ResourceGroup` door de `scope` parameter. Welke combinatie parameters ook wordt gebruikt, geef voldoende informatie aan de cmdlet om het Active Directory-object (gebruiker, groep of serviceprincipal), bereik (resourcegroep of resource) en roldefinitie op unieke wijze te identificeren.

## <a name="use-azure-command-line-interface-cli"></a>Cli (Azure Command Line Interface) gebruiken
In Azure CLI wordt het toevoegen van een `az role assignment create` labsgebruiker aan een lab gedaan met behulp van de opdracht. Zie [Toegang tot Azure-resources beheren met RBAC en Azure CLI](../role-based-access-control/role-assignments-cli.md)voor meer informatie over Azure CLI-cmdlets.

Het object dat toegang krijgt, kan `objectId` `signInName`worden `spn` opgegeven door de parameters. Het lab waartoe het object toegang krijgt, `scope` kan worden geïdentificeerd `resource-name`door `resource-type`de `resource-group` url of een combinatie van de , en parameters.

In het volgende voorbeeld van Azure CLI ziet u hoe u een persoon toevoegt aan de rol DevTest Labs-gebruiker voor het opgegeven lab.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Virtuele machines maken en beheren met DevTest Labs met behulp van de Azure CLI](devtest-lab-vmcli.md)
- [Een virtuele machine maken met DevTest Labs met Azure PowerShell](devtest-lab-vm-powershell.md)
- [Gebruik opdrachtregelgereedschappen om virtuele azure-machines van Azure DevTest Labs te starten en te stoppen](use-command-line-start-stop-virtual-machines.md)

