---
title: Include-bestand
description: Include-bestand
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335112"
---
| Resource | Limiet |
| --- | --- |
| Beheer groepen per Directory | 10.000 |
| Abonnementen per beheer groep | Limited. |
| Niveaus van beheer groeps hiërarchie | Hoofd niveau plus 6 niveaus<sup>1</sup> |
| Directe bovenliggende beheer groep per beheer groep | Eén |
| [Implementaties op beheer groepniveau](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per locatie | 800<sup>2</sup> |

<sup>1</sup> De 6 niveaus bevatten niet het abonnements niveau.

<sup>2</sup> Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Als u implementaties op beheer groeps niveau wilt verwijderen, gebruikt u [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) of [AZ Deployment mg delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
