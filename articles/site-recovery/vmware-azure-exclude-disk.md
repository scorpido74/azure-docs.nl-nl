---
title: VMware VM-schijven uitsluiten van disaster recovery naar Azure met Azure Site Recovery
description: VMware VM-schijven uitsluiten van replicatie naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.date: 12/10/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: cd54da5ee01206e576157435135065189bfb8035
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495365"
---
# <a name="exclude-disks-from-vmware-vm-replication-to-azure"></a>Schijven uitsluiten van Vm-replicatie van VMware naar Azure

In dit artikel wordt beschreven hoe u schijven uitsluiten bij het repliceren van VMware VM's naar Azure voor herstel na noodgevallen. U schijven om een aantal redenen uitsluiten van replicatie:

- Zorg ervoor dat onbelangrijke gegevens die op de uitgesloten schijf worden gechurn, niet worden gerepliceerd.
- Optimaliseer de verbruikte replicatiebandbreedte of de bronnen aan de doelzijde door schijven uit te sluiten die u niet hoeft te repliceren.
- Sla opslag- en netwerkbronnen op door gegevens die u niet nodig hebt, niet te repliceren.

Voordat u schijven uitsluit van replicatie:

- [Meer informatie](exclude-disks-replication.md) over het uitsluiten van schijven.
- Bekijk [typische uitsluitingsscenario's](exclude-disks-replication.md#typical-scenarios) en [voorbeelden](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) die laten zien hoe het uitsluiten van een schijf van invloed is op replicatie, failover en failback.

## <a name="before-you-start"></a>Voordat u begint

 Let op het volgende voordat u begint:

- **Replicatie:** Standaard worden alle schijven op een machine gerepliceerd.
- **Schijftype:** Alleen basisschijven kunnen worden uitgesloten van replicatie. Besturingssystemen en dynamische schijven kunt u niet uitsluiten.
- **Mobiliteitsservice:** Als u een schijf wilt uitsluiten van replicatie, moet u de Mobiliteitsservice handmatig op de machine installeren voordat u replicatie inschakelt. U de push-installatie niet gebruiken, omdat deze methode de Mobiliteitsservice pas op een VM installeert nadat replicatie is ingeschakeld.  
- **Schijven toevoegen/verwijderen/uitsluiten:** Nadat u replicatie hebt ingeschakeld, u geen schijven toevoegen/verwijderen/uitsluiten voor replicatie. Als u schijven wilt toevoegen/verwijderen of uitsluiten, moet u de beveiliging van de machine uitschakelen en deze vervolgens opnieuw inschakelen.
- **Failover:** Na failover, als mislukt over apps moeten uitgesloten schijven om te werken, moet u deze schijven handmatig maken. U Azure-automatisering ook integreren in een herstelplan om de schijf te maken tijdens het mislukken van de machine.
- **Failback-Windows:** Wanneer u na failover niet meer op uw on-premises site terechtkomt, worden Windows-schijven die u handmatig in Azure maakt, niet teruggezet. Als u bijvoorbeeld meer dan drie schijven niet hebt en twee schijven rechtstreeks in Azure VM's maakt, worden alleen de drie schijven die zijn mislukt, weer mislukt.
- **Failback-Linux:** Voor failback van Linux-machines zijn schijven die u handmatig in Azure maakt, weer mislukt. Als u bijvoorbeeld meer dan drie schijven niet hebt en twee schijven rechtstreeks op Azure VM's maakt, worden alle vijf weer mislukt. U schijven die handmatig zijn gemaakt in de failback of in herbescherming van VM's niet uitsluiten.



## <a name="exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

1. Wanneer u [replicatie inschakelt](site-recovery-hyper-v-site-to-azure.md) voor een Vm vmware, controleert u, nadat u de VM's hebt geselecteerd die u wilt repliceren, in de pagina Eigenschappen van**replicatie-eigenschappen** >  **Enable replication** > configureren**inschakelen,** de kolom Schijven naar **repliceren.** Standaard zijn alle schijven geselecteerd voor replicatie.
2. Als u een specifieke schijf niet wilt repliceren, u in schijven de selectie voor schijven **wissen** die u wilt uitsluiten. 

    ![Schijven uitsluiten van replicatie](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)



## <a name="next-steps"></a>Volgende stappen
Wanneer uw implementatie actief is, kunt u [hier](failover-failback-overview.md) meer lezen over de verschillende soorten failovers.
