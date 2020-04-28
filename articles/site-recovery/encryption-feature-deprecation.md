---
title: Afschaffing van Azure Site Recovery-functie voor gegevens versleuteling | Microsoft Docs
description: Details regarig Azure Site Recovery gegevens versleutelings functie
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/15/2019
ms.author: rajanaki
ms.openlocfilehash: 5e74466891a5926d8ae8feb3c1c48348ecf3cfe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74134994"
---
# <a name="deprecation-of-site-recovery-data-encryption-feature"></a>Afschaffing van Site Recovery-functie voor gegevens versleuteling

In dit document worden de details van de afschaffing en de herstel actie beschreven die u moet uitvoeren als u de functie voor gegevens versleuteling van Site Recovery gebruikt tijdens het configureren van herstel na nood geval voor virtuele Hyper-V-machines naar Azure. 

## <a name="deprecation-information"></a>Gegevens van afschaffing


De functie voor gegevens versleuteling van Site Recovery is beschikbaar voor klanten die virtuele Hyper-V-machines beveiligen om ervoor te zorgen dat de gerepliceerde gegevens worden beschermd tegen beveiligings Risico's. deze functie wordt op **30 December 2019**afgeschaft. Het wordt vervangen door de geavanceerde functie [versleuteling op rest](https://azure.microsoft.com/blog/azure-site-recovery-encryption-at-rest/) , die gebruikmaakt van [Storage service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) (SSE). Met SSE worden gegevens versleuteld voordat ze persistent worden gemaakt en ontsleuteld op het moment dat ze worden opgehaald. bij een failover naar Azure worden uw Vm's uitgevoerd vanaf de versleutelde opslag accounts, waardoor een verbeterde herstel tijd (RTO) mogelijk is.

Als u een bestaande klant bent die gebruikmaakt van deze functie, hebt u communicatie ontvangen met de details van de afschaffing en de herstels tappen. 


## <a name="what-are-the-implications"></a>Wat zijn de gevolgen?

Na **30 December 2019**kunnen vm's die nog steeds gebruikmaken van de buiten gebruik gestelde versleutelings functie, geen failover uitvoeren. 

## <a name="required-action"></a>Vereiste actie
Voer de volgende stappen uit om geslaagde failover-bewerkingen voort te zetten:

Volg deze stappen voor elke virtuele machine: 
1.  [Schakel replicatie uit](https://docs.microsoft.com/azure/site-recovery/site-recovery-manage-registration-and-protection#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).
2.  [Maak een nieuw replicatie beleid](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-tutorial#set-up-a-replication-policy).
3.  [Schakel replicatie in](https://docs.microsoft.com/azure/site-recovery/hyper-v-vmm-azure-tutorial#enable-replication) en selecteer een opslag account waarvoor SSE is ingeschakeld.

Na het volt ooien van de initiële replicatie naar opslag accounts waarvoor SSE is ingeschakeld, wordt voor uw virtuele machines versleuteling in rust gebruikt met Azure Site Recovery.


## <a name="next-steps"></a>Volgende stappen
Plan voor het uitvoeren van de herstels tappen en voer deze zo snel mogelijk uit. Als u query's hebt over deze afschaffing, neemt u contact op met Microsoft Ondersteuning. Zie [hier](hyper-v-vmm-architecture.md)voor meer informatie over Hyper-V naar Azure scenario.

