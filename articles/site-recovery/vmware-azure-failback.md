---
title: Failback van virtuele VMware-machines/fysieke servers van Azure met Azure Site Recovery
description: Meer informatie over het uitvoeren van een failback naar de on-premises site na een failover naar Azure, tijdens nood herstel van virtuele VMware-machines en fysieke servers naar Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84485321"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Failback van virtuele VMware-machines naar een on-premises site

In dit artikel wordt beschreven hoe u back-ups van virtuele Azure-machines naar een on-premises site kunt herstellen, na het uitvoeren van een [failover](site-recovery-failover.md) van on-premises Vm's naar Azure met [Azure site Recovery](site-recovery-overview.md). Na failback naar on-premises schakelt u replicatie in zodat de on-premises Vm's beginnen met repliceren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

1. Meer informatie over [VMware-failback](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Zorg ervoor dat u de stappen voor het [voorbereiden van de failback](vmware-azure-prepare-failback.md)hebt gecontroleerd en voltooid en dat alle vereiste onderdelen worden geïmplementeerd. Onderdelen bevatten een proces server in azure, een on-premises hoofddoel server en een VPN-site-naar-site-verbinding (of ExpressRoute privé-peering) voor failback.
3. Zorg ervoor dat u de [vereisten](vmware-azure-reprotect.md#before-you-begin) voor opnieuw beveiligen en failback hebt voltooid en dat u het opnieuw beveiligen van virtuele Azure-machines hebt [ingeschakeld](vmware-azure-reprotect.md#enable-reprotection) , zodat deze vanuit Azure worden gerepliceerd naar de on-premises site. Vm's moeten zich in een gerepliceerde status bevindt, een failback-bewerking moet worden uitgevoerd.




## <a name="run-a-failover-to-fail-back"></a>Een failover uitvoeren om een failback uit te voeren

1. Zorg ervoor dat de virtuele machines van Azure opnieuw worden beveiligd en gerepliceerd naar de on-premises site.
    - Een virtuele machine moet ten minste één herstel punt hebben om failback te kunnen uitvoeren.
    - Als u een herstel plan failback terugzet, moeten alle computers in het plan ten minste één herstel punt hebben.
2. Selecteer de virtuele machine in de kluis > **gerepliceerde items**. Klik met de rechter muisknop op de virtuele machine > niet- **geplande failover**.
3. Controleer bij **failover bevestigen**de richting van de failover (van Azure).
4. Selecteer het herstelpunt dat u wilt gebruiken voor de failover.
    - U wordt aangeraden het **meest recente** herstel punt te gebruiken. Het app-consistente punt bevindt zich achter het laatste moment en veroorzaakt gegevens verlies.
    - **Meest recente** is een crash-consistent herstel punt.
    - Met de **nieuwste**virtuele machine wordt een failover uitgevoerd naar het meest recente beschik bare tijdstip. Als u een replicatie groep hebt voor de consistentie tussen meerdere VM'S binnen een herstel plan, wordt voor elke virtuele machine in de groep een failover uitgevoerd naar het onafhankelijke laatste tijdstip.
    - Als u een app-consistent herstel punt gebruikt, mislukt elke VM het meest recente beschik bare punt. Als een herstel plan een replicatie groep heeft, herstelt elke groep het algemene beschik bare herstel punt.
5. De failover wordt gestart. Site Recovery worden de Azure-Vm's afgesloten.
6. Wanneer de failover is voltooid, controleert u of alles werkt zoals verwacht. Controleer of de virtuele machines van Azure zijn afgesloten. 
7. Met alles wat is geverifieerd, klikt u met de rechter muisknop op de virtuele machine > **door voeren**om de failover te volt ooien. Door voeren verwijdert de failover van Azure VM. 

> [!NOTE]
> Voor virtuele Windows-machines Site Recovery de VMware-hulpprogram ma's uitgeschakeld tijdens de failover. Tijdens de failback van de Windows-VM zijn de VMware-hulpprogram ma's weer ingeschakeld. 




## <a name="reprotect-from-on-premises-to-azure"></a>Opnieuw beveiligen van on-premises naar Azure

Nadat de failback is doorgevoerd, worden de virtuele Azure-machines verwijderd. De virtuele machine bevindt zich weer op de on-premises site, maar is niet beveiligd. Als volgt om het repliceren van Vm's naar Azure opnieuw te starten:

1. Selecteer in de kluis > **gerepliceerde items**de optie fail back vm's en selecteer vervolgens **opnieuw beveiligen**.
2. Geef de proces server op die wordt gebruikt voor het terugsturen van gegevens naar Azure.
3. Selecteer **OK** om de taak opnieuw beveiligen te starten.

> [!NOTE]
> Nadat een on-premises VM is gestart, duurt het Maxi maal 15 minuten voordat de agent zich opnieuw registreerde op de configuratie server. Tijdens deze periode mislukt opnieuw beveiligen en wordt een fout bericht weer gegeven met de melding dat de agent niet is geïnstalleerd. Als dit het geval is, wacht u een paar minuten en verveiligt u het opnieuw.

## <a name="next-steps"></a>Volgende stappen

Nadat de taak opnieuw beveiligen is voltooid, wordt de on-premises VM gerepliceerd naar Azure. Als dat nodig is, kunt u [nog een failover uitvoeren](site-recovery-failover.md) naar Azure.

