---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 1190798b234f9c73e02fda41c03ffa296246be63
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71975292"
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

<sup>1</sup> Als u de limiet van 800 implementaties per resource groep bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Het verwijderen van een item uit de implementatie geschiedenis heeft geen invloed op de geïmplementeerde resources. Zie voor meer informatie [oplossen fout bij aantal implementaties is groter dan 800](../articles/azure-resource-manager/deployment-quota-exceeded.md).

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
