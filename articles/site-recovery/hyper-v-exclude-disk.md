---
title: Hyper-V VM-schijven uitsluiten van noodherstel naar Azure met Azure Site Recovery
description: Hyper-V VM-schijven uitsluiten van replicatie naar Azure met Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 50fb6da2905b2ae27547f25cce3d7a76ca7976b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498131"
---
# <a name="exclude-disks-from-replication"></a>Schijven uitsluiten van replicatie

In dit artikel wordt beschreven hoe u schijven uitsluiten bij het repliceren van Hyper-V VM's naar Azure. U schijven om een aantal redenen uitsluiten van replicatie:

- Zorg ervoor dat onbelangrijke gegevens die op de uitgesloten schijf worden gechurn, niet worden gerepliceerd.
- Optimaliseer de verbruikte replicatiebandbreedte of de bronnen aan de doelzijde door schijven uit te sluiten die u niet hoeft te repliceren.
- Sla opslag- en netwerkbronnen op door gegevens die u niet nodig hebt, niet te repliceren.

Voordat u schijven uitsluit van replicatie:

- [Meer informatie](exclude-disks-replication.md) over het uitsluiten van schijven.
- Bekijk [typische uitsluitingsscenario's](exclude-disks-replication.md#typical-scenarios) en [voorbeelden](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) die laten zien hoe het uitsluiten van een schijf van invloed is op replicatie, failover en failback.

## <a name="before-you-start"></a>Voordat u begint

Let op het volgende voordat u begint:

- **Replicatie:** standaard worden alle schijven op een machine gerepliceerd.
- **Schijftype**:
    - U basisschijven uitsluiten van replicatie.
    - Besturingssysteemschijven kunt u niet uitsluiten.
    - We raden u aan geen dynamische schijven uit te sluiten. Site Recovery kan niet identificeren welke VHD basic of dynamisch is in de gast-VM.  Als u niet alle afhankelijke dynamische volumeschijven uitsluit, wordt de beveiligde dynamische schijf een mislukte schijf op een mislukte via VM en zijn de gegevens op die schijf niet toegankelijk.
- **Schijven toevoegen/verwijderen/uitsluiten:** Nadat u replicatie hebt ingeschakeld, u geen schijven toevoegen/verwijderen/uitsluiten voor replicatie. Als u een schijf wilt toevoegen/verwijderen of uitsluiten, moet u de beveiliging van de virtuele machine uitschakelen en deze vervolgens opnieuw inschakelen.
- **Failover:** Na failover, als mislukt over apps moeten sluiten schijven om te werken, moet u deze schijven handmatig maken. U Azure-automatisering ook integreren in een herstelplan om de schijf te maken tijdens het mislukken van de machine.
- **Failback:** Wanneer u na failover niet meer naar uw on-premises site gaat, worden schijven die u handmatig in Azure hebt gemaakt, niet teruggezet. Als u bijvoorbeeld meer dan drie schijven niet hebt en twee schijven rechtstreeks op een Azure-vm maakt, worden slechts drie schijven die zijn mislukt, vervolgens weer mislukt. U geen schijven opnemen die handmatig zijn gemaakt in failback of in omgekeerde replicatie van VM's.

## <a name="exclude-disks"></a>Schijven uitsluiten

1. Als u schijven wilt uitsluiten wanneer u [replicatie inschakelt](site-recovery-hyper-v-site-to-azure.md) voor een Hyper-V-vm, controleert u na het selecteren van de VM's die u wilt repliceren, in de pagina Eigenschappen van**replicatie-eigenschappen** >  **Enable replication** > configureren**configureren,** de kolom Schijven naar **repliceren.** Standaard zijn alle schijven geselecteerd voor replicatie.
2. Als u een specifieke schijf niet wilt repliceren, u in schijven de selectie voor schijven **wissen** die u wilt uitsluiten. 

    ![Schijven uitsluiten van replicatie](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>Volgende stappen
Wanneer uw implementatie actief is, kunt u [hier](failover-failback-overview.md) meer lezen over de verschillende soorten failovers.
