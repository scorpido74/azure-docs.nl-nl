---
title: Failback van virtuele VMware-machines/fysieke servers van Azure met Azure Site Recovery
description: Meer informatie over het uitvoeren van een failback naar de on-premises site na een failover naar Azure, tijdens nood herstel van virtuele VMware-machines en fysieke servers naar Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084062"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>Failback van virtuele VMware-machines en fysieke servers van Azure naar een on-premises site

In dit artikel wordt beschreven hoe u failback van virtuele machines van Azure Virtual Machines naar een on-premises VMware-omgeving. Volg de instructies in dit artikel om een failback uit te voeren van uw virtuele VMware-machines of fysieke Windows/Linux-servers nadat er een failover van de on-premises site naar Azure is uitgevoerd met behulp van de [testfailover in azure site Recovery](site-recovery-failover.md) zelf studie.

## <a name="prerequisites"></a>Vereisten
- Zorg ervoor dat u de details van de [verschillende typen failback](concepts-types-of-failback.md) en bijbehorende voor behoud hebt gelezen.

> [!WARNING]
> U kunt geen failback uitvoeren nadat u de [migratie hebt voltooid](migrate-overview.md#what-do-we-mean-by-migration), een virtuele machine naar een andere resource groep hebt verplaatst of de virtuele machine van Azure hebt verwijderd. Als u de beveiliging van de virtuele machine uitschakelt, kunt u een failback uitvoeren.

> [!WARNING]
> Een fysieke server met Windows Server 2008 R2 SP1, indien beveiligd en failover naar Azure, kan niet worden hersteld.

> [!NOTE]
> Als u virtuele VMware-machines hebt mislukt, kunt u geen failback uitvoeren naar een Hyper-V-host.


- Voordat u doorgaat, voltooit u de stappen voor opnieuw beveiligen, zodat de virtuele machines een gerepliceerde status hebben en u een failover naar een on-premises site kunt starten. Zie [How to rebeveilig from Azure to on-premises](vmware-azure-reprotect.md)(Engelstalig) voor meer informatie.

- Zorg ervoor dat de vCenter een verbonden status heeft voordat u een failback uitvoert. Als dat niet het geval is, ontkoppelt u schijven en koppelt u deze opnieuw aan de virtuele machine.

- Tijdens een failover naar Azure is de on-premises site mogelijk niet toegankelijk en is de configuratie server mogelijk niet beschikbaar of afgesloten. Tijdens het opnieuw beveiligen en failback moet de on-premises configuratie server worden uitgevoerd en de status verbonden OK hebben. 

- Tijdens het failback moet de virtuele machine bestaan in de data base van de configuratie server of kan de failback niet worden uitgevoerd. Zorg ervoor dat u regel matig geplande back-ups van uw server onderneemt. Als er zich een nood situatie voordoet, moet u de server met het oorspronkelijke IP-adres herstellen om failback te kunnen gebruiken.

- De hoofddoel server mag geen moment opnamen hebben voordat de beveiliging/failback wordt geactiveerd.

## <a name="overview-of-failback"></a>Overzicht van failback
Nadat u een failover naar Azure hebt uitgevoerd, kunt u een failback uitvoeren naar uw on-premises site door de volgende stappen uit te voeren:

1. [Beveilig](vmware-azure-reprotect.md) de virtuele machines in azure opnieuw zodat ze repliceren naar virtuele VMware-machines in uw on-premises site. Als onderdeel van dit proces moet u ook het volgende doen:

    * Een on-premises hoofd doel instellen. Gebruik een Windows-hoofd doel voor virtuele Windows-machines en een [Linux-hoofd doel](vmware-azure-install-linux-master-target.md) voor virtuele Linux-machines.
    * Stel een [proces server](vmware-azure-set-up-process-server-azure.md)in.
    * Start opnieuw [beveiligen](vmware-azure-reprotect.md) om de on-premises virtuele machine uit te scha kelen en de gegevens van de virtuele Azure-machine te synchroniseren met de on-premises schijven.

2. Nadat uw virtuele machines in azure naar uw on-premises site zijn gerepliceerd, start u een failover van Azure naar de on-premises site.

3. Nadat de gegevens zijn hersteld, moet u de on-premises virtuele machines opnieuw beveiligen, zodat ze naar Azure gaan repliceren.

Bekijk de volgende video over het mislukken van een failback naar een on-premises site voor een beknopt overzicht:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Stappen om een failback uit te voeren

> [!IMPORTANT]
> Zorg ervoor dat u klaar bent met het opnieuw beveiligen van de virtuele machines voordat u begint met failback. De virtuele machines moeten een beveiligde status hebben en hun status moet **OK**zijn. Lees [hoe u](vmware-azure-reprotect.md)de virtuele machines opnieuw kunt beveiligen.

1. Selecteer de virtuele machine op de pagina gerepliceerde items. Klik er met de rechter muisknop op om een niet- **geplande failover**te selecteren.
2. Controleer bij **failover bevestigen**de richting van de failover (van Azure). Selecteer vervolgens het herstel punt (meest recente of de meest recente App-consistent) die u wilt gebruiken voor de failover. Het app-consistente punt bevindt zich achter het laatste moment en veroorzaakt gegevens verlies.
3. Tijdens de failover worden Site Recovery de virtuele machines in azure afgesloten. Nadat u hebt gecontroleerd of de failback naar verwachting is voltooid, kunt u controleren of de virtuele machines op Azure worden afgesloten.
4. **Door voeren** is vereist voor het verwijderen van de virtuele machine waarvoor een failover is uitgevoerd vanuit Azure. Klik met de rechter muisknop op het beveiligde item en selecteer **door voeren**. Met een taak worden de virtuele machines waarvoor failover is uitgevoerd in azure, verwijderd.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Op welk herstel punt kan ik een failback uitvoeren voor de virtuele machines?

Tijdens de failback hebt u twee opties voor het uitvoeren van een failback van de virtuele machine/het herstel plan.

- Als u het meest recente verwerkte punt in de tijd selecteert, wordt er een failover uitgevoerd voor alle virtuele machines naar het meest recente beschik bare tijdstip. Als er sprake is van een replicatie groep in het herstel plan, wordt de failover van elke virtuele machine van de replicatie groep naar het onafhankelijke laatste punt in de tijd uitgevoerd.

  U kunt geen failback uitvoeren voor een virtuele machine totdat deze ten minste één herstel punt heeft. U kunt geen failback uitvoeren voor een herstel plan totdat alle virtuele machines ten minste één herstel punt hebben.

  > [!NOTE]
  > Een laatste herstel punt is een crash-consistent herstel punt.

- Als u het toepassings consistente herstel punt selecteert, herstelt één virtuele-machine failback het meest recente beschik bare toepassings consistente herstel punt. In het geval van een herstel plan met een replicatie groep, herstelt elke replicatie groep het algemene beschik bare herstel punt.
Toepassings consistente herstel punten kunnen zich achter in de tijd bevinden en er kunnen gegevens verloren gaan.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Wat gebeurt er met VMware tools om failback te plaatsen?

In het geval van een virtuele Windows-machine wordt Site Recovery de VMware-hulpprogram ma's uitgeschakeld tijdens de failover. Tijdens de failback van de virtuele Windows-machine worden de VMware-hulpprogram ma's opnieuw ingeschakeld. 


## <a name="reprotect-from-on-premises-to-azure"></a>Opnieuw beveiligen van on-premises naar Azure
Nadat de failback is voltooid en u de door Voer hebt gestart, worden de herstelde virtuele machines in azure verwijderd. De virtuele machine bevindt zich nu weer op de on-premises site, maar deze is niet beveiligd. Ga als volgt te werk om te beginnen met het repliceren naar Azure:

1. Selecteer **kluis** > **instelling** > **gerepliceerde items**, selecteer de virtuele machines waarvoor een failback is ingesteld en selecteer vervolgens **opnieuw beveiligen**.
2. Voer de waarde in van de proces server die moet worden gebruikt om gegevens terug naar Azure te verzenden.
3. Selecteer **OK** om de taak opnieuw beveiligen te starten.

> [!NOTE]
> Nadat een on-premises virtuele machine is opgestart, kan het enige tijd duren voordat de agent zich opnieuw heeft geregistreerd bij de configuratie server (Maxi maal 15 minuten). Tijdens deze periode mislukt opnieuw beveiligen en wordt een fout bericht weer gegeven met de melding dat de agent niet is geïnstalleerd. Wacht een paar minuten en probeer opnieuw te beveiligen.

## <a name="next-steps"></a>Volgende stappen

Nadat de taak opnieuw beveiligen is voltooid, repliceert de virtuele machine terug naar Azure en kunt u een [failover](site-recovery-failover.md) uitvoeren om uw virtuele machines opnieuw naar Azure te verplaatsen.


