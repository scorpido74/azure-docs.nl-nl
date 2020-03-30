---
title: Resourcegroepen beheren - Azure PowerShell
description: Gebruik Azure PowerShell om uw brongroepen te beheren via Azure Resource Manager. Hier ziet u hoe u resourcegroepen maakt, opdook en verwijdert.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248357"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure Resource Manager-brongroepen beheren met Azure PowerShell

Meer informatie over het gebruik van Azure PowerShell met [Azure Resource Manager](overview.md) om uw Azure-brongroepen te beheren. Zie [Azure-resources beheren met Azure PowerShell](manage-resources-powershell.md)voor het beheren van Azure-resources.

Andere artikelen over het beheren van resourcegroepen:

- [Azure-brongroepen beheren met behulp van de Azure-portal](manage-resources-portal.md)
- [Azure-brongroepen beheren met Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Wat is een resourcegroep

Een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is. Voeg over het algemeen resources toe die dezelfde levenscyclus delen aan dezelfde resourcegroep, zodat u ze eenvoudig implementeren, bijwerken en verwijderen als groep.

De resourcegroep slaat metagegevens op over de resources. Dat is de reden waarom u moet aangeven waar die metagegevens moeten worden opgeslagen als u een locatie voor de resourcegroep opgeeft. In verband met nalevingsvereisten moet u er mogelijk voor zorgen dat uw gegevens worden opgeslagen in een bepaalde regio.

De resourcegroep slaat metagegevens op over de resources. Wanneer u een locatie voor de resourcegroep opgeeft, geeft u op waar die metagegevens zijn opgeslagen.

## <a name="create-resource-groups"></a>Resourcegroepen maken

Met het volgende PowerShell-script wordt een resourcegroep gemaakt en wordt de resourcegroep weergegeven.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Resourcegroepen aanbieden

In het volgende PowerShell-script worden de brongroepen onder uw abonnement weergegeven.

```azurepowershell-interactive
Get-AzResourceGroup
```

Eén resourcegroep op te halen:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Resourcegroepen verwijderen

Met het volgende PowerShell-script wordt een brongroep verwijderd:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Zie het verwijderen van [azure resource manager-bronnen](delete-resource-group.md)voor meer informatie over hoe Azure Resource Manager de verwijdering van resources bestelt.

## <a name="deploy-resources-to-an-existing-resource-group"></a>Resources implementeren voor een bestaande resourcegroep

Zie [Resources implementeren voor een bestaande resourcegroep](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Zie [Implementatie test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)als u een implementatie van een resourcegroep wilt valideren.

## <a name="deploy-a-resource-group-and-resources"></a>Een resourcegroep en resources implementeren

U een resourcegroep maken en resources naar de groep implementeren met behulp van een resourcemanagersjabloon. Zie [Resourcegroep maken en resources implementeren](../templates/deploy-to-subscription.md#resource-group-and-resources)voor meer informatie.

## <a name="redeploy-when-deployment-fails"></a>Opnieuw implementeren wanneer implementatie mislukt

Deze functie wordt ook wel *Rollback on error genoemd.* Zie [Opnieuw implementeren wanneer de implementatie mislukt](../templates/rollback-on-error.md)voor meer informatie .

## <a name="move-to-another-resource-group-or-subscription"></a>Naar een andere resourcegroep of -abonnement gaan

U de resources in de groep verplaatsen naar een andere resourcegroep. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Brongroepen vergrendelen

Vergrendeling voorkomt dat andere gebruikers in uw organisatie per ongeluk kritieke bronnen verwijderen of wijzigen, zoals Azure-abonnement, resourcegroep of resource. 

In het volgende script wordt een resourcegroep vergrendeld, zodat de resourcegroep niet kan worden verwijderd.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

In het volgende script worden alle vergrendelingen voor een resourcegroep opgevolgd:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](lock-resources.md).

## <a name="tag-resource-groups"></a>Brongroepen taggen

U tags toepassen op resourcegroepen en resources om uw assets logisch te ordenen. Zie Tags [gebruiken om uw Azure-bronnen te ordenen](tag-resources.md#powershell)voor meer informatie.

## <a name="export-resource-groups-to-templates"></a>Brongroepen exporteren naar sjablonen

Nadat u uw resourcegroep hebt ingesteld, u een resourcemanagersjabloon voor de resourcegroep weergeven. Het exporteren van de sjabloon biedt twee voordelen:

- Automatiseer toekomstige implementaties van de oplossing omdat de sjabloon de volledige infrastructuur bevat.
- Lees de syntaxis van sjablonen door te kijken naar de Json (JavaScript Object Notatie) die uw oplossing vertegenwoordigt.

Als u alle resources in een resourcegroep wilt exporteren, gebruikt u de cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) en geeft u de naam van de resourcegroep op.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Het slaat de sjabloon op als een lokaal bestand.

In plaats van alle resources in de resourcegroep te exporteren, u selecteren welke resources u wilt exporteren.

Als u één resource wilt exporteren, geeft u die resource-id door.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Als u meer dan één resource wilt exporteren, geeft u de resource-id's door in een array.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Wanneer u de sjabloon exporteert, u opgeven of parameters in de sjabloon worden gebruikt. Standaard zijn parameters voor resourcenamen opgenomen, maar ze hebben geen standaardwaarde. U moet die parameterwaarde doorgeven tijdens de implementatie.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

In de resource wordt de parameter gebruikt voor de naam.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Als u `-IncludeParameterDefaultValue` de parameter gebruikt bij het exporteren van de sjabloon, bevat de parameter sjabloon een standaardwaarde die is ingesteld op de huidige waarde. U die standaardwaarde gebruiken of de standaardwaarde overschrijven door een andere waarde door te geven.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Als u `-SkipResourceNameParameterization` de parameter gebruikt bij het exporteren van de sjabloon, worden parameters voor resourcenamen niet opgenomen in de sjabloon. In plaats daarvan wordt de resourcenaam rechtstreeks op de resource ingesteld op de huidige waarde. U de naam niet aanpassen tijdens de implementatie.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

De functie exportsjabloon biedt geen ondersteuning voor het exporteren van Azure Data Factory-resources. Zie [Een gegevensfabriek kopiëren of klonen in Azure Data Factory](https://aka.ms/exportTemplateViaAdf)voor meer informatie over hoe u gegevensfabriekresources exporteren.

Als u resources wilt exporteren die zijn gemaakt via een klassiek implementatiemodel, moet u [deze migreren naar het implementatiemodel resourcebeheer](https://aka.ms/migrateclassicresourcetoarm).

Zie Exporteren naar sjabloon met [één en meerdere resources naar een sjabloon in azure portal](../templates/export-template-portal.md)voor meer informatie.

## <a name="manage-access-to-resource-groups"></a>Toegang tot resourcegroepen beheren

[RBAC (Role-based access control)](../../role-based-access-control/overview.md) is de manier waarop u de toegang tot bronnen in Azure beheert. Zie [Toegang beheren met RBAC en Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Overzicht azure [resource manager](overview.md)voor azure resource manager.
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](../templates/template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .