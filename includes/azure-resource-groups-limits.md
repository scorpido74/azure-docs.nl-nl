---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: 33a63280f6973d2c5e29db29f7a6f3fc68c57c77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424720"
---
| Resource | Limiet |
| --- | --- |
| Resources per [resource groep](../articles/azure-resource-manager/management/overview.md#resource-groups) | Resources worden niet beperkt door de resource groep. In plaats daarvan worden ze beperkt door het resource type in een resource groep. Zie de volgende rij. |
| Resources per resource groep, per resource type |800-sommige resource typen kunnen de 800-limiet overschrijden. Zie [resources niet beperkt tot 800 exemplaren per resource groep](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implementaties per resource groep in de implementatie geschiedenis |800<sup>1</sup> |
| Bronnen per implementatie |800 |
| Beheer vergrendelingen per uniek bereik |20 |
| Aantal Tags per resource of resource groep |50 |
| Sleutel lengte van tag |512 |
| Lengte van label waarde |256 |

<sup>1</sup> Vanaf juni 2020 worden implementaties automatisch verwijderd uit de geschiedenis, net als bij de limiet. Het verwijderen van een item uit de implementatie geschiedenis heeft geen invloed op de geïmplementeerde resources. Zie [automatische verwijderingen uit de implementatie geschiedenis](../articles/azure-resource-manager/templates/deployment-history-deletions.md)voor meer informatie.

#### <a name="template-limits"></a>Sjabloon limieten

| Waarde | Limiet |
| --- | --- |
| Parameters |256 |
| Variabelen |256 |
| Bronnen (met inbegrip van het aantal kopieën) |800 |
| Uitvoer |64 |
| Expressie voor sjabloon |24.576 tekens |
| Resources in geëxporteerde sjablonen |200 |
| Sjabloon grootte |4 MB |
| Parameter bestands grootte |64 kB |

U kunt enkele sjabloon limieten overschrijden met behulp van een geneste sjabloon. Zie voor meer informatie [gekoppelde sjablonen gebruiken wanneer u Azure-resources implementeert](../articles/azure-resource-manager/templates/linked-templates.md). Als u het aantal para meters, variabelen of uitvoer wilt reduceren, kunt u verschillende waarden combi neren in een-object. Zie [objecten als para meters](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)voor meer informatie.
