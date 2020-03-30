---
title: VMware VM's/fysieke servers van Azure teruglaten mislukken met Azure-siteherstel
description: Lees hoe u na een failover naar Azure, tijdens het herstel van VMware VM's en fysieke servers naar Azure, niet meer naar de on-premises site ontbreken.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: cd4cc90fb102d517a47ba458619e22b8921dd498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495324"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>VMware VM's teruglaten mislukken naar on-premises site

In dit artikel wordt beschreven hoe u Azure VM's terugkunt naar een on-premises site, na [een fail-over](site-recovery-failover.md) van on-premises VM's naar Azure met [Azure Site Recovery](site-recovery-overview.md). Nadat u niet bent teruggegaan naar on-premises, schakelt u replicatie in, zodat de on-premises VM's beginnen te repliceren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

1. Meer informatie over [VMware-failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Zorg ervoor dat u de stappen hebt gecontroleerd en voltooid om u voor te [bereiden op failback](vmware-azure-prepare-failback.md)en dat alle vereiste onderdelen zijn geïmplementeerd. Onderdelen omvatten een processerver in Azure, een on-premises hoofddoelserver en een VPN-site-to-site-verbinding (of ExpressRoute private peering) voor failback.
3. Zorg ervoor dat u de [vereisten](vmware-azure-reprotect.md#before-you-begin) voor herbescherming en failback hebt voltooid en dat u opnieuw beveiliging van Azure VM's hebt [ingeschakeld,](vmware-azure-reprotect.md#enable-reprotection) zodat ze van Azure naar de on-premises site worden gerepliceerd. VM's moeten in een gerepliceerde staat zijn, is om terug te mislukken.




## <a name="run-a-failover-to-fail-back"></a>Een failover uitvoeren om terug te mislukken

1. Zorg ervoor dat Azure VM's opnieuw worden beveiligd en repliceren naar de on-premises site. 
    - Een VM heeft ten minste één herstelpunt nodig om terug te falen.
    - Als u een herstelplan niet terughaalt, moeten alle machines in het plan ten minste één herstelpunt hebben.
2. Selecteer de VM in de kluis > **gerepliceerde items.** Klik met de rechtermuisknop op de VM-> **Ongeplande failover**.
3. Controleer in **Failover bevestigen**de failoverrichting (vanuit Azure).
4. Selecteer het herstelpunt dat u wilt gebruiken voor de failover.
    - We raden u aan het laatste herstelpunt **te** gebruiken. Het app-consistente punt ligt achter het laatste punt in de tijd en veroorzaakt wat gegevensverlies.
    - **Laatste** is een crash-consistent herstelpunt.
    - Met **Laatste**, een VM niet over aan zijn recentste beschikbare punt in tijd. Als u een replicatiegroep hebt voor multi-VM-consistentie in een herstelplan, mislukt elke vm in de groep niet meer naar het onafhankelijke laatste tijdstip.
    - Als u een app-consistent herstelpunt gebruikt, wordt elke vm teruggezet naar het meest recente beschikbare punt. Als een herstelplan een replicatiegroep heeft, herstelt elke groep naar het gemeenschappelijke beschikbare herstelpunt.
5. Failover begint. Siteherstel schakelt de Azure VM's uit.
6. Nadat failover is voltooid, controleert u of alles werkt zoals verwacht. Controleer of de Azure VM's zijn uitgeschakeld. 
7. Met alles geverifieerd, klik met de rechtermuisknop op de VM > **Commit**, om het failoverproces te voltooien. Commit verwijdert de mislukte Azure VM. 

> [!NOTE]
> Voor Windows VM's schakelt Site Recovery de VMware-hulpprogramma's uit tijdens failover. Tijdens failback van de Windows VM zijn de VMware-hulpprogramma's opnieuw ingeschakeld. 




## <a name="reprotect-from-on-premises-to-azure"></a>Opnieuw beveiligen van on-premises naar Azure

Na het plegen van de failback worden de Azure VM's verwijderd. De VM is terug op de on-premises site, maar is niet beschermd. Ga als volgt te werk om vm's opnieuw te repliceren naar Azure:

1. Selecteer in de kluis > **gerepliceerde items**de optie mislukte VM's terug en selecteer **Vervolgens Opnieuw beveiligen**.
2. Geef de processerver op die wordt gebruikt om gegevens terug te sturen naar Azure.
3. Selecteer **OK** om de taak opnieuw beveiligen te starten.

> [!NOTE]
> Nadat een on-premises VM is gestart, duurt het maximaal 15 minuten voordat de agent zich opnieuw registreert bij de configuratieserver. Gedurende deze periode mislukt opnieuw beveiligen en retourneert een foutbericht waarin staat dat de agent niet is geïnstalleerd. Als dit gebeurt, wacht dan een paar minuten en bescherm opnieuw.

## <a name="next-steps"></a>Volgende stappen

Nadat de taak reprotect is voltooid, wordt de on-premises VM gerepliceerd naar Azure. Indien nodig u [een andere failover naar](site-recovery-failover.md) Azure uitvoeren.

