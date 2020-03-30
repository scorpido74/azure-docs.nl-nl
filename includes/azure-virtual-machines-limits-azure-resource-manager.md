---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 90dc5a067c05cadb3d5e102435b2e3d3de803e28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335028"
---
| Resource | Limiet |
| --- | --- |
| Virtuele machines [per abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per regio. |
| Totaal aantal VM-cores per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. Neem contact op met ondersteuning om de limiet te verhogen. |
| Azure Spot VM-totaalkernen per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. Neem contact op met ondersteuning om de limiet te verhogen. |
| VM per serie, zoals Dv2 en F, cores per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. Neem contact op met ondersteuning om de limiet te verhogen. |
| [Beschikbaarheidssets](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per abonnement |2.000 per regio. |
| Virtuele machines per beschikbaarheidsset | 200 |
| Certificaten per abonnement |Onbeperkt<sup>2</sup> |

<sup>1.</sup> Standaardlimieten variëren per type aanbiedingscategorie, zoals Gratis proefversie en Betalen per gebruik, en per serie, zoals Dv2, F en G. De standaardinstelling voor Enterprise Agreement-abonnementen is bijvoorbeeld 350.

<sup>2.</sup> Met Azure Resource Manager worden certificaten opgeslagen in de Azure Key Vault. Het aantal certificaten is onbeperkt voor een abonnement. Er is een limiet van 1 MB aan certificaten per implementatie, die bestaat uit één VM of een beschikbaarheidsset.

> [!NOTE]
> Virtuele machinekernen hebben een regionale totale limiet. Ze hebben ook een limiet voor regionale per-size series, zoals Dv2 en F. Deze limieten worden afzonderlijk gehandhaafd. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Dit abonnement kan 30 A1 VM's of 30 D1 VM's implementeren, of een combinatie van beide die in totaal 30 cores niet overschrijdt. Een voorbeeld van een combinatie is 10 A1 VM's en 20 D1 VM's.  
> <!-- -->
> 
