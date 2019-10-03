---
title: Azure-implementatie quotum overschreden
description: Hierin wordt beschreven hoe u de fout van het gebruik van meer dan 800 implementaties in de geschiedenis van de resource groep kunt oplossen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719433"
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

U kunt het huidige aantal in de implementatie geschiedenis ophalen met de volgende opdracht:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Gebruik de opdracht [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) om implementaties uit de geschiedenis te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

U kunt het huidige aantal in de implementatie geschiedenis ophalen met de volgende opdracht:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

