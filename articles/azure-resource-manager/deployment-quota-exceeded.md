---
title: Azure-implementatie quotum overschreden
description: Hierin wordt beschreven hoe u de fout van het gebruik van meer dan 800 implementaties in de geschiedenis van de resource groep kunt oplossen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.author: tomfitz
ms.openlocfilehash: 7f9e1b6b8518ebc03f051e379e4707dd1864e003
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578976"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Fout oplossen wanneer het aantal implementaties groter is dan 800

Elke resource groep is beperkt tot 800 implementaties in de implementatie geschiedenis. In dit artikel wordt de fout beschreven die u ontvangt wanneer een implementatie mislukt, omdat deze de toegestane 800-implementaties zou overschrijden. U kunt deze fout oplossen door implementaties te verwijderen uit de geschiedenis van de resource groep. Het verwijderen van een implementatie uit de geschiedenis heeft geen invloed op de resources die zijn geïmplementeerd.

## <a name="symptom"></a>Symptoom

Tijdens de implementatie wordt er een fout bericht weer gegeven met de mede deling dat de huidige implementatie het quotum van 800 implementaties overschrijdt.

## <a name="solution"></a>Oplossing

### <a name="azure-cli"></a>Azure-CLI

Gebruik de opdracht [AZ Group Deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) om implementaties uit de geschiedenis te verwijderen.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Als u alle implementaties die ouder zijn dan vijf dagen wilt verwijderen, gebruikt u:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az group deployment list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az group deployment delete --resource-group exampleGroup --name $deployment
done
```

U kunt het huidige aantal in de implementatie geschiedenis ophalen met de volgende opdracht:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de opdracht [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) om implementaties uit de geschiedenis te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Als u alle implementaties die ouder zijn dan vijf dagen wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

U kunt het huidige aantal in de implementatie geschiedenis ophalen met de volgende opdracht:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Oplossingen van derden

De volgende externe oplossingen hebben betrekking op specifieke scenario's:

* [Azure Logic Apps-en Power shell-oplossingen](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps-extensie](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
