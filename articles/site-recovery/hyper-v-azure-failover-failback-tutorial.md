---
title: Failover van virtuele Hyper-V-machines instellen in Azure in Azure Site Recovery
description: Meer informatie over het uitvoeren van een failover van virtuele Hyper-V-machines naar Azure met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 03826abf6da94859c510f4c127dfce035aa79370
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75498171"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Failover van virtuele Hyper-V-machines naar Azure

In deze zelf studie wordt beschreven hoe u een failover voor virtuele Hyper-V-machines naar Azure met [Azure site Recovery](site-recovery-overview.md). Nadat u een failover hebt uitgevoerd, valt u terug op uw on-premises site wanneer deze beschikbaar is. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer de eigenschappen van de Hyper-V-virtuele machine om te controleren of aan de vereisten van Azure wordt voldaan.
> * Failover van specifieke Vm's naar Azure.


Dit is de vijfde zelfstudie in een reeks. In deze zelfstudie wordt ervan uitgegaan dat u de taken in de vorige zelfstudies al hebt voltooid.    

1. [Azure voorbereiden](tutorial-prepare-azure.md)
2. [On-premises Hyper-V voorbereiden](tutorial-prepare-on-premises-hyper-v.md)
3. Noodherstel configureren voor [virtuele Hyper-V-machines](tutorial-hyper-v-to-azure.md) of voor [virtuele Hyper-V-machines die worden beheerd in System Center VMM-clouds](tutorial-hyper-v-vmm-to-azure.md)
4. [Noodherstelanalyse uitvoeren](tutorial-dr-drill-azure.md)

[Meer informatie over](failover-failback-overview.md#types-of-failover) verschillende typen failover. Als u een failover wilt uitvoeren voor meerdere Vm's in een herstel plan, raadpleegt u [dit artikel](site-recovery-failover.md).

## <a name="prepare-for-failover"></a>Voorbereiden op een failover 
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

## <a name="fail-over-to-azure"></a>Failover naar Azure

1. Klik in **Instellingen** > **Gerepliceerde items** op de VM > **Failover**.
2. Selecteer bij **Failover** **Meest recent** als het herstelpunt. 
3. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery probeert virtuele bronmachines af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
4. Na het controleren van de failover, klikt u op **Doorvoeren**. Hiermee worden alle beschikbare herstelpunten verwijderd.

> [!WARNING]
> **Een failover die in uitvoering is, mag niet worden geannuleerd**: als u dat doet, wordt de failover wel beëindigd, maar de virtuele machine wordt niet meer gerepliceerd.

## <a name="connect-to-failed-over-vm"></a>Verbinding maken met mislukte VM

1. Als u na een failover verbinding wilt maken met virtuele Azure-machines met behulp van Remote Desktop Protocol (RDP) en Secure Shell (SSH), [controleert u of aan de vereisten is voldaan](failover-failback-overview.md#connect-to-azure-after-failover).
2. Ga na een failover naar de virtuele machine en valideer deze door [verbinding te maken](../virtual-machines/windows/connect-logon.md) .
3. **Herstel punt voor wijzigingen** gebruiken als u na een failover een ander herstel punt wilt gebruiken. Nadat u de failover in de volgende stap hebt doorgevoerd, is deze optie niet meer beschikbaar.
4. Na validatie selecteert u **door voeren** om het herstel punt van de virtuele machine na een failover te volt ooien.
5. Nadat u de wijzigingen hebt doorgevoerd, worden alle andere beschik bare herstel punten verwijderd. Met deze stap wordt de failover voltooid.

>[!TIP]
> Als u na de failover problemen ondervindt met de verbinding, volgt u de [hand leiding](site-recovery-failover-to-azure-troubleshoot.md)voor het oplossen van problemen.


## <a name="next-steps"></a>Volgende stappen

Na een failover kunt u de Azure-Vm's opnieuw beveiligen zodat ze van Azure naar on-premises worden gerepliceerd. Nadat de Vm's opnieuw zijn beveiligd en repliceren naar de on-premises site, kunt u een failback uitvoeren van Azure wanneer u klaar bent.
