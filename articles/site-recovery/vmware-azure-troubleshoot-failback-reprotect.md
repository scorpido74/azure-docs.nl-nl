---
title: Problemen met failback in VMware VM-nood herstel oplossen met Azure Site Recovery
description: In dit artikel worden manieren beschreven voor het oplossen van problemen met failback en opnieuw beveiligen tijdens de nood herstel van een VMware-VM naar Azure met Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b597ecb67ab30c8617029fe741af1014444a9b70
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693144"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Problemen met failback naar on-premises vanuit Azure oplossen

In dit artikel wordt beschreven hoe u problemen oplost die kunnen optreden wanneer u back-ups van virtuele Azure-machines naar uw on-premises VMware-infra structuur uitvoert, nadat u de failover naar Azure hebt uitgevoerd met behulp van [Azure site Recovery](site-recovery-overview.md).

Voor failback zijn in feite twee belang rijke stappen vereist. Voor de eerste stap moet u na een failover de Azure-Vm's opnieuw beveiligen naar on-premises zodat ze worden gerepliceerd. De tweede stap bestaat uit het uitvoeren van een failover vanuit Azure om een failback uit te voeren naar uw on-premises site.

## <a name="troubleshoot-reprotection-errors"></a>Fouten bij het opnieuw beveiligen oplossen

In deze sectie vindt u informatie over veelvoorkomende problemen met opnieuw beveiligen en hoe u deze kunt corrigeren.

### <a name="error-code-95226"></a>Fout code 95226

**Het opnieuw beveiligen is mislukt omdat de virtuele Azure-machine de on-premises configuratie server niet kan bereiken.**

Deze fout treedt op wanneer:

* De Azure-VM kan de on-premises configuratie server niet bereiken. De virtuele machine kan niet worden gedetecteerd en geregistreerd op de configuratie server.
* De inmage Scout-toepassings service wordt niet uitgevoerd op de virtuele Azure-machine na een failover. De service is vereist voor communicatie met de on-premises configuratie server.

Los dit probleem als volgt op:

* Controleer of het VM-netwerk van Azure toestaat dat de Azure VM communiceert met de on-premises configuratie server. U kunt een site-naar-site-VPN instellen voor uw on-premises Data Center of een Azure ExpressRoute-verbinding configureren met privé-peering op het virtuele netwerk van de Azure VM.
* Als de virtuele machine kan communiceren met de on-premises configuratie server, meldt u zich aan bij de virtuele machine. Controleer vervolgens de toepassings service inmage Scout. Als u ziet dat deze niet wordt uitgevoerd, start u de service hand matig. Controleer of het start type van de service is ingesteld op **automatisch**.

### <a name="error-code-78052"></a>Fout code 78052

**De beveiliging kan niet worden voltooid voor de virtuele machine.**

Dit probleem kan zich voordoen als er al een virtuele machine met dezelfde naam op de hoofddoel server is die u niet meer gebruikt.

Los dit probleem als volgt op:

* Selecteer een andere Master doel server op een andere host zodat de computer op een andere host wordt gemaakt, waarbij de namen niet conflicteren.
* U kunt vMotion ook gebruiken om het hoofd doel te verplaatsen naar een andere host waar de naam conflicten niet optreden. Als de bestaande VM een losse machine is, wijzigt u de naam zodat de nieuwe VM op dezelfde ESXi-host kan worden gemaakt.


### <a name="error-code-78093"></a>Fout code 78093

**De VM wordt niet uitgevoerd, is vastgelopen of is niet toegankelijk.**

Los dit probleem als volgt op:

Als u een failover-VM opnieuw wilt beveiligen, moet de virtuele machine van Azure worden uitgevoerd zodat de Mobility-service wordt geregistreerd bij de on-premises configuratie server en kan worden gerepliceerd door te communiceren met de proces server. Als de computer zich op een onjuist netwerk bevindt of niet wordt uitgevoerd (niet reageert of wordt afgesloten), kan de configuratie server de Mobility-service niet bereiken op de VM om de herbeveiliging te starten.

* Start de VM opnieuw op zodat deze on-premises kan communiceren.
* Start de taak opnieuw beveiligen opnieuw nadat u de virtuele machine van Azure hebt gestart.

### <a name="error-code-8061"></a>Fout code 8061

**De gegevens opslag is niet toegankelijk vanaf de ESXi-host.**

Controleer de [vereisten voor het hoofd doel en ondersteunde gegevens archieven](vmware-azure-reprotect.md#deploy-a-separate-master-target-server) voor failback.


## <a name="troubleshoot-failback-errors"></a>Failback-fouten oplossen

In deze sectie worden veelvoorkomende fouten beschreven die kunnen optreden tijdens het failback.

### <a name="error-code-8038"></a>Fout code 8038

**Het online zetten van de on-premises virtuele machine is mislukt vanwege de fout.**

Dit probleem treedt op wanneer de on-premises VM wordt geactiveerd op een host waarvoor onvoldoende geheugen is ingericht. 

Los dit probleem als volgt op:

* Richt meer geheugen in op de ESXi-host.
* Daarnaast kunt u met vMotion de virtuele machine verplaatsen naar een andere ESXi-host die voldoende geheugen heeft om de virtuele machine op te starten.
