---
title: Fail over VMware VM's naar Azure met Site Recovery
description: Meer informatie over het mislukken van VMware VM's naar Azure in Azure Site Recovery
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: 8501bb1a998eb08984a118bfa5d52d1e3f3e4f84
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498079"
---
# <a name="fail-over--vmware-vms"></a>Mislukt boven VMware VM's

In dit artikel wordt beschreven hoe u falen over een on-premises virtuele vmware-virtuele machine (VM) naar Azure met [Azure Site Recovery](site-recovery-overview.md).

Dit is de vijfde zelfstudie in een reeks die laat zien hoe u disaster recovery instellen op Azure voor on-premises machines.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Controleer of de VMware VM-eigenschappen voldoen aan azure-vereisten.
> * Fail over specifieke VM's naar Azure.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario. Ze maken waar mogelijk gebruik van standaardopties en geven niet alle mogelijke instellingen en paden weer. Zie Fail [over VM's en fysieke servers](site-recovery-failover.md)als u meer wilt weten over failover.

[Meer informatie over](failover-failback-overview.md#types-of-failover) verschillende soorten failover. Als u meerdere VM's in een herstelplan wilt mislukken, bekijkt u [dit artikel](site-recovery-failover.md).

## <a name="before-you-start"></a>Voordat u begint

Voltooi de vorige tutorials:

1. Zorg ervoor dat u Azure hebt [ingesteld](tutorial-prepare-azure.md) voor on-premises noodherstel van Vm's vMware, Hyper-V VM's en fysieke machines in Azure.
2. Bereid uw on-premises [VMware-omgeving](vmware-azure-tutorial-prepare-on-premises.md) voor op herstel na noodgevallen. 
3. Noodherstel instellen voor [VMware VM's.](vmware-azure-tutorial.md)
4. Voer een [noodhersteloefening](tutorial-dr-drill-azure.md) uit om ervoor te zorgen dat alles werkt zoals verwacht.

## <a name="verify-vm-properties"></a>VM-eigenschappen verifiëren

Controleer voordat u een failover uitvoert de VM-eigenschappen om te controleren of de VM's voldoen aan [Azure-vereisten.](vmware-physical-azure-support-matrix.md#replicated-machines)

De eigenschappen als volgt verifiëren:

1. Selecteer **in Beveiligde items** **gerepliceerde items**en selecteer vervolgens de vm die u wilt verifiëren.

2. In het deelvenster **Gerepliceerd item** bevindt zich een overzicht van VM-informatie, status, en de laatste beschikbare herstelpunten. Selecteer **Eigenschappen** om meer details weer te geven.

3. In **Compute en Network**u deze eigenschappen zo nodig wijzigen:
    * Azure-naam
    * Resourcegroep
    * Doelgrootte
    * [Beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md)
    * Beheerde schijfinstellingen

4. U netwerkinstellingen bekijken en wijzigen, waaronder:

    * Het netwerk en subnet waarin de Azure VM zich na failover bevindt.
    * Het IP-adres dat eraan wordt toegewezen.

5. In **Schijven** ziet u informatie over het besturingssysteem en de gegevensschijven van de VM.

## <a name="run-a-failover-to-azure"></a>Een failover naar Azure uitvoeren

1. Selecteer **in gerepliceerde items instellingen** > **Replicated items**de VM die u wilt mislukken en selecteer **Vervolgens Failover**.
2. Selecteer **in Failover**een **herstelpunt** om niet naar over te gaan. U kunt een van de volgende opties gebruiken:
   * **Laatste**: met deze optie worden eerst alle gegevens naar Site Recovery verzonden gegevens verwerkt. Het biedt de laagste Herstelpuntdoelstelling (RPO), omdat de Azure VM die is gemaakt na failover alle gegevens bevat die zijn gerepliceerd naar Siteherstel toen de failover werd geactiveerd.
   * **Laatste verwerkt:** Deze optie mislukt de VM naar het laatste herstelpunt dat door Site Recovery is verwerkt. Deze optie biedt een lage RTO (Hersteltijddoelstelling) omdat er geen tijd wordt besteed aan het verwerken van onverwerkte gegevens.
   * **Laatste app-consistent:** Deze optie mislukt de VM naar het nieuwste app-consistente herstelpunt dat is verwerkt door Site Recovery.
   * **Aangepast:** met deze optie u een herstelpunt opgeven.

3. Selecteer **De machine afsluiten voordat u failover begint** om te proberen bron-VM's af te sluiten voordat de failover wordt geactiveerd. Failover blijft zelfs als de shutdown mislukt. U de failovervoortgang volgen op de pagina **Vacatures.**

In sommige scenario's vereist failover extra verwerking die ongeveer 8 tot 10 minuten in beslag neemt. U langere testfailovertijden opmerken voor:

* VMware VM's met een Mobility-serviceversie ouder dan 9.8.
* Fysieke servers.
* VMware Linux VM's.
* Hyper-V VM's beschermd als fysieke servers.
* VMware VM's die de DHCP-service niet hebben ingeschakeld.
* VMware VM's die niet de volgende boot drivers hebben: storvsc, vmbus, storflt, intelide, atapi.

> [!WARNING]
> Annuleer een mislukte mislukte uitvoering niet. De VM-replicatie wordt gestopt voordat de failover is gestart. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.

## <a name="connect-to-failed-over-vm"></a>Verbinding maken met mislukte VM

1. Als u na failover verbinding wilt maken met Azure VM's met Behulp van Extern bureaublad-protocol (RDP) en Secure Shell (SSH), controleert u of aan de vereisten is voldaan]((ailover-failback-overview.md#connect-to-azure-after-failover).
2. Ga na failover naar de VM en valideer door er verbinding mee te [maken.](../virtual-machines/windows/connect-logon.md)
3. Gebruik **Herstelpunt wijzigen** als u een ander herstelpunt wilt gebruiken na een failover. Nadat je de failover hebt begaan in de volgende stap, is deze optie niet meer beschikbaar.
4. Na validatie selecteert u **Commit** om het herstelpunt van de VM na failover af te ronden.
5. Nadat je je hebt vastgezet, worden alle andere beschikbare herstelpunten verwijderd. Met deze stap wordt de failover voltooid.

>[!TIP]
> Als u verbindingsproblemen ondervindt na een failover, volgt u de [handleiding voor probleemoplossing.](site-recovery-failover-to-azure-troubleshoot.md)

## <a name="next-steps"></a>Volgende stappen

Na failover u de Azure VM's opnieuw beveiligen voor on-premises. Nadat de VM's opnieuw zijn beveiligd en repliceren naar de on-premises site, u vervolgens niet meer terugvallen vanuit Azure wanneer u er klaar voor bent.

> [!div class="nextstepaction"]
> [Azure VM's](vmware-azure-reprotect.md)
> opnieuw beveiligen[Vanuit Azure](vmware-azure-failback.md)
