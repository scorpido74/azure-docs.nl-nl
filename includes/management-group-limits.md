---
title: bestand opnemen
description: bestand opnemen
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335112"
---
| Resource | Limiet |
| --- | --- |
| Beheergroepen per map | 10.000 |
| Abonnementen per beheergroep | Onbeperkt. |
| Niveaus van de hiërarchie van de managementgroep | Wortelniveau plus 6 niveaus<sup>1</sup> |
| Directe bovenliggende beheergroep per managementgroep | Eén |
| [Implementaties op managementgroepniveau](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per locatie | 800<sup>2</sup> |

<sup>1.</sup> De 6 niveaus omvatten niet het abonnementsniveau.

<sup>2.</sup> Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties uit de geschiedenis die niet langer nodig zijn. Als u implementaties op managementgroepniveau wilt verwijderen, gebruikt u [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) of [az deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
