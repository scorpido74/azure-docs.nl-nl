---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: b4338560d515289d3e9aedfa716ec7c3ace51131
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75392280"
---
| Bron | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Resources per [resource groep](../articles/azure-resource-manager/management/overview.md#resource-groups), per resource type |800 |Sommige resource typen kunnen de limiet van 800 overschrijden. Zie [resources niet beperkt tot 800 exemplaren per resource groep](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implementaties per resource groep in de implementatie geschiedenis |800<sup>1</sup> |800 |
| Bronnen per implementatie |800 |800 |
| Beheer vergrendelingen per uniek bereik |20 |20 |
| Aantal Tags per resource of resource groep |50 |50 |
| Sleutel lengte van tag |512 |512 |
| Lengte van label waarde |256 |256 |

<sup>1</sup> Als u de limiet van 800 implementaties per resource groep bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Het verwijderen van een item uit de implementatie geschiedenis heeft geen invloed op de geïmplementeerde resources. Zie voor meer informatie [oplossen fout bij aantal implementaties is groter dan 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

#### <a name="template-limits"></a>Sjabloon limieten

| Waarde | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Parameters |256 |256 |
| Variabelen |256 |256 |
| Bronnen (met inbegrip van het aantal kopieën) |800 |800 |
| Uitvoer |64 |64 |
| Expressie voor sjabloon |24.576 tekens |24.576 tekens |
| Resources in geëxporteerde sjablonen |200 |200 | 
| Sjabloon grootte |4 MB |4 MB |
| Parameter bestands grootte |64 kB |64 kB |

U kunt enkele sjabloon limieten overschrijden met behulp van een geneste sjabloon. Zie voor meer informatie [gekoppelde sjablonen gebruiken wanneer u Azure-resources implementeert](../articles/azure-resource-manager/templates/linked-templates.md). Als u het aantal para meters, variabelen of uitvoer wilt reduceren, kunt u verschillende waarden combi neren in een-object. Zie [objecten als para meters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)voor meer informatie.
