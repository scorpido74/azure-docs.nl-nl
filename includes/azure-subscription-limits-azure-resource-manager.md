---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334986"
---
| Resource | Limiet |
| --- | --- |
| Abonnementen per Azure Active Directory-tenant | Onbeperkt. |
| [Medebeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Onbeperkt. |
| [Resourcegroepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |
| Grootte van Azure Resource Manager API-aanvraag |4.194.304 bytes. |
| Tags per abonnement<sup>1</sup> |50 |
| Unieke tagberekeningen per abonnement<sup>1</sup> | 10.000 |
| [Implementaties op abonnementsniveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> |

<sup>1.</sup> U maximaal 50 tags rechtstreeks op een abonnement toepassen. Het abonnement kan echter een onbeperkt aantal tags bevatten die worden toegepast op resourcegroepen en resources binnen het abonnement. Het aantal tags per resource- of resourcegroep is beperkt tot 50. Resource Manager retourneert een [lijst met unieke tagnaam en waarden](/rest/api/resources/tags) in het abonnement alleen als het aantal tags 10.000 of minder is. U nog steeds een bron vinden op tag wanneer het aantal hoger is dan 10.000.  

<sup>2.</sup> Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties uit de geschiedenis die niet langer nodig zijn. Als u implementaties op abonnementsniveau wilt verwijderen, gebruikt u [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [sub delete az deployment](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
