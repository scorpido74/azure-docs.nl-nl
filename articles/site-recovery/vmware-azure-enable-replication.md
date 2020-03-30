---
title: VMware VM's inschakelen voor herstel na noodgevallen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u Vm-replicatie van VMware inschakelt voor noodherstel met behulp van de Azure Site Recovery-service
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 06/28/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 10b3e572ec61d1eff342f24a6a5a7bcba6276983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257314"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Replicatie inschakelen voor Azure voor VMware VM's

In dit artikel wordt beschreven hoe u replicatie van on-premises Vm's vMware naar Azure inschakelen.

## <a name="resolve-common-issues"></a>Veelvoorkomende problemen oplossen

* Elke schijf moet kleiner zijn dan 4 TB.
* De OS-schijf moet een basisschijf zijn, geen dynamische schijf.
* Voor virtuele machines met 2/UEFI moet de familie van het besturingssysteem Windows zijn en moet de opstartschijf kleiner zijn dan 300 GB.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u:

- [Stel uw on-premises bronomgeving in.](vmware-azure-set-up-source.md)
- [Stel uw doelomgeving in Azure in.](vmware-azure-set-up-target.md)
- [Controleer vereisten en vereisten](vmware-physical-azure-support-matrix.md) voordat u begint. Belangrijke dingen om op te merken zijn:
    - [Ondersteunde besturingssystemen](vmware-physical-azure-support-matrix.md#replicated-machines) voor gerepliceerde machines.
    - [Ondersteuning voor opslag/schijf.](vmware-physical-azure-support-matrix.md#storage)
    - [Azure-vereisten](vmware-physical-azure-support-matrix.md#azure-vm-requirements) waaraan on-premises machines moeten voldoen.


## <a name="before-you-start"></a>Voordat u begint
Wanneer u virtuele VMware-machines repliceert, houdt u rekening met deze informatie:

* Uw Azure-gebruikersaccount moet over bepaalde [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) beschikken om replicatie van een nieuwe virtuele machine naar Azure mogelijk te maken.
* VMware VM's worden elke 15 minuten ontdekt. Het kan 15 minuten of langer duren voordat VM's na detectie in de Azure-portal worden weergegeven. Op dezelfde manier kan detectie 15 minuten of langer duren wanneer u een nieuwe vCenter-server of vSphere-host toevoegt.
* Het kan 15 minuten of langer duren voordat omgevingswijzigingen op de virtuele machine (zoals de installatie van VMware-gereedschappen) in de portal worden bijgewerkt.
* U de laatst ontdekte tijd controleren voor VMware VM's: Zie het veld **Laatste contactpersoon op** de pagina **Configuratieservers** voor de vCenter-server/vSphere-host.
* Als u virtuele machines wilt toevoegen voor replicatie zonder te wachten op de geplande detectie, markeert u de configuratieserver (maar klikt u er niet op) en selecteert u **Vernieuwen**.
* Wanneer u replicatie inschakelt, als de virtuele machine is voorbereid, installeert de processerver automatisch de Azure Site Recovery Mobility-service erop.

## <a name="enable-replication"></a>Replicatie inschakelen

Voordat u de stappen in deze sectie volgt, moet u de volgende informatie noteren:
* Azure Site Recovery repliceert nu rechtstreeks naar beheerde schijven voor alle nieuwe replicaties. De processerver schrijft replicatielogboeken naar een cacheopslagaccount in het doelgebied. Deze logboeken worden gebruikt om herstelpunten te maken in replica beheerde schijven met naamgevingsconventie van asrseeddisk.
* Powershell-ondersteuning voor het repliceren naar beheerde schijven is beschikbaar via [Az.RecoveryServices-moduleversie 2.0.0 en verder](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 
* Op het moment van failover wordt het herstelpunt dat u selecteert gebruikt om de door het doel beheerde schijf te maken.
* VM's die eerder zijn geconfigureerd om te repliceren naar doelopslagaccounts, worden niet beïnvloed.
* Replicatie naar opslagaccounts voor een nieuwe virtuele machine is alleen beschikbaar via een REST-API (Representational State Transfer) en Powershell. Gebruik Azure REST API-versie 2016-08-10 of 2018-01-10 voor het repliceren naar opslagaccounts.

Volg onderstaande stappen om replicatie in te schakelen:
1. Ga naar **stap 2:** > **Toepassingsbron**repliceren . Nadat u replicatie voor de eerste keer hebt ingeschakeld, selecteert u **+Repliceren** in de kluis om replicatie voor extra virtuele machines in te schakelen.
2. Selecteer op de pagina **Bron** > **Bron**de configuratieserver.
3. Selecteer **virtuele machines** of **fysieke machines voor** **machinetype**.
4. Selecteer in **vCenter/vSphere-hypervisor** de vCenter-server waarmee de vSphere-host wordt beheerd, of selecteer de host. Deze instelling is niet relevant als u fysieke computers repliceert.
5. Selecteer de processerver. Als er geen extra processervers zijn gemaakt, is de ingebouwde processerver van de configuratieserver beschikbaar in de vervolgkeuzelijst. De status van elke processerver wordt aangegeven volgens aanbevolen limieten en andere parameters. Kies een gezonde processerver. Een [kritieke](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver kan niet worden gekozen. U de fouten [oplossen en oplossen](vmware-physical-azure-troubleshoot-process-server.md) **of** een [scale-outprocesserver](vmware-azure-set-up-process-server-scale.md)instellen.
    ![Replicatiebronvenster inschakelen](media/vmware-azure-enable-replication/ps-selection.png)

> [!NOTE]
> Vanaf [9,24 versies](service-updates-how-to.md#links-to-currently-supported-update-rollups)worden extra waarschuwingen geïntroduceerd om de statuswaarschuwingen van de processerver te verbeteren. Upgrade Site Recovery-componenten naar 9,24 versies of hoger om alle waarschuwingen te genereren.

6. Selecteer **bij Target**de abonnements- en resourcegroep waar u de defecte virtuele machines wilt maken. Kies het implementatiemodel dat u in Azure wilt gebruiken voor de niet-over-VM's.
2. Selecteer het Azure-netwerk en subnet waarmee de Azure VM's verbinding maken na een failover. Het netwerk moet zich in dezelfde regio bevinden als de kluis van de service Site Recovery.

   Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle virtuele machines die u selecteert voor bescherming. Selecteer **Later configureren** om het Azure-netwerk per virtuele machine te selecteren. Als u geen netwerk hebt, moet u er een maken. Als u een netwerk wilt maken met Azure Resource Manager, selecteert **u Nieuw maken**. Selecteer indien van toepassing een subnet en selecteer **OK**.
   
   ![Replicatiedoelvenster inschakelen](./media/vmware-azure-enable-replication/enable-rep3.png)

1. Selecteer **voor virtuele machines** > **Selecteer virtuele machines**elke virtuele machine die u wilt repliceren. U alleen virtuele machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Zie [Bronmachine wordt niet weergegeven in de Azure-portal](https://aka.ms/doc-plugin-VM-not-showing) om het probleem op te lossen als u een bepaalde virtuele machine niet zien of selecteren.

    ![Replicatie selecteren venster virtuele machines inschakelen](./media/vmware-azure-enable-replication/enable-replication5.png)

1. Selecteer voor **Eigenschappen** > **Configureren het**account dat de processerver gebruikt om de service Site Recovery Mobility automatisch op de virtuele machine te installeren. Kies ook het type doelbeheerschijf dat u wilt repliceren op basis van uw gegevensverlooppatronen.
10. Standaard worden alle schijven van een virtuele bronmachine gerepliceerd. Als u schijven wilt uitsluiten van replicatie, schakelt u het selectievakje **Opnemen** uit voor schijven die u niet wilt repliceren. Selecteer vervolgens **OK**. Later kunt u eventueel extra eigenschappen instellen. Meer informatie over [het uitsluiten van schijven](vmware-azure-exclude-disk.md).

    ![Venster Eigenschappen van replicatieconfigureren inschakelen](./media/vmware-azure-enable-replication/enable-replication6.png)

1. Bij **Replicatieinstellingen** > **Replicatieinstellingen configureren,** controleer of het juiste replicatiebeleid is geselecteerd. U de instellingen voor replicatiebeleid wijzigen bij **Instellingen** > **Replicatiebeleid** > ***met de naam*** > **Instellingen bewerken**. Wijzigingen die u toepast op een beleid, zijn ook van toepassing op replicerende en nieuwe virtuele machines.
1. **Multi-VM-consistentie** inschakelen als u virtuele machines in een replicatiegroep wilt verzamelen. Geef een naam voor de groep op en selecteer **OK**.

    > [!NOTE]
    >    * Virtuele machines in een replicatiegroep repliceren samen en hebben crashconsistente en app-consistente herstelpunten gedeeld wanneer ze mislukken.
    >    * Verzamel VM's en fysieke servers, zodat ze uw workloads weerspiegelen. Het inschakelen van multi-VM-consistentie kan de prestaties van de werkbelasting beïnvloeden. Doe dit alleen als de virtuele machines dezelfde werkbelasting uitvoeren en u consistentie nodig hebt.

    ![Replicatievenster inschakelen](./media/vmware-azure-enable-replication/enable-replication7.png)
    
1. Selecteer **Replicatie inschakelen**. U de voortgang van de taak **Beveiliging inschakelen** bij **Vacatures** > **voor hersteltaken**van**vacatures** > bijhouden. Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de virtuele machine klaar voor failover.

## <a name="view-and-manage-vm-properties"></a>Eigenschappen van virtuele machines weergeven en beheren

Controleer vervolgens de eigenschappen van de virtuele bronmachine. Houd er rekening mee dat de Azure VM-naam moet voldoen aan [de vereisten voor virtuele azure-machines.](vmware-physical-azure-support-matrix.md#replicated-machines)

1. Ga naar**Gerepliceerde items** **instellingen** > en selecteer de virtuele machine. De pagina **Essentials** toont informatie over de instellingen en status van de VM.
1. In **Eigenschappen**u replicatie- en failovergegevens voor de vm weergeven.
1. In **compute- en network** > **compute-eigenschappen**u meerdere VM-eigenschappen wijzigen. 

    ![Venster Reken- en netwerkeigenschappen](./media/vmware-azure-enable-replication/vmproperties.png)

    * Azure VM-naam: wijzig de naam om te voldoen aan Azure-vereisten, indien nodig.
    * DoelVM-grootte of VM-type: de standaardVM-grootte wordt gekozen op basis van een aantal parameters, waaronder het aantal schijven, het aantal NIC's, het aantal CPU-kernen, het geheugen en de beschikbare VM-rolgroottes in het doelazure-gebied. Azure Site Recovery kiest de eerste beschikbare VM-grootte die aan alle criteria voldoet. U op elk gewenst moment een andere VM-grootte selecteren op basis van uw behoeften voordat u een fail-over maakt. Houd er rekening mee dat de grootte van de VM-schijf ook is gebaseerd op de grootte van de bronschijf en dat deze alleen kan worden gewijzigd na een failover. Meer informatie over schijfformaten en IOPS-snelheden bij [schaalbaarheids- en prestatiedoelen voor VM-schijven in Windows](../virtual-machines/windows/disk-scalability-targets.md).

    *  Resourcegroep: u een [resourcegroep](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-resource-groups-guidelines)selecteren, waaruit een virtuele machine onderdeel wordt van een failovervoor na bericht. U deze instelling op elk gewenst moment wijzigen voordat u een fail-over maakt. Als u de virtuele machine na het mislukken migreert naar een andere resourcegroep, breken de beveiligingsinstellingen voor die virtuele machine.
    * Beschikbaarheidsset: u een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) selecteren als uw virtuele machine deel moet uitmaken van een failoverpost. Wanneer u een beschikbaarheidsset selecteert, houdt u rekening met de volgende gegevens:

        * Alleen beschikbaarheidssets die deel uitmaken van de opgegeven resourcegroep worden weergegeven.  
        * VM's die zich op verschillende virtuele netwerken bevinden, kunnen geen deel uitmaken van dezelfde beschikbaarheidsset.
        * Alleen virtuele machines van dezelfde grootte kunnen deel uitmaken van een beschikbaarheidsset.
1. U ook informatie toevoegen over het doelnetwerk, subnet en IP-adres dat is toegewezen aan de Azure VM.
2. In **schijven**u het besturingssysteem en de gegevensschijven op de VM zien die worden gerepliceerd.

### <a name="configure-networks-and-ip-addresses"></a>Netwerken en IP-adressen configureren

U kunt het doel-IP-adres instellen. Als u geen adres opgeeft, gebruikt de mislukte virtuele machine DHCP. Als u een adres instelt dat niet beschikbaar is bij failover, werkt de failover niet. Als het adres beschikbaar is in het failovernetwerk van de test, u hetzelfde doel-IP-adres gebruiken voor een testfailover.

Het aantal netwerkadapters wordt als volgt bepaald door de grootte die u opgeeft voor de virtuele doelmachine:

- Als het aantal netwerkadapters op de virtuele bronmachine kleiner is dan of gelijk is aan het aantal adapters dat is toegestaan voor de grootte van de doel-VM, heeft het doel hetzelfde aantal adapters als de bron.
- Als het aantal adapters voor de virtuele bronmachine groter is dan het aantal dat is toegestaan voor de grootte van de doel-VM, wordt het maximum van de doelgrootte gebruikt. Als een virtuele bronmachine bijvoorbeeld twee netwerkadapters heeft en de grootte van de doel-VM vier ondersteunt, heeft de virtuele doelmachine twee adapters. Als de bron-VM twee adapters heeft, maar de doelgrootte slechts één ondersteunt, heeft de doel-VM slechts één adapter.
- Als de virtuele machine meerdere netwerkadapters heeft, maken ze allemaal verbinding met hetzelfde netwerk. De eerste adapter die in de lijst wordt weergegeven, wordt ook de *standaardnetwerkadapter* in de virtuele Azure-machine. 

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Klanten van Microsoft Software Assurance kunnen Azure Hybrid Benefit gebruiken om te besparen op licentiekosten voor Windows Server-computers die zijn gemigreerd naar Azure. Het voordeel is ook van toepassing op Azure disaster recovery. Als u in aanmerking komt, u het voordeel toewijzen aan de virtuele machine die Site Recovery maakt als er een failover is. Voer hiervoor de volgende stappen uit:
1. Ga naar de **computer- en netwerkeigenschappen** van de gerepliceerde virtuele machine.
2. Antwoord wanneer u wordt gevraagd of u een Windows Server-licentie hebt waardoor u in aanmerking komt voor Azure Hybrid Benefit.
3. Controleer of u een in aanmerking komende Windows Server-licentie hebt met Software Assurance die u gebruiken om het voordeel toe te passen op de VM die bij failover wordt gemaakt.
4. Sla de instellingen voor de gerepliceerde virtuele machine op.

Meer informatie over [Azure Hybrid Benefit](https://aka.ms/azure-hybrid-benefit-pricing).



## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine een beveiligde status heeft bereikt, probeert u een [failover](site-recovery-failover.md) om te controleren of uw toepassing wordt weergegeven in Azure.

* Meer informatie over het [schoonmaken van registratie- en beveiligingsinstellingen](site-recovery-manage-registration-and-protection.md) om replicatie uit te schakelen.
* Meer informatie over het [automatiseren van replicatie voor uw virtuele machines met Powershell.](vmware-azure-disaster-recovery-powershell.md)
