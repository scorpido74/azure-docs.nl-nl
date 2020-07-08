---
title: Include-bestand
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334986"
---
| Resource | Limiet |
| --- | --- |
| Abonnementen per Azure Active Directory Tenant | Limited. |
| [Cobeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Limited. |
| [Resource groepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |
| Grootte van de Azure Resource Manager-API-aanvraag |4.194.304 bytes. |
| Tags per abonnement<sup>1</sup> |50 |
| Unieke label berekeningen per abonnement<sup>1</sup> | 10.000 |
| [Implementaties op abonnements niveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> |

<sup>1</sup> U kunt Maxi maal 50 Tags rechtstreeks op een abonnement Toep assen. Het abonnement kan echter een onbeperkt aantal labels bevatten die worden toegepast op resource groepen en resources binnen het abonnement. Het aantal Tags per resource of resource groep is beperkt tot 50. Resource Manager retourneert alleen een [lijst met unieke label namen en-waarden](/rest/api/resources/tags) in het abonnement wanneer het aantal Tags 10.000 of minder is. U kunt nog steeds een resource vinden op label wanneer het aantal groter is dan 10.000.  

<sup>2</sup> Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Als u implementaties op abonnements niveau wilt verwijderen, gebruikt u de [subsleutel](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete) [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of AZ-implementatie.
