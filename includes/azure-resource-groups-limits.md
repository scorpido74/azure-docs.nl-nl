---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334948"
---
| Resource | Limiet |
| --- | --- |
| Resources per [resourcegroep](../articles/azure-resource-manager/management/overview.md#resource-groups) | Resources worden niet beperkt door resourcegroep. In plaats daarvan worden ze beperkt door resourcetype in een resourcegroep. Zie volgende rij. |
| Resources per resourcegroep, per resourcetype |800 - Sommige resourcetypen kunnen de 800-limiet overschrijden. Zie [Resources die niet zijn beperkt tot 800 exemplaren per resourcegroep](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Implementaties per resourcegroep in de implementatiegeschiedenis |800<sup>1</sup> |
| Resources per implementatie |800 |
| Beheervergrendelingen per unieke scope |20 |
| Aantal tags per resource of resourcegroep |50 |
| Sleutellengte tagt |512 |
| Lengte tagwaarde |256 |

<sup>1.</sup> Als u de limiet van 800 implementaties per resourcegroep bereikt, verwijdert u implementaties uit de geschiedenis die niet langer nodig zijn. Het verwijderen van een item uit de implementatiegeschiedenis heeft geen invloed op de geïmplementeerde resources. Zie Fout oplossen wanneer het [aantal implementaties hoger is dan 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md)voor meer informatie.

#### <a name="template-limits"></a>Sjabloonlimieten

| Waarde | Limiet |
| --- | --- |
| Parameters |256 |
| Variabelen |256 |
| Bronnen (inclusief aantal exemplaren) |800 |
| Uitvoer |64 |
| Sjabloonexpressie |24.576 chars |
| Bronnen in geëxporteerde sjablonen |200 |
| Sjabloongrootte |4 MB |
| Parameterbestandsgrootte |64 kB |

U bepaalde sjabloonlimieten overschrijden met een geneste sjabloon. Zie [Gekoppelde sjablonen gebruiken wanneer u Azure-resources implementeert](../articles/azure-resource-manager/templates/linked-templates.md)voor meer informatie. Als u het aantal parameters, variabelen of uitvoer wilt verminderen, u verschillende waarden in een object combineren. Zie [Objecten als parameters voor](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md)meer informatie .
