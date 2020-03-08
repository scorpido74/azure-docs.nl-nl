---
title: Virtuele VMware-machines inschakelen voor herstel na nood geval met behulp van Azure Site Recovery
description: In dit artikel wordt beschreven hoe u VMware-VM-replicatie inschakelt voor nood herstel met behulp van de Azure Site Recovery-service
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362719"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Replicatie naar Azure inschakelen voor virtuele VMware-machines

In dit artikel wordt beschreven hoe u replicatie van on-premises virtuele VMware-machines naar Azure inschakelt.

## <a name="resolve-common-issues"></a>Veelvoorkomende problemen oplossen

* Elke schijf moet kleiner zijn dan 4 TB.
* De besturingssysteem schijf moet een standaard schijf zijn, niet een dynamische schijf.
* Voor virtuele machines van generatie 2/UEFI moet de besturingssysteem familie Windows zijn en moet de opstart schijf kleiner zijn dan 300 GB.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u hebt:

- [Stel uw on-premises bron omgeving in](vmware-azure-set-up-source.md).
- [Stel uw doel omgeving in op Azure](vmware-azure-set-up-target.md).
- [Controleer de vereisten](vmware-physical-azure-support-matrix.md) voordat u begint. Belang rijke aandachtspunten voor:
    - [Ondersteunde besturings systemen](vmware-physical-azure-support-matrix.md#replicated-machines) voor gerepliceerde machines.
    - Ondersteuning voor [opslag/schijf](vmware-physical-azure-support-matrix.md#storage) .
    - [Azure-vereisten](vmware-physical-azure-support-matrix.md#azure-vm-requirements) waaraan on-premises machines moeten voldoen.


## <a name="before-you-start"></a>Voordat u begint
Houd rekening met de volgende informatie wanneer u virtuele VMware-machines repliceert:

* Uw Azure-gebruikers account moet bepaalde [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) hebben om replicatie van een nieuwe virtuele machine naar Azure in te scha kelen.
* VMware-Vm's worden elke 15 minuten gedetecteerd. Het kan 15 minuten of langer duren voordat Vm's worden weer gegeven in de Azure Portal na detectie. Detectie kan ook 15 minuten of langer duren wanneer u een nieuwe vCenter-Server of vSphere-host toevoegt.
* Het kan 15 minuten of langer duren voor omgevings wijzigingen op de virtuele machine (zoals de installatie van VMware-hulpprogram ma's) die in de portal moeten worden bijgewerkt.
* U kunt de laatst gedetecteerde tijd voor virtuele VMware-machines controleren: Raadpleeg het veld **laatste contact persoon bij** op de pagina **configuratie servers** voor de VCenter-server/vSphere-host.
* Als u virtuele machines voor replicatie wilt toevoegen zonder te wachten op de geplande detectie, markeert u de configuratie server (maar klikt u er niet op) en selecteert u **vernieuwen**.
* Wanneer u replicatie inschakelt en de virtuele machine is voor bereid, wordt de Azure Site Recovery Mobility-service automatisch door de proces server geïnstalleerd.

## <a name="enable-replication"></a>Replicatie inschakelen

Houd rekening met de volgende informatie voordat u de stappen in deze sectie volgt:
* Azure Site Recovery wordt nu rechtstreeks gerepliceerd naar Managed disks voor alle nieuwe replicaties. De proces server schrijft replicatie logboeken naar een cache-opslag account in de doel regio. Deze logboeken worden gebruikt voor het maken van herstel punten in replica Managed disks met een naam Conventie van asrseeddisk.
* Power Shell-ondersteuning voor het repliceren naar Managed disks is beschikbaar via [AZ. Recovery Services module versie 2.0.0 en hoger](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* Op het moment van de failover wordt het door u geselecteerde herstel punt gebruikt voor het maken van de doel-beheerde schijf.
* Vm's die eerder zijn geconfigureerd om te worden gerepliceerd naar doel opslag accounts, worden niet beïnvloed.
* Replicatie naar opslag accounts voor een nieuwe virtuele machine is alleen beschikbaar via een Representational State overdracht (REST) API en Power shell. Gebruik Azure REST API versie 2016-08-10 of 2018-01-10 voor replicatie naar opslag accounts.

Volg de onderstaande stappen om de replicatie in te scha kelen:
1. Ga naar **stap 2: toepassings** > **bron**repliceren. Nadat u replicatie voor de eerste keer hebt ingeschakeld, selecteert u **+ repliceren** in de kluis om replicatie in te scha kelen voor aanvullende virtuele machines.
2. Selecteer de configuratie server in de **bron** pagina > **bron**.
3. Selecteer bij **computer type** **virtual machines** of **fysieke machines**.
4. Selecteer in **vCenter/vSphere-hypervisor** de vCenter-server waarmee de vSphere-host wordt beheerd, of selecteer de host. Deze instelling is niet relevant als u fysieke computers repliceert.
5. Selecteer de proces server. Als er geen extra proces servers zijn gemaakt, is de ingebouwde proces server van de configuratie server beschikbaar in de vervolg keuzelijst. De integriteits status van elke proces server wordt aangegeven volgens de aanbevolen limieten en andere para meters. Kies een goede proces server. Er kan geen [essentiële](vmware-physical-azure-monitor-process-server.md#process-server-alerts) proces server worden gekozen. U kunt [problemen oplossen en](vmware-physical-azure-troubleshoot-process-server.md) de fouten oplossen **of** een [scale-out proces server](vmware-azure-set-up-process-server-scale.md)instellen.
    ![het venster replicatie bron inschakelen](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Van [9,24 versies](service-updates-how-to.md#links-to-currently-supported-update-rollups)worden extra waarschuwingen geïntroduceerd om de status waarschuwingen van de proces server te verbeteren. Upgrade Site Recovery-onderdelen naar 9,24-versies of hoger voor alle waarschuwingen die moeten worden gegenereerd.

6. Selecteer bij **doel**het abonnement en de resource groep waarin u de virtuele machines waarvoor een failover is uitgevoerd wilt maken. Kies het implementatie model dat u wilt gebruiken in azure voor de failover-Vm's die zijn mislukt.
2. Selecteer het Azure-netwerk en-subnet waarmee de Azure-Vm's verbinding maken na een failover. Het netwerk moet zich in dezelfde regio bevinden als de Site Recovery service-kluis.

   Selecteer **nu configureren voor geselecteerde machines** om de netwerk instelling toe te passen op alle virtuele machines die u voor beveiliging selecteert. Selecteer **later configureren** om het Azure-netwerk per virtuele machine te selecteren. Als u geen netwerk hebt, moet u er een maken. Als u een netwerk wilt maken met behulp van Azure Resource Manager, selecteert u **nieuwe maken**. Selecteer een subnet, indien van toepassing, en selecteer vervolgens **OK**.
   
   ![Venster replicatie doel inschakelen](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Voor **virtuele machines** > **virtuele machines selecteren**selecteert u elke virtuele machine die u wilt repliceren. U kunt alleen virtuele machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Als u een bepaalde virtuele machine niet kunt zien of selecteren, Zie [bron machine wordt niet vermeld in de Azure Portal](https://aka.ms/doc-plugin-VM-not-showing) om het probleem op te lossen.

    ![Replicatie inschakelen voor het venster virtuele machines selecteren](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Selecteer voor **eigenschappen** > **eigenschappen configureren**het account dat door de proces server wordt gebruikt om automatisch de site Recovery Mobility-service op de virtuele machine te installeren. Kies ook het type doel-beheerde schijf waarnaar moet worden gerepliceerd, op basis van uw gegevens verloop patronen.
10. Standaard worden alle schijven van een virtuele bron machine gerepliceerd. Als u schijven wilt uitsluiten van replicatie, schakelt u het selectie vakje **opnemen** uit voor schijven die u niet wilt repliceren. Selecteer vervolgens **OK**. Later kunt u eventueel extra eigenschappen instellen. Meer informatie over het [uitsluiten van schijven](vmware-azure-exclude-disk.md).

    ![Het venster Eigenschappen voor replicatie configureren inschakelen](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Controleer bij **replicatie-instellingen** > replicatie- **instellingen configureren**of het juiste replicatie beleid is geselecteerd. U kunt replicatie beleids instellingen wijzigen bij **instellingen** > **replicatie beleid** > ***beleids naam*** > **Instellingen bewerken**. Wijzigingen die u toepast op een beleid, zijn ook van toepassing op replicatie en nieuwe virtuele machines.
1. Schakel **multi-VM-consistentie** in als u virtuele machines wilt verzamelen in een replicatie groep. Geef een naam op voor de groep en selecteer **OK**.

    > [!NOTE]
    >    * Virtuele machines in een replicatie groep worden samen gerepliceerd en hebben gedeelde crash-consistente en toepassings consistente herstel punten wanneer ze een failover uitvoeren.
    >    * Verzamel Vm's en fysieke servers samen zodat ze uw workloads spie gelen. Het inschakelen van multi-VM-consistentie kan de prestaties van de werk belasting beïnvloeden. Doe dit alleen als de virtuele machines dezelfde werk belasting uitvoeren en u consistentie nodig hebt.

    ![Replicatie venster inschakelen](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selecteer **Replicatie inschakelen**. U kunt de voortgang van de taak **beveiliging inschakelen** volgen bij **instellingen** > **taken** > **site Recovery taken**. Nadat de taak **beveiliging volt ooien** is uitgevoerd, is de virtuele machine klaar voor failover.

## <a name="view-and-manage-vm-properties"></a>Eigenschappen van virtuele machines weergeven en beheren

Controleer vervolgens de eigenschappen van de virtuele bron machine. Houd er rekening mee dat de Azure VM-naam moet voldoen aan de vereisten van de [virtuele Azure-machine](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Ga naar **instellingen** > **gerepliceerde items**en selecteer de virtuele machine. Op de pagina **Essentials** vindt u informatie over de instellingen en status van de virtuele machine.
1. In **Eigenschappen** kunt u de replicatie- en failoverinformatie van de virtuele machine weergeven.
1. In **Compute en Network** > **compute properties**kunt u meerdere VM-eigenschappen wijzigen. 

    ![Venster Eigenschappen van Compute en netwerk](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM-naam: Wijzig de naam zo nodig om aan de vereisten van Azure te voldoen.
    * Grootte van de doel-VM of het VM-type: de standaard grootte voor de virtuele machine wordt gekozen op basis van enkele para meters die het aantal schijven, het aantal NIC'S, het CPU-aantal, het geheugen en de beschik bare VM-rollen in de Azure-doel regio bevatten Azure Site Recovery kiest de eerste beschik bare VM-grootte die voldoet aan alle criteria. U kunt op elk gewenst moment een andere VM-grootte selecteren op basis van uw behoeften voordat de failover wordt uitgevoerd. Houd er rekening mee dat de grootte van de VM-schijf ook is gebaseerd op de grootte van de bron schijf en kan worden gewijzigd na een failover. Meer informatie over schijf grootten en IOPS-tarieven bij [schaal baarheid en prestatie doelen voor VM-schijven in Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Resource groep: u kunt een [resource groep](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)selecteren, van waaruit een virtuele machine deel wordt van een post-failover. U kunt deze instelling op elk gewenst moment wijzigen vóór de failover. Als u na de failover een migratie van de virtuele machine naar een andere resource groep hebt, worden de beveiligings instellingen voor die virtuele machine verbroken.
    * Beschikbaarheidsset: u kunt een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) selecteren als de virtuele machine deel moet uitmaken van een post-failover. Wanneer u een beschikbaarheidsset selecteert, houdt u de volgende informatie in acht:

        * Alleen beschikbaarheids sets die bij de opgegeven resource groep horen, worden weer gegeven.  
        * Vm's die zich op verschillende virtuele netwerken bevinden, kunnen geen deel uitmaken van dezelfde beschikbaarheidsset.
        * Alleen virtuele machines met dezelfde grootte kunnen deel uitmaken van een beschikbaarheidsset.
1. U kunt ook informatie toevoegen over het doelnet werken, het subnet en het IP-adres dat is toegewezen aan de Azure VM.
2. In **schijven**ziet u het besturings systeem en de gegevens schijven op de virtuele machine die worden gerepliceerd.

### <a name="configure-networks-and-ip-addresses"></a>Netwerken en IP-adressen configureren

U kunt het doel-IP-adres instellen. Als u geen adres opgeeft, wordt DHCP gebruikt voor de virtuele machine waarvoor een failover is uitgevoerd. Als u een adres instelt dat niet beschikbaar is op failover, werkt de failover niet. Als het adres beschikbaar is in het testfailover, kunt u hetzelfde doel-IP-adres gebruiken voor de testfailover.

Het aantal netwerk adapters wordt bepaald door de grootte die u opgeeft voor de virtuele doel machine, als volgt:

- Als het aantal netwerk adapters op de virtuele bron machine kleiner is dan of gelijk is aan het aantal adapters dat is toegestaan voor de grootte van de doel-VM, heeft het doel hetzelfde aantal adapters als de bron.
- Als het aantal adapters voor de virtuele bron machine groter is dan het aantal dat is toegestaan voor de grootte van de doel-VM, wordt het maximum voor de doel grootte gebruikt. Als een virtuele bron machine bijvoorbeeld twee netwerk adapters heeft en de grootte van de doel-VM vier ondersteunt, heeft de virtuele doel machine twee adapters. Als de bron-VM twee adapters heeft, maar de doel grootte slechts een ondersteunt, heeft de doel-VM slechts één adapter.
- Als de virtuele machine meerdere netwerk adapters heeft, worden deze allemaal verbonden met hetzelfde netwerk. De eerste adapter die wordt weer gegeven in de lijst wordt ook de *standaard* netwerk adapter in de virtuele machine van Azure. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Klanten van micro soft-Software Assurance kunnen Azure Hybrid Benefit gebruiken om de licentie kosten voor Windows Server-computers die worden gemigreerd naar Azure, op te slaan. Het voor deel is ook van toepassing op herstel na nood gevallen van Azure. Als u in aanmerking komt, kunt u het voor deel toewijzen aan de virtuele machine die Site Recovery maakt als er een failover plaatsvindt. Voer hiervoor de volgende stappen uit:
1. Ga naar de **computer-en netwerk eigenschappen** van de gerepliceerde virtuele machine.
2. Antwoord wanneer u wordt gevraagd of u een Windows Server-licentie hebt waarmee u in aanmerking komt voor Azure Hybrid Benefit.
3. Bevestig dat u een Windows Server-licentie met Software Assurance hebt die u kunt gebruiken om het voor deel toe te passen op de virtuele machine die tijdens de failover wordt gemaakt.
4. Sla de instellingen voor de gerepliceerde virtuele machine op.

Meer informatie over [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine een beveiligde status heeft bereikt, probeert u een [failover](site-recovery-failover.md) om te controleren of uw toepassing wordt weer gegeven in Azure.

* Meer informatie over het [opschonen van de registratie-en beveiligings instellingen](site-recovery-manage-registration-and-protection.md) om replicatie uit te scha kelen.
* Meer informatie over het [automatiseren van replicatie voor uw virtuele machines met behulp van Power shell](vmware-azure-disaster-recovery-powershell.md).
