---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 25928ef35da1ce4b3824303a5d46749c32aa701f
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69626320"
---
| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Resources per [resource groep](../articles/azure-resource-manager/resource-group-overview.md#resource-groups), per resource type |800 |Sommige resource typen kunnen de limiet van 800 overschrijden. Zie [resources niet beperkt tot 800 exemplaren per resource groep](../articles/azure-resource-manager/resources-without-rg-limit.md). |
| Implementaties per resource groep in de implementatie geschiedenis |800<sup>1</sup> |800 |
| Bronnen per implementatie |800 |800 |
| Beheer vergrendelingen per uniek bereik |20 |20 |
| Aantal Tags per resource of resource groep |50 |50 |
| Sleutel lengte van tag |512 |512 |
| Lengte van label waarde |256 |256 |

<sup>1</sup> Als u de limiet van 800 implementaties per resource groep bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Het verwijderen van een item uit de implementatie geschiedenis heeft geen invloed op de geïmplementeerde resources. U kunt vermeldingen uit de geschiedenis verwijderen met [AZ Group Deployment delete](/cli/azure/group/deployment) voor Azure CLI of [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) in Power shell.  Zie [Remove-Deployments. ps1](https://gist.github.com/bmoore-msft/ed33fb940dafb09380174b7fca57651f)voor een Power shell-script dat het verwijderen van implementaties automatiseert in een scenario voor continue integratie en continue levering (CI/cd).

#### <a name="template-limits"></a>Sjabloon limieten

| Value | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Parameters |256 |256 |
| Variabelen |256 |256 |
| Bronnen (met inbegrip van het aantal kopieën) |800 |800 |
| outputs |64 |64 |
| Expressie voor sjabloon |24.576 tekens |24.576 tekens |
| Resources in geëxporteerde sjablonen |200 |200 | 
| Sjabloon grootte |4 MB |4 MB |
| Parameter bestands grootte |64 kB |64 kB |

U kunt enkele sjabloon limieten overschrijden met behulp van een geneste sjabloon. Zie voor meer informatie [gekoppelde sjablonen gebruiken wanneer u Azure-resources implementeert](../articles/azure-resource-manager/resource-group-linked-templates.md). Als u het aantal para meters, variabelen of uitvoer wilt reduceren, kunt u verschillende waarden combi neren in een-object. Zie [objecten als para meters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)voor meer informatie.
