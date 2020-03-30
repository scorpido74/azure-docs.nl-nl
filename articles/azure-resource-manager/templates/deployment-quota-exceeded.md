---
title: Implementatiequotum overschreden
description: Beschrijft hoe u de fout oplossen van het hebben van meer dan 800 implementaties in de geschiedenis van de resourcegroep.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245086"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Fout oplossen wanneer het aantal implementaties hoger is dan 800

Elke resourcegroep is beperkt tot 800 implementaties in de implementatiegeschiedenis. In dit artikel wordt de fout beschreven die u ontvangt wanneer een implementatie mislukt omdat deze de toegestane 800-implementaties overschrijdt. Als u deze fout wilt oplossen, verwijdert u implementaties uit de resourcegroepgeschiedenis. Als u een implementatie uit de geschiedenis verwijderde, heeft dit geen invloed op de resources die zijn geïmplementeerd.

## <a name="symptom"></a>Symptoom

Tijdens de implementatie ontvangt u een fout waarin staat dat de huidige implementatie het quotum van 800 implementaties zal overschrijden.

## <a name="solution"></a>Oplossing

### <a name="azure-cli"></a>Azure-CLI

Gebruik de opdracht [Verwijderen van az-implementatiegroepen](/cli/azure/group/deployment) om implementaties uit de geschiedenis te verwijderen.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Als u alle implementaties ouder dan vijf dagen wilt verwijderen, gebruikt u:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

U het huidige aantal in de implementatiegeschiedenis opde volgende opdracht krijgen:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de opdracht [Verwijderen-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) om implementaties uit de geschiedenis te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Als u alle implementaties ouder dan vijf dagen wilt verwijderen, gebruikt u:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

U het huidige aantal in de implementatiegeschiedenis opde volgende opdracht krijgen:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Oplossingen van derden

De volgende externe oplossingen hebben betrekking op specifieke scenario's:

* [Azure Logic Apps- en PowerShell-oplossingen](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps Uitbreiding](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
