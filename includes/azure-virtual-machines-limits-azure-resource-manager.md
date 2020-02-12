---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 8607fd6f01ea02841017d09dac7236812f2330b9
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133700"
---
| Resource | Standaardlimiet |
| --- | --- |
| Virtuele machines [per abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |25.000<sup>1</sup> per regio. |
| Totaal aantal VM-cores per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| Azure spot VM totale aantal kernen per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| VM per serie, zoals dv2 en F, kernen per [abonnement](../articles/billing-buy-sign-up-azure-subscription.md) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| [Beschikbaarheids sets](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per abonnement |2\.000 per regio. |
| Virtuele machines per beschikbaarheidsset | 200 |
| Certificaten per abonnement |Onbeperkt<sup>2</sup> |

<sup>1</sup> De standaard limieten variëren per aanbiedings categorie type, zoals gratis proef versie en betalen per gebruik en op serie, zoals dv2, F en G. De standaard instelling voor Enterprise Agreement abonnementen is 350.

<sup>2</sup> Met Azure Resource Manager worden certificaten opgeslagen in de Azure Key Vault. Het aantal certificaten is onbeperkt voor een abonnement. Er is een limiet van 1 MB voor certificaten per implementatie, die bestaat uit één virtuele machine of een beschikbaarheidsset.

> [!NOTE]
> De kern geheugens van de virtuele machine hebben een regionale limiet. Ze hebben ook een limiet voor regionale reeksen per grootte, zoals dv2 en F. Deze limieten worden afzonderlijk afgedwongen. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Dit abonnement kan 30 a1-vm's of 30 D1 Vm's implementeren, of een combi natie van de twee niet meer dan een totaal van 30 kernen overschrijdt. Een voor beeld van een combi natie is 10 a1-Vm's en 20 D1 Vm's.  
> <!-- -->
> 
