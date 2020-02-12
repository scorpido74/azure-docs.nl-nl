---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 02/10/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 32d39493e526a4b95369e2998d3f9ade9f2964dc
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133719"
---
| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| [Resource groepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |980 |
| Grootte van de Azure Resource Manager-API-aanvraag |4\.194.304 bytes. |4\.194.304 bytes. |
| Tags per abonnement<sup>1</sup> |Limited. |Limited. |
| Unieke label berekeningen per abonnement<sup>1</sup> | 10.000 | 10.000 |
| [Implementaties op abonnements niveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> | 800 |
| Abonnementen per Azure Active Directory Tenant | Limited. | Limited. |
| [Cobeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Limited. |Limited. |

<sup>1</sup> U kunt een onbeperkt aantal Tags per abonnement Toep assen. Het aantal Tags per resource of resource groep is beperkt tot 50. Resource Manager retourneert alleen een [lijst met unieke label namen en-waarden](/rest/api/resources/tags) in het abonnement wanneer het aantal Tags 10.000 of minder is. U kunt nog steeds een resource vinden op label wanneer het aantal groter is dan 10.000.  

<sup>2</sup> Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Als u implementaties op abonnements niveau wilt verwijderen, gebruikt u verwijderen [AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [AZ implementatie delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).
