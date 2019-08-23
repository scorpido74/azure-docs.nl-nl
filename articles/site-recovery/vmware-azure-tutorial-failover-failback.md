---
title: Failover- en failback-overschakeling uitvoeren van VMware-VM’s en fysieke servers tijdens herstel na noodgeval in Azure met Site Recovery | Microsoft Docs
description: Meer informatie over het uitvoeren van een failover van virtuele VMware-machines en fysieke servers naar Azure en het uitvoeren van een failback naar de on-premises site tijdens nood herstel naar Azure met behulp van Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852193e137eab10d1e46c5ba6ae6636d530095be
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972196"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Failover en failback van virtuele VMware-machines

In dit artikel wordt beschreven hoe u een failover van een on-premises virtuele VMware-machine (VM) naar [Azure site Recovery](site-recovery-overview.md).

Dit is de vijfde zelf studie in een serie die laat zien hoe u herstel na nood gevallen naar Azure instelt voor on-premises machines.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer of de eigenschappen van de VMware-VM voldoen aan de vereisten van Azure.
> * Een failover uitvoeren naar Azure.

> [!NOTE]
> Zelf studies laten u de eenvoudigste implementatie traject voor een scenario zien. Ze gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Zie failover van [vm's en fysieke servers](site-recovery-failover.md)voor meer informatie over failover in detail.

## <a name="before-you-start"></a>Voordat u begint

Voltooi de vorige zelf studies:

1. Zorg ervoor dat u [Azure hebt ingesteld](tutorial-prepare-azure.md) voor on-premises herstel na nood gevallen van virtuele VMware-machines, Hyper-V-vm's en fysieke machines naar Azure.
2. Bereid uw on-premises [VMware](vmware-azure-tutorial-prepare-on-premises.md) [-of Hyper-V-](hyper-v-prepare-on-premises-tutorial.md) omgeving voor op herstel na nood geval. Als u herstel na nood gevallen instelt voor fysieke servers, raadpleegt u de ondersteunings [matrix](vmware-physical-secondary-support-matrix.md).
3. Herstel na nood gevallen instellen voor [VMware-vm's](vmware-azure-tutorial.md), [Hyper-V-vm's](hyper-v-azure-tutorial.md)of [fysieke machines](physical-azure-disaster-recovery.md).
4. Voer een [nood herstel analyse](tutorial-dr-drill-azure.md) uit om te controleren of alles werkt zoals verwacht.

## <a name="failover-and-failback"></a>Failover en failback

Failover en failback bestaat uit vier fasen:

1. **Failover naar Azure:** Als uw on-premises primaire site uitvalt, worden er geen computers meer naar Azure verzonden. Na een failover worden virtuele Azure-machines gemaakt op basis van gerepliceerde gegevens.
2. **Azure-Vm's opnieuw beveiligen:** Beveilig in azure de virtuele Azure-machines opnieuw zodat ze terugrepliceren naar on-premises virtuele VMware-machines. De on-premises VM is uitgeschakeld tijdens het opnieuw beveiligen, zodat gegevens consistentie wordt gegarandeerd.
3. **Failover naar on-premises:** Als uw on-premises site actief is, voert u een failover uit om een failback uit te voeren van Azure.
4. **On-premises Vm's opnieuw beveiligen:** Nadat de gegevens zijn hersteld, moet u de on-premises virtuele machines waarop u bent teruggevallen opnieuw beveiligen, zodat ze repliceren naar Azure.

## <a name="verify-vm-properties"></a>VM-eigenschappen verifiëren

Voordat u een failover uitvoert, controleert u de eigenschappen van de virtuele machine om ervoor te zorgen dat de virtuele machines voldoen aan de [vereisten van Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Controleer de eigenschappen als volgt:

1. Selecteer in **beveiligde items** **gerepliceerde items**en selecteer vervolgens de virtuele machine die u wilt controleren.

2. In het deelvenster **Gerepliceerd item** bevindt zich een overzicht van VM-informatie, status, en de laatste beschikbare herstelpunten. Selecteer **Eigenschappen** om meer details weer te geven.

3. In **Compute en Network**kunt u deze eigenschappen naar behoefte wijzigen:
    * Azure-naam
    * Resource group
    * Doelgrootte
    * [Beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md)
    * Instellingen voor beheerde schijven

4. U kunt de netwerk instellingen weer geven en wijzigen, zoals:

    * Het netwerk en subnet waarin de virtuele Azure-machine na een failover wordt gevonden.
    * Het IP-adres dat wordt toegewezen.

5. In **Schijven** ziet u informatie over het besturingssysteem en de gegevensschijven van de VM.

## <a name="run-a-failover-to-azure"></a>Een failover naar Azure uitvoeren

1. Selecteer in **instellingen** > **gerepliceerde items**de virtuele machine waarvoor u een failover wilt uitvoeren en selecteer vervolgens **failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. U kunt een van de volgende opties gebruiken:
   * **Laatste**: Met deze optie worden eerst alle gegevens verwerkt die naar Site Recovery zijn verzonden. Het biedt het laagste beoogde herstel punt (RPO), omdat de virtuele Azure-machine die is gemaakt na een failover, alle gegevens bevat die naar Site Recovery zijn gerepliceerd toen de failover werd geactiveerd.
   * **Laatst verwerkt**: Met deze optie mislukt de VM naar het laatste herstel punt dat is verwerkt door Site Recovery. Deze optie biedt een lage RTO (herstel tijd doelstelling), omdat er geen tijd wordt besteed aan het verwerken van niet-verwerkte gegevens.
   * **Laatste toepassingsconsistente punt**: Met deze optie wordt de virtuele machine niet meer dan het nieuwste toepassings consistente herstel punt dat is verwerkt door Site Recovery.
   * **Aangepast**: Met deze optie kunt u een herstel punt opgeven.

3. Selecteer **computer afsluiten voordat de failover wordt gestart** om te proberen de bron-vm's af te sluiten voordat de failover wordt geactiveerd. Failover wordt voortgezet, zelfs als het afsluiten mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.

In sommige gevallen vereist failover extra verwerking die ongeveer 8 tot 10 minuten duurt. U kunt langere failover-tijden voor het testen waarnemen:

* VMware-Vm's met een Mobility Service-versie ouder dan 9,8.
* Fysieke servers.
* Virtuele VMware Linux-machines.
* Virtuele Hyper-V-machines die worden beveiligd als fysieke servers.
* VMware-Vm's waarop de DHCP-service niet is ingeschakeld.
* VMware-Vm's die niet de volgende opstart Stuur Programma's hebben: storvsc, vmbus, storflt, Intelide, ATAPI.

> [!WARNING]
> Een failover wordt niet geannuleerd. De VM-replicatie wordt gestopt voordat de failover is gestart. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.

## <a name="connect-to-failed-over-vm"></a>Verbinding maken met mislukte VM

1. Als u na een failover verbinding wilt maken met virtuele Azure-machines met behulp van Remote Desktop Protocol (RDP) en Secure Shell (SSH), [controleert u of aan de vereisten is voldaan](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Ga na een failover naar de virtuele machine en valideer deze door [verbinding te maken](../virtual-machines/windows/connect-logon.md) .
3. **Herstel punt voor wijzigingen** gebruiken als u na een failover een ander herstel punt wilt gebruiken. Nadat u de failover in de volgende stap hebt doorgevoerd, is deze optie niet meer beschikbaar.
4. Na validatie selecteert u **door voeren** om het herstel punt van de virtuele machine na een failover te volt ooien.
5. Nadat u de wijzigingen hebt doorgevoerd, worden alle andere beschik bare herstel punten verwijderd. Met deze stap wordt de failover voltooid.

>[!TIP]
> Als u na de failover problemen ondervindt met de verbinding, volgt u de [hand leiding](site-recovery-failover-to-azure-troubleshoot.md)voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Na een failover kunt u de Azure-Vm's opnieuw beveiligen naar on-premises. Nadat de Vm's opnieuw zijn beveiligd en repliceren naar de on-premises site, kunt u een failback uitvoeren van Azure wanneer u klaar bent.

> [!div class="nextstepaction"]
> [](vmware-azure-reprotect.md)
> [Een failback van Azure-vm's van Azure opnieuw](vmware-azure-failback.md) beveiligen
