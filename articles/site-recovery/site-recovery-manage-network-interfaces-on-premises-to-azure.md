---
title: Netwerkadapters beheren voor on-premises herstel na noodgevallen met Azure Site Recovery
description: Beschrijft hoe u netwerkinterfaces beheert voor on-premises disaster recovery naar Azure met Azure Site Recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954592"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>VM-netwerkinterfaces beheren voor on-premises noodherstel naar Azure

Een virtuele machine (VM) in Azure moet ten minste één netwerkinterface hebben. Er kunnen evenveel netwerkinterfaces aan zijn gekoppeld als de VM-grootte ondersteunt.

Standaard wordt de eerste netwerkinterface die is gekoppeld aan een virtuele Azure-machine gedefinieerd als de primaire netwerkinterface. Alle andere netwerkinterfaces in de virtuele machine zijn secundaire netwerkinterfaces. Ook wordt standaard al het uitgaande verkeer van de virtuele machine verzonden naar het IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface.

In een on-premises omgeving kunnen virtuele machines of servers meerdere netwerkinterfaces hebben voor verschillende netwerken binnen de omgeving. Verschillende netwerken worden meestal gebruikt voor het uitvoeren van specifieke bewerkingen, zoals upgrades, onderhoud en internettoegang. Wanneer u vanuit een on-premises omgeving migreert of niet naar Azure migreert, moet u er rekening mee houden dat netwerkinterfaces in dezelfde virtuele machine allemaal zijn verbonden met hetzelfde virtuele netwerk.

Azure Site Recovery maakt standaard evenveel netwerkinterfaces op een virtuele Azure-machine als verbonden zijn met de on-premises server. U voorkomen dat redundante netwerkinterfaces worden gemaakt tijdens migratie of failover door de netwerkinterface-instellingen te bewerken onder de instellingen voor de gerepliceerde virtuele machine.

## <a name="select-the-target-network"></a>Het doelnetwerk selecteren

Voor VMware- en fysieke machines en voor virtuele machines van Hyper-V (zonder Virtual Machine Manager van het Systeemcentrum) u het doelnetwerk voor afzonderlijke virtuele machines opgeven. Voor virtuele hyper-v-machines die worden beheerd met Virtual Machine Manager, gebruikt u [netwerktoewijzing](site-recovery-network-mapping.md) om VM-netwerken in kaart te brengen op een bronvirtual machinebeheerserver en Azure-netwerken te targeten.

1. Selecteer **onder Gerepliceerde items** in een kluis van Recovery Services een gerepliceerd item om toegang te krijgen tot de instellingen voor dat gerepliceerde item.

2. Selecteer het tabblad **Gegevensberekenen en netwerk** om toegang te krijgen tot de netwerkinstellingen voor het gerepliceerde item.

3. Kies **onder Netwerkeigenschappen**een virtueel netwerk in de lijst met beschikbare netwerkinterfaces.

    ![Netwerkinstellingen](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Het wijzigen van het doelnetwerk is van invloed op alle netwerkinterfaces voor die specifieke virtuele machine.

Voor Virtual Machine Manager-clouds is het wijzigen van netwerktoewijzing van invloed op alle virtuele machines en hun netwerkinterfaces.

## <a name="select-the-target-interface-type"></a>Het doelinterfacetype selecteren

Onder het gedeelte **Netwerkinterfaces** van het deelvenster **Gegevensbekeeren en netwerk** u de instellingen van de netwerkinterface weergeven en bewerken. U ook het doelnetwerkinterfacetype opgeven.

- Voor failover is **een primaire** netwerkinterface vereist.
- Alle andere geselecteerde netwerkinterfaces, indien van toepassing, zijn **secundaire** netwerkinterfaces.
- Selecteer **Niet gebruiken** om een netwerkinterface uit te sluiten van het maken bij failover.

Wanneer u replicatie inschakelt, selecteert Site Recovery standaard alle gedetecteerde netwerkinterfaces op de on-premises server. Het merkt één als **Primair** en alle anderen als **Secundair**. Eventuele volgende interfaces die op de on-premises server zijn toegevoegd, zijn gemarkeerd **Als u niet** standaard wordt gebruikt. Wanneer u meer netwerkinterfaces toevoegt, moet u ervoor zorgen dat de juiste doelgrootte van de virtuele Azure-machine is geselecteerd om geschikt te zijn voor alle vereiste netwerkinterfaces.

## <a name="modify-network-interface-settings"></a>Instellingen voor netwerkinterface wijzigen

U het subnet en het IP-adres wijzigen voor de netwerkinterfaces van een gerepliceerd item. Als een IP-adres niet is opgegeven, wijst Site Recovery het volgende beschikbare IP-adres van het subnet toe aan de netwerkinterface bij failover.

1. Selecteer een beschikbare netwerkinterface om de netwerkinterface-instellingen te openen.

2. Kies het gewenste subnet in de lijst met beschikbare subnetten.

3. Voer het gewenste IP-adres in (indien vereist).

    ![Netwerkinterface-instellingen](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Selecteer **OK** om de bewerking af te ronden en terug te keren naar het deelvenster **Gegevensbekabel en netwerk.**

5. Herhaal stap 1-4 voor andere netwerkinterfaces.

6. Selecteer **Opslaan** om alle wijzigingen op te slaan.

## <a name="next-steps"></a>Volgende stappen
  [Meer informatie](../virtual-network/virtual-network-network-interface-vm.md) over netwerkinterfaces voor virtuele Azure-machines.
