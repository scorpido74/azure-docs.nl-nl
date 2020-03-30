---
title: Failover/failback instellen op een secundaire Hyper-V-site met Azure Site Recovery
description: Lees hoe u falen op Hyper V VM's naar uw secundaire on-premises site en niet terug naar de primaire site, tijdens noodherstel met Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082588"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>Fail over and fail back Hyper-V VM's gerepliceerd naar uw secundaire on-premises site

De [Azure Site Recovery-service](site-recovery-overview.md) beheert en orkestreert replicatie, failover en failback van on-premises machines en Virtuele Azure-machines (VM's).

In dit artikel wordt beschreven hoe u falen over een Hyper-V VM die wordt beheerd in een VMM-cloud (Virtual Machine Manager) van het Systeemcentrum, naar een secundaire VMM-site. Nadat u een failover hebt uitgevoerd, valt u terug op uw on-premises site wanneer deze beschikbaar is. In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Fail over een Hyper-V VM van een primaire VMM-cloud naar een secundaire VMM-cloud
> * Opnieuw beveiligen van de secundaire site naar de primaire site en terugfalen
> * Optioneel opnieuw beginnen met repliceren van primair naar secundair

## <a name="failover-and-failback"></a>Failover en failback

Failover en failback heeft drie fasen:

1. **Fail over naar secundaire site:** Fail machines over van de primaire site naar de secundaire.
2. **Mislukt terug van de secundaire site:** Repliceren VM's van secundair naar primair, en voer een geplande failover terug te mislukken.
3. Na de geplande failover u optioneel opnieuw van de primaire site naar de secundaire site repliceren.


## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u een [noodhersteloefening](hyper-v-vmm-test-failover.md) hebt voltooid om te controleren of alles werkt zoals verwacht.
- Als u failback wilt voltooien, moet u ervoor zorgen dat de primaire en secundaire VMM-servers zijn verbonden met Site Recovery.



## <a name="run-a-failover-from-primary-to-secondary"></a>Een failover uitvoeren van primair naar secundair

U een regelmatige of geplande failover uitvoeren voor Hyper-V VM's.

- Gebruik een regelmatige failover voor onverwachte storingen. Wanneer u deze failover uitvoert, maakt Site Recovery een VM op de secundaire site en wordt deze up-up uitgevoerd. Gegevensverlies kan optreden, afhankelijk van gegevens in behandeling die niet zijn gesynchroniseerd.
- Een geplande failover kan worden gebruikt voor onderhoud of tijdens verwachte uitval. Deze optie biedt nul gegevensverlies. Wanneer een geplande failover wordt geactiveerd, worden de bron-VM's uitgeschakeld. Niet-gesynchroniseerde gegevens worden gesynchroniseerd en de failover wordt geactiveerd. 
- 
  In deze procedure wordt beschreven hoe u een regelmatige failover uitvoert.


1. Klik **in gerepliceerde items op** > **Replicated items** de vm->-failover . **Failover**
1. Selecteer De machine afsluiten voordat u **failover begint** als u wilt dat Siteherstel probeert de bron-VM's uit te schakelen voordat de failover wordt geactiveerd. Siteherstel probeert ook on-premises gegevens te synchroniseren die nog niet naar de secundaire site zijn verzonden, voordat de failover wordt geactiveerd. Houd er rekening mee dat failover blijft, zelfs als shutdown mislukt. U de failovervoortgang volgen op de pagina **Vacatures.**
2. U moet nu in staat zijn om de VM te zien in de secundaire VMM-cloud.
3. Nadat u de VM hebt geverifieerd, **verbindt u** de failover. Hiermee verwijdert u alle beschikbare herstelpunten.

> [!WARNING]
> **Annuleer een failover die in voortgang is niet**: voordat de failover wordt gestart, wordt de VM-replicatie gestopt. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.  


## <a name="reverse-replicate-and-failover"></a>Reverse repliceren en failover

Begin met repliceren van de secundaire site naar de primaire site en ga niet terug naar de primaire site. Nadat VM's weer op de primaire site worden uitgevoerd, u deze repliceren naar de secundaire site.  

 
1. Klik op de VM > klik op **Repliceren omkeren**.
2. Zodra de taak is voltooid, klikt u op de VM->In **Failover,** controleert u de failoverrichting (vanuit de secundaire VMM-cloud) en selecteert u de bron- en doellocaties. 
4. Start de failover. U kunt de voortgang van de failover volgen op het tabblad **Taken**.
5. Controleer in de primaire VMM-cloud of de VM beschikbaar is.
6. Als u de primaire vm opnieuw wilt repliceren naar de secundaire site, klikt u op **Repliceren.**

## <a name="next-steps"></a>Volgende stappen
[Bekijk de stap](hyper-v-vmm-disaster-recovery.md) voor het repliceren van Hyper-V VM's naar een secundaire site.
