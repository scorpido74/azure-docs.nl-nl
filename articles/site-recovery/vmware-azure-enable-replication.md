---
title: VMware VM's inschakelen voor herstel na noodgevallen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u Vm-replicatie van VMware inschakelt voor noodherstel met behulp van de Azure Site Recovery-service
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.date: 04/01/2020
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 6547bcf2061213cd01550367171d432900693ea5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584135"
---
# <a name="enable-replication-to-azure-for-vmware-vms"></a>Replicatie inschakelen voor Azure voor VMware VM's

In dit artikel wordt beschreven hoe u replicatie van on-premises Virtuele VMware-machines (VM) naar Azure inschakelen.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat uw systeem aan de volgende criteria voldoet:

- [Stel uw on-premises bronomgeving in.](vmware-azure-set-up-source.md)
- [Stel uw doelomgeving in Azure in.](vmware-azure-set-up-target.md)
- [Controleer vereisten en vereisten](vmware-physical-azure-support-matrix.md) voordat u begint. Belangrijke dingen om op te merken zijn:
  - [Ondersteunde besturingssystemen](vmware-physical-azure-support-matrix.md#replicated-machines) voor gerepliceerde machines.
  - [Ondersteuning voor opslag/schijf.](vmware-physical-azure-support-matrix.md#storage)
  - [Azure-vereisten](vmware-physical-azure-support-matrix.md#azure-vm-requirements) waaraan on-premises machines moeten voldoen.

### <a name="resolve-common-issues"></a>Veelvoorkomende problemen oplossen

- Elke schijf moet kleiner zijn dan 4 TB.
- De schijf van het besturingssysteem moet een basisschijf zijn, geen dynamische schijf.
- Voor virtuele machines met UEFI-generatie moet de familie van het besturingssysteem Windows zijn en moet de opstartschijf kleiner zijn dan 300 GB.

## <a name="before-you-start"></a>Voordat u begint

Wanneer u virtuele Machines van VMware repliceert, moet u rekening houden met deze informatie:

- Uw Azure-gebruikersaccount moet over bepaalde [machtigingen](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) beschikken om replicatie van een nieuwe virtuele machine naar Azure mogelijk te maken.
- VMware VM's worden elke 15 minuten ontdekt. Het kan 15 minuten of langer duren voordat VM's na detectie in de Azure-portal worden weergegeven. Wanneer u een nieuwe vCenter-server of vSphere-host toevoegt, kan detectie 15 minuten of langer duren.
- Het kan 15 minuten of langer duren voordat omgevingswijzigingen op de virtuele machine worden bijgewerkt in de portal. Bijvoorbeeld de installatie van VMware-gereedschappen.
- U de laatst ontdekte tijd controleren voor VMware VM's: Zie het veld **Laatste contactpersoon op** de pagina **Configuratieservers** voor de vCenter-server/vSphere-host.
- Als u virtuele machines wilt toevoegen voor replicatie zonder te wachten op de geplande detectie, markeert u de configuratieserver (maar klikt u er niet op) en selecteert u **Vernieuwen**.
- Wanneer u replicatie inschakelt, installeert de processerver automatisch de Azure Site Recovery Mobility-service op de VM als de virtuele machine is voorbereid.

## <a name="enable-replication"></a>Replicatie inschakelen

Voordat u de stappen in deze sectie doet, controleert u de volgende informatie:

- Azure Site Recovery repliceert nu rechtstreeks naar beheerde schijven voor alle nieuwe replicaties. De processerver schrijft replicatielogboeken naar een cacheopslagaccount in het doelgebied. Deze logboeken worden gebruikt om herstelpunten te maken `asrseeddisk`in replica beheerde schijven met naamgevingsconventie van .
- PowerShell-ondersteuning voor replicatie naar beheerde schijven is beschikbaar vanaf [Az.RecoveryServices-moduleversie 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)
- Op het moment van failover wordt het herstelpunt dat u selecteert gebruikt om de door het doel beheerde schijf te maken.
- VM's die eerder zijn geconfigureerd om te repliceren naar doelopslagaccounts, worden niet beïnvloed.
- Replicatie naar opslagaccounts voor een nieuwe virtuele machine is alleen beschikbaar via een REST-API (Representational State Transfer) en PowerShell. Gebruik Azure REST API-versie 2016-08-10 of 2018-01-10 voor replicatie naar opslagaccounts.

Voer de volgende stappen uit om replicatie in te schakelen:

1. Ga naar **stap 2:** > **Toepassingsbron**repliceren . Nadat u replicatie voor de eerste keer hebt ingeschakeld, selecteert u **+Repliceren** in de kluis om replicatie voor extra virtuele machines in te schakelen.
1. Selecteer op de pagina **Bron** > **Bron**de configuratieserver.
1. Selecteer **virtuele machines** of **fysieke machines voor** **machinetype**.
1. Selecteer in **vCenter/vSphere-hypervisor** de vCenter-server waarmee de vSphere-host wordt beheerd, of selecteer de host. Deze instelling is niet relevant als u fysieke computers repliceert.
1. Selecteer de processerver. Als er geen extra processervers zijn gemaakt, is de ingebouwde processerver van de configuratieserver beschikbaar in het vervolgkeuzemenu. De status van elke processerver wordt aangegeven volgens aanbevolen limieten en andere parameters. Kies een gezonde processerver. Er kan geen [kritieke](vmware-physical-azure-monitor-process-server.md#process-server-alerts) processerver worden gekozen. U de fouten [oplossen en oplossen](vmware-physical-azure-troubleshoot-process-server.md) **of** een [scale-outprocesserver](vmware-azure-set-up-process-server-scale.md)instellen.

   :::image type="content" source="./media/vmware-azure-enable-replication/ps-selection.png" alt-text="Replicatiebronvenster inschakelen":::

   > [!NOTE]
   > Vanaf [versie 9.24](site-recovery-whats-new.md)worden aanvullende waarschuwingen geïntroduceerd om de statuswaarschuwingen van de processerver te verbeteren. Upgrade de siteherstelcomponenten naar versie 9.24 of hoger om alle waarschuwingen te genereren.

1. Selecteer **bij Target**de abonnements- en resourcegroep waar u de mislukte optie voor virtuele machines wilt maken. Kies het implementatiemodel dat u in Azure wilt gebruiken voor de mislukte vm's.
1. Selecteer het Azure-netwerk en subnet waarmee de Azure VM's verbinding maken na een failover. Het netwerk moet zich in dezelfde regio bevinden als de kluis van de service Site Recovery.

   Selecteer **Nu configureren voor geselecteerde machines** om de netwerkinstelling toe te passen op alle virtuele machines die u selecteert voor bescherming. Selecteer **Later configureren** om het Azure-netwerk per virtuele machine te selecteren. Als u geen netwerk hebt, moet u er een maken. Als u een netwerk wilt maken met Azure Resource Manager, selecteert **u Nieuw maken**. Selecteer indien van toepassing een subnet en selecteer **OK**.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-rep3.png" alt-text="Replicatiedoelvenster inschakelen":::

1. Selecteer **voor virtuele machines** > **Selecteer virtuele machines**elke virtuele machine die u wilt repliceren. U alleen virtuele machines selecteren waarvoor replicatie kan worden ingeschakeld. Selecteer vervolgens **OK**. Zie [Bronmachine wordt niet weergegeven in de Azure-portal](vmware-azure-troubleshoot-replication.md#step-3-troubleshoot-source-machines-that-arent-available-for-replication) om het probleem op te lossen als u een bepaalde virtuele machine niet zien of selecteren.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication5.png" alt-text="Replicatie selecteren venster virtuele machines inschakelen":::

1. Selecteer voor **Eigenschappen** > **Eigenschappen configureren**het account dat de processerver gebruikt om de service Site Recovery Mobility automatisch op de VM te installeren. Kies ook het type doelbeheerschijf dat moet worden gebruikt voor replicatie op basis van uw gegevensverlooppatronen.
1. Standaard worden alle schijven van een bron-VM gerepliceerd. Als u schijven wilt uitsluiten van replicatie, schakelt u het selectievakje **Opnemen** uit voor schijven die u niet wilt repliceren. Selecteer vervolgens **OK**. Later kunt u eventueel extra eigenschappen instellen. [Meer informatie](vmware-azure-exclude-disk.md) over het uitsluiten van schijven.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication6.png" alt-text="Venster Eigenschappen van replicatieconfigureren inschakelen":::

1. Vanuit **Replicatieinstellingen** > **Replicatieinstellingen configureren,** controleer of het juiste replicatiebeleid is geselecteerd. U de instellingen voor replicatiebeleid wijzigen bij **Instellingen** > **Replicatiebeleid** > _met de naam_ > **Instellingen bewerken**. Wijzigingen die van toepassing zijn op een beleid, zijn ook van toepassing op replicerende en nieuwe virtuele machines.
1. Als u virtuele machines in een replicatiegroep wilt verzamelen, schakelt u **multi-VM-consistentie**in. Geef een naam voor de groep op en selecteer **OK**.

   > [!NOTE]
   > - Virtuele machines in een replicatiegroep repliceren samen en hebben crashconsistente en app-consistente herstelpunten gedeeld wanneer ze mislukken.
   > - Verzamel VM's en fysieke servers, zodat ze uw workloads weerspiegelen. Het inschakelen van multi-VM-consistentie kan de prestaties van de werkbelasting beïnvloeden. Doe dit alleen als de virtuele machines dezelfde werkbelasting uitvoeren en u consistentie nodig hebt.

   :::image type="content" source="./media/vmware-azure-enable-replication/enable-replication7.png" alt-text="Replicatievenster inschakelen":::

1. Selecteer **Replicatie inschakelen**. U de voortgang van de taak **Beveiliging inschakelen** bij **Vacatures** > **voor hersteltaken**van**vacatures** > bijhouden. Nadat de taak **Beveiliging voltooien** is uitgevoerd, is de virtuele machine klaar voor failover.

## <a name="view-and-manage-vm-properties"></a>Eigenschappen van virtuele machines weergeven en beheren

Controleer vervolgens de eigenschappen van de virtuele bronmachine. Houd er rekening mee dat de Azure VM-naam moet voldoen aan [de vereisten voor virtuele azure-machines.](vmware-physical-azure-support-matrix.md#replicated-machines)

1. Ga naar**Gerepliceerde items** **instellingen** > en selecteer de virtuele machine. De pagina **Essentials** toont informatie over de instellingen en status van de VM.
1. In **Eigenschappen**u replicatie- en failovergegevens voor de vm weergeven.
1. In **compute- en network** > **compute-eigenschappen**u meerdere VM-eigenschappen wijzigen.

   :::image type="content" source="./media/vmware-azure-enable-replication/vmproperties.png" alt-text="Venster Reken- en netwerkeigenschappen":::

   - **Azure VM-naam:** wijzig de naam om te voldoen aan Azure-vereisten, indien nodig.
   - **DoelVM-grootte of VM-type:** de standaardVM-grootte wordt gekozen op basis van parameters die het aantal schijven, het aantal NIC's, het aantal CPU-kernen, het geheugen en de beschikbare VM-rolgroottes in het doelazure-gebied omvatten. Azure Site Recovery kiest de eerste beschikbare VM-grootte die aan alle criteria voldoet. U op elk gewenst moment een andere VM-grootte selecteren op basis van uw behoeften voordat u een fail-over maakt. De vm-schijfgrootte is ook gebaseerd op de grootte van de bronschijf en kan alleen worden gewijzigd na een failover. Meer informatie over schijfformaten en IOPS-snelheden bij [schaalbaarheids- en prestatiedoelen voor VM-schijven in Windows](/azure/virtual-machines/windows/disk-scalability-targets).
   - **Resourcegroep**: U een [resourcegroep](/azure/azure-resource-manager/management/overview#resource-groups)selecteren waaruit een virtuele machine onderdeel wordt van een failoverpost. U deze instelling op elk gewenst moment wijzigen voordat u een fail-over maakt. Als u de virtuele machine na het mislukken migreert naar een andere resourcegroep, breken de beveiligingsinstellingen voor die virtuele machine.
   - **Beschikbaarheidsset**: U een [beschikbaarheidsset](/azure/virtual-machines/windows/tutorial-availability-sets) selecteren als uw virtuele machine deel moet uitmaken van een failover post. Wanneer u een beschikbaarheidsset selecteert, houdt u rekening met de volgende gegevens:
     - Alleen beschikbaarheidssets die deel uitmaken van de opgegeven resourcegroep worden weergegeven.
     - VM's die zich op verschillende virtuele netwerken bevinden, kunnen geen deel uitmaken van dezelfde beschikbaarheidsset.
     - Alleen virtuele machines van dezelfde grootte kunnen deel uitmaken van een beschikbaarheidsset.

1. U ook informatie toevoegen over het doelnetwerk, subnet en IP-adres dat is toegewezen aan de Azure VM.
1. In **schijven**u het besturingssysteem en de gegevensschijven op de VM zien die worden gerepliceerd.

### <a name="configure-networks-and-ip-addresses"></a>Netwerken en IP-adressen configureren

U het doel-IP-adres instellen:

- Als u geen adres opgeeft, wordt dhcp gebruikt de mislukte over-vm.
- Als u een adres instelt dat niet beschikbaar is bij failover, werkt de failover niet.
- Als het adres beschikbaar is in het failovernetwerk van de test, u hetzelfde doel-IP-adres gebruiken voor een testfailover.

Het aantal netwerkadapters wordt als volgt bepaald door de grootte die u opgeeft voor de virtuele doelmachine:

- Als het aantal netwerkadapters op de virtuele bronmachine kleiner is dan of gelijk is aan het aantal adapters dat is toegestaan voor de grootte van de doel-VM, heeft het doel hetzelfde aantal adapters als de bron.
- Als het aantal adapters voor de virtuele bronmachine groter is dan het aantal dat is toegestaan voor de grootte van de doel-VM, wordt het maximum van de doelgrootte gebruikt. Als een virtuele bronmachine bijvoorbeeld twee netwerkadapters heeft en de grootte van de doel-VM vier ondersteunt, heeft de virtuele doelmachine twee adapters. Als de bron-VM twee adapters heeft, maar de doelgrootte slechts één ondersteunt, heeft de doel-VM slechts één adapter.
- Als de virtuele machine meerdere netwerkadapters heeft, maken ze allemaal verbinding met hetzelfde netwerk. De eerste adapter die in de lijst wordt weergegeven, wordt ook de standaardnetwerkadapter in de virtuele Azure-machine.

### <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

Klanten van Microsoft Software Assurance kunnen Azure Hybrid Benefit gebruiken om te besparen op licentiekosten voor Windows Server-computers die zijn gemigreerd naar Azure. Het voordeel is ook van toepassing op Azure disaster recovery. Als u in aanmerking komt, u het voordeel toewijzen aan de virtuele machine die Site Recovery maakt als er een failover is.

1. Ga naar de **computer- en netwerkeigenschappen** van de gerepliceerde virtuele machine.
1. Antwoord wanneer u wordt gevraagd of u een Windows Server-licentie hebt waardoor u in aanmerking komt voor Azure Hybrid Benefit.
1. Controleer of u een in aanmerking komende Windows Server-licentie hebt met Software Assurance die u gebruiken om het voordeel toe te passen op de VM die bij failover wordt gemaakt.
1. Sla de instellingen voor de gerepliceerde virtuele machine op.

[Meer informatie](https://azure.microsoft.com/pricing/hybrid-benefit/) over Azure Hybrid Benefit.

## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine een beveiligde status heeft bereikt, probeert u een [failover](site-recovery-failover.md) om te controleren of uw toepassing wordt weergegeven in Azure.

- [Meer informatie](site-recovery-manage-registration-and-protection.md) over het schoonmaken van registratie- en beveiligingsinstellingen om replicatie uit te schakelen.
- [Meer informatie](vmware-azure-disaster-recovery-powershell.md) over het automatiseren van replicatie voor uw virtuele machines met PowerShell.
