---
title: bestand opnemen
description: bestand opnemen
services: billing
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 07/22/2019
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: d94937a738034904413eac8b256121f14221d1ac
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845991"
---
| Bron | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Virtuele machines [per abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per regio. |25.000 per regio. |
| Totaal aantal VM-cores per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. | Neem contact op met ondersteuning. |
| Azure spot VM totale aantal kernen per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. | Neem contact op met ondersteuning. |
| VM per serie, zoals dv2 en F, kernen per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. | Neem contact op met ondersteuning. |
| [Opslag accounts](../articles/storage/common/storage-account-create.md) per regio per abonnement |250 |250 |
| [Beschikbaarheids sets](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per abonnement |2\.000 per regio. |2\.000 per regio. |
| [Affiniteitsgroepen](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) per abonnement |N.v.t.<sup>3</sup> |N.v.t.<sup>3</sup> |
| [Cloudservices](../articles/cloud-services/cloud-services-choose-me.md) per abonnement |N.v.t.<sup>3</sup> |N.v.t.<sup>3</sup> |
| [Resource groepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |980 |
| Grootte van de Azure Resource Manager-API-aanvraag |4\.194.304 bytes. |4\.194.304 bytes. |
| Tags per abonnement<sup>2</sup> |Limited. |Limited. |
| Unieke label berekeningen per abonnement<sup>2</sup> | 10.000 | 10.000 |
| [Implementaties op abonnements niveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>4</sup> | 800 |
| Abonnementen per Azure Active Directory Tenant | Limited. | Limited. |
| [Cobeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Limited. |Limited. |

<sup>1</sup> De standaard limieten variëren per aanbiedings categorie type, zoals gratis proef versie en betalen per gebruik en op serie, zoals dv2, F en G. De standaard instelling voor Enterprise Agreement abonnementen is 350.

<sup>2</sup> U kunt een onbeperkt aantal Tags per abonnement Toep assen. Het aantal Tags per resource of resource groep is beperkt tot 50. Resource Manager retourneert alleen een [lijst met unieke label namen en-waarden](/rest/api/resources/tags) in het abonnement wanneer het aantal Tags 10.000 of minder is. U kunt nog steeds een resource vinden op label wanneer het aantal groter is dan 10.000.  

<sup>3</sup> Deze functies zijn niet langer vereist voor Azure-resource groepen en Resource Manager.

<sup>4</sup> Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties uit de geschiedenis die niet meer nodig zijn. Als u implementaties op abonnements niveau wilt verwijderen, gebruikt u verwijderen [AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [AZ implementatie delete](/cli/azure/deployment?view=azure-cli-latest#az-deployment-delete).

> [!NOTE]
> De kern geheugens van de virtuele machine hebben een regionale limiet. Ze hebben ook een limiet voor regionale reeksen per grootte, zoals dv2 en F. Deze limieten worden afzonderlijk afgedwongen. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Dit abonnement kan 30 a1-vm's of 30 D1 Vm's implementeren, of een combi natie van de twee niet meer dan een totaal van 30 kernen overschrijdt. Een voor beeld van een combi natie is 10 a1-Vm's en 20 D1 Vm's.  
> <!-- -->
> 
> 

