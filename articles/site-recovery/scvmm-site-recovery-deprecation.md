---
title: Toekomstige afschaffing van DR tussen sites van klanten die gebruikmaken van Hyper-V en tussen sites die worden beheerd door SCVMM naar Azure | Microsoft Docs
description: Details over toekomstige afschaffing van DR tussen sites die eigendom zijn van de klant met Hyper-V en tussen sites die worden beheerd door SCVMM naar Azure en alternatieve opties
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/13/2019
ms.author: rajanaki
ms.openlocfilehash: 972223815810917684f35c4e99f04e1ab5b882c8
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952119"
---
# <a name="upcoming-deprecation-of-dr-between-customer-owned-sites-using-hyper-v-and-between-sites-managed-by-scvmm-to-azure"></a>Toekomstige afschaffing van DR tussen sites van klanten die gebruikmaken van Hyper-V en tussen sites die worden beheerd door SCVMM naar Azure

In dit artikel worden de geplande afschaffing, de overeenkomstige implicaties en de alternatieve opties voor de klanten beschreven voor de volgende scenario's die door Azure Site Recovery worden ondersteund. 

- DR tussen virtuele Hyper-V-machines die worden beheerd door SCVMM tussen sites van klanten 
- DR. Hyper-V-Vm's die worden beheerd door SCVMM naar Azure

> [!IMPORTANT]
> Deze scenario's zijn momenteel gemarkeerd voor afschaffing en klanten worden verwacht de herstels tappen zo spoedig mogelijk te nemen om onderbrekingen van hun omgeving te voor komen. 
 

## <a name="what-changes-should-you-expect"></a>Welke wijzigingen moet u verwachten?

- Vanaf november 2019 is geen nieuwe gebruiker on-boards toegestaan voor deze scenario's. **Bestaande replicaties en beheer bewerkingen** , waaronder failover, testfailover, bewaking enz. **worden niet beïnvloed**.

- Zodra de scenario's zijn afgeschaft, zijn er de volgende implicaties, tenzij de klant de aanbevolen stappen volgt.

    - DR tussen de Hyper-V-Vm's die worden beheerd door SCVMM tussen sites van de klant: de replicaties blijven werken, omdat de onderliggende mogelijkheid van Hyper-V replica blijft werken, maar klanten kunnen geen aan de slag gerelateerde bewerkingen van het DR-beleid weer geven, beheren of uitvoeren via de Azure Sire-herstel ervaring in de Azure Portal. 
    - DR. virtuele Hyper-V-machines die worden beheerd door SCVMM naar Azure: de bestaande replicaties worden onderbroken en klanten kunnen niet-gerelateerde Azure-bewerkingen via de Azure Site Recovery weer geven, beheren of uitvoeren.


## <a name="recommended-actions-to-be-taken"></a>Aanbevolen acties die moeten worden uitgevoerd

Hieronder vindt u de alternatieve opties die de klant heeft, om ervoor te zorgen dat de DR-strategie niet wordt beïnvloed zodra het scenario is afgeschaft. 

- Kies ervoor om [Azure te gaan gebruiken als het Dr-doel voor vm's op Hyper-V-hosts](hyper-v-azure-tutorial.md).

> [!IMPORTANT]
> Houd er rekening mee dat uw on-premises omgeving nog steeds SCVMMM heeft, maar u kunt ASR alleen configureren met verwijzingen naar de Hyper-V-hosts.

- Kies ervoor om door te gaan met site-naar-site-replicatie, maar met de onderliggende [Hyper-V-replica oplossing](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica), maar u kunt geen Dr-configuraties beheren met Azure site Recovery in de Azure Portal. 


## <a name="next-steps"></a>Volgende stappen
Plan de afschaffing en kies een andere optie die het meest geschikt is voor uw infra structuur en uw bedrijf. Als er query's over zijn, kunt u contact opnemen met Microsoft Ondersteuning

