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
ms.openlocfilehash: 49c0a2a37fa72cd8c4750b39ff422a90721e3940
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233985"
---
| Resource | Limiet |
| --- | --- |
| Abonnementen per Azure Active Directory-tenant | Onbeperkt |
| [Medebeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Onbeperkt |
| [Resourcegroepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |
| Aanvraaggrootte voor Azure Resource Manager API |4.194.304 bytes |
| Tags per abonnement<sup>1</sup> |50 |
| Unieke tagberekeningen per abonnement<sup>1</sup> | 10.000 |
| [Implementaties van abonnementsniveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> |

<sup>1</sup>U kunt maximaal 50 tags rechtstreeks op een abonnement toepassen. Het abonnement kan echter een onbeperkt aantal tags bevatten die worden toegepast op resourcegroepen en resources binnen het abonnement. Het aantal tags per resource of resourcegroep is beperkt tot 50. Resource Manager retourneert alleen een [lijst met unieke tagnamen en -waarden](/rest/api/resources/tags) in het abonnement wanneer het aantal tags 10.000 of minder is. U kunt echter nog steeds resources zoeken aan de hand van tags wanneer er meer dan 10.000 tags zijn.  

<sup>2</sup>Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties die niet meer nodig zijn uit de geschiedenis. Als u implementaties op abonnementsniveau wilt verwijderen, gebruikt u [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
