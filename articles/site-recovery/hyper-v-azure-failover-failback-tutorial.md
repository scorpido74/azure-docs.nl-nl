---
title: Failover en failback instellen voor virtuele Hyper-V-machines in Azure Site Recovery
description: Leer hoe u failover- en failback-overschakeling kunt uitvoeren van Hyper-V-VM's naar Azure als onderdeel van herstel na een noodgeval met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8c197c2f0875bb31d091fb5839730ee1568b471
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082655"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Failover en failback voor virtuele Hyper-V-machines die zijn gerepliceerd naar Azure

Deze zelfstudie beschrijft het uitvoeren van een failover van een virtuele Hyper-V-machine naar Azure. Nadat u een failover hebt uitgevoerd, valt u terug op uw on-premises site wanneer deze beschikbaar is. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleren of de eigenschappen van de virtuele Hyper-V-machine in overeenstemming zijn met de Azure-vereisten
> * Een failover naar Azure uitvoeren
> * Failback van Azure naar on-premises
> * Replicatie van on-premises virtuele machines omkeren om weer te repliceren naar Azure

Dit is de vijfde zelfstudie in een reeks. In deze zelfstudie wordt ervan uitgegaan dat u de taken in de vorige zelfstudies al hebt voltooid.    

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](tutorial-prepare-on-premises-hyper-v.md)
3. Noodherstel configureren voor [virtuele Hyper-V-machines](tutorial-hyper-v-to-azure.md) of voor [virtuele Hyper-V-machines die worden beheerd in System Center VMM-clouds](tutorial-hyper-v-vmm-to-azure.md)
4. [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Voorbereiden voor failover en failback

Zorg ervoor dat er zich geen momentopnamen bevinden op de virtuele machine en dat de on-premises virtuele machine is uitgeschakeld tijdens failback. Dit helpt ervoor te zorgen dat gegevens tijdens de replicatie consistent blijven. Schakel de on-premises virtuele machine niet in tijdens failback. 

Failover en failback bestaat uit drie fasen:

1. **Failover naar Azure**: failover virtuele Hyper-V-machines van de on-premises site naar Azure.
2. **Failback naar on-premises**: failover virtuele Azure-machines naar uw on-premises-site als de on-premises site beschikbaar is. Gegevens worden gesynchroniseerd van Azure naar on-premises en als dat is afgerond, worden de virtuele machines on-premises online gezet.  
3. **Replicatie omkeren op on-premises machines**: nadat failback naar on-premises is voltooid, keert u de replicatie van de on-premises virtuele machines om, zodat deze weer worden gerepliceerd naar Azure.

## <a name="verify-vm-properties"></a>VM-eigenschappen verifiëren

Controleer voorafgaand aan failover de eigenschappen van de virtuele machine om er zeker van te zijn dat de machine voldoet aan de [Azure-vereisten](hyper-v-azure-support-matrix.md#replicated-vms).

Klik in **Beveiligde items** op **Gerepliceerde items** > VM.

1. In het deelvenster **Gerepliceerd item** bevindt zich een overzicht van VM-informatie, status, en de laatste beschikbare herstelpunten. Klik op **Eigenschappen** om meer details te bekijken.

1. In **Berekening en netwerk** kunt u de Azure-naam, de resourcegroep, de doelgrootte, [beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md) en instellingen van de beheerde schijf wijzigen

1. U kunt de netwerkinstellingen bekijken en wijzigen, inclusief het netwerk-/subnet waarin de Azure VM zich na failover bevindt en het IP-adres dat eraan wordt toegewezen.

1. In **Schijven** ziet u informatie over het besturingssysteem en de gegevensschijven van de VM.

## <a name="failover-to-azure"></a>Failover naar Azure

1. Klik in **Instellingen** > **Gerepliceerde items** op de VM > **Failover**.
2. Selecteer bij **Failover** **Meest recent** als het herstelpunt. 
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery probeert virtuele bronmachines af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Na het controleren van de failover, klikt u op **Doorvoeren**. Hiermee worden alle beschikbare herstelpunten verwijderd.

> [!WARNING]
> **Een failover die in uitvoering is, mag niet worden geannuleerd**: als u dat doet, wordt de failover wel beëindigd, maar de virtuele machine wordt niet meer gerepliceerd.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Failback van virtuele Azure-machine van Azure naar on-premises en replicatie van on-premises virtuele machine omkeren

Failback is in feite een failover van Azure naar de on-premises site waarna virtuele machines weer worden gerepliceerd van de on-premises site naar Azure.

1. Klik in **Instellingen** > **Gerepliceerde items** op de virtuele machine > **Geplande failover**.
2. Controleer bij **Geplande failover bevestigen** de failover-richting (van Azure) en selecteer de bron- en doellocaties.
3. Selecteer **Gegevens synchroniseren voor failover (alleen deltawijzigingen synchroniseren)** . Met deze optie beperkt u de downtime van de virtuele machine tot een minimum omdat er wordt gesynchroniseerd zonder de virtuele machine af te sluiten.
4. Start de failover. U kunt de voortgang van de failover volgen op het tabblad **Taken**.
5. Als de initiële synchronisatie is voltooid en u de virtuele Azure-machines wilt afsluiten, klikt u op **Taken** > naam-van-geplande-failover-taak> **Failover voltooien**. Hierdoor wordt de virtuele Azure-machine afgesloten, worden de meest recente wijzigingen overgebracht naar on-premises en wordt de on-premises virtuele machine gestart.
6. Meld u aan bij de on-premises virtuele machine om te controleren of deze inderdaad beschikbaar is.
7. De on-premises virtuele machine heeft nu de status **Wachten op doorvoering**. Klik op **Doorvoeren**. Hiermee verwijdert u de virtuele Azure-machines en de bijbehorende schijven, en bereidt u de on-premises virtuele machine voor op omgekeerde replicatie.
Om de on-premises virtuele machine te repliceren naar Azure, schakelt u **Omgekeerde replicatie** in. Hierdoor wordt de replicatie van deltawijzigingen geactiveerd die hebben plaatsgevonden sinds het uitschakelen van de virtuele Azure-machine.  
