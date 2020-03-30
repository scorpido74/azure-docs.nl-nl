---
title: Afschrijving van azure site recovery-gegevensversleutelingsfunctie | Microsoft Documenten
description: Details regarig Azure Site Recovery data encryptie functie
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74134994"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Afschrijving van de versleutelingsfunctie siteherstelgegevens

In dit document worden de afschrijvingsgegevens en de herstelactie beschreven die u moet uitvoeren als u de functie siteherstelgegevensversleuteling gebruikt terwijl u het herstel van hyper-v-virtuele machines naar Azure configureert. 

## <a name="deprecation-information"></a>Afschaffingsinformatie


De siteherstelgegevensversleutelingsfunctie was beschikbaar voor klanten die Hyper-V vms beschermen om ervoor te zorgen dat de gerepliceerde gegevens werden beschermd tegen beveiligingsbedreigingen. deze functie wordt voor **30 december 2019**afgeschaft. Het wordt vervangen door de meer geavanceerde [Encryption at Rest-functie,](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) die gebruik maakt van Storage Service [Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). Met SSE worden gegevens versleuteld voordat ze blijven opslaan en gedecodeerd bij het ophalen, en bij failover naar Azure worden uw VM's uitgevoerd vanaf de versleutelde opslagaccounts, waardoor een verbeterde hersteltijddoelstelling (RTO) mogelijk is.

Houd er rekening mee dat als u een bestaande klant bent die deze functie gebruikt, u communicatie zou hebben ontvangen met de afschrijvingsgegevens en herstelstappen. 


## <a name="what-are-the-implications"></a>Wat zijn de implicaties?

Na **30 december 2019**mogen vm's die nog steeds gebruik maken van de gepensioneerde versleutelingsfunctie, geen failover uitvoeren. 

## <a name="required-action"></a>Vereiste actie
Als u de succesvolle failoverbewerkingen wilt voortzetten en replicaties de onderstaande stappen volgen:

Volg de volgende stappen voor elke virtuele machine: 
1.  [Replicatie uitschakelen](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Een nieuw replicatiebeleid maken](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Schakel replicatie in](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) en selecteer een opslagaccount waarmee SSE is ingeschakeld.

Nadat de eerste replicatie naar opslagaccounts is voltooid, met SSE-ingeschakeld, gebruiken uw VM's Versleuteling in rust met Azure Site Recovery.


## <a name="next-steps"></a>Volgende stappen
Plan voor het uitvoeren van de herstelstappen, en deze op zijn vroegst uit te voeren. Als u vragen heeft over deze afschrijving, neem dan contact op met Microsoft Support. Voor meer informatie over Hyper-V naar Azure-scenario verwijzen we [hier](hyper-v-vmm-architecture.md)naar .

