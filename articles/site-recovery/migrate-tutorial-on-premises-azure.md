---
title: On-premises machines migreren met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u on-premises VM en fysieke machines naar Azure migreert met behulp van Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: ccf83bacedb667e52e9865b6d451641faa0ac414
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86131176"
---
# <a name="migrate-on-premises-machines-to-azure"></a>On-premises machines migreren naar Azure


In dit artikel wordt beschreven hoe u on-premises machines naar Azure migreert met behulp van [Azure Site Recovery](site-recovery-overview.md). 

> [!TIP]
> U moet nu Azure Migrate in plaats van de Azure Site Recovery-service gebruiken om on-premises machines naar Azure te migreren. [Meer informatie](../migrate/migrate-services-overview.md).


Deze zelfstudie laat u zien hoe u on-premises VM's en fysieke servers naar Azure migreert met Site Recovery. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * De bron- en doelomgeving instellen voor migratie
> * Een replicatiebeleid instellen
> * Replicatie inschakelen
> * Een testfailover uitvoeren om te controleren of alles goed werkt
> * Een eenmalige failover uitvoeren naar Azure


> [!TIP]
> U kunt nu on-premises servers migreren naar Azure met behulp van de Azure Migrate-service. [Meer informatie](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Voordat u begint

Apparaten die zijn geëxporteerd door geparavirtualiseerde stuurprogramma's worden niet ondersteund.


## <a name="prepare-azure-and-on-premises"></a>Azure en on-premises omgeving voorbereiden

1. Bereid Azure voor als beschreven in [dit artikel](tutorial-prepare-azure.md). Hoewel in dit artikel de voorbereidende stappen voor herstel na noodgeval worden beschreven, zijn de stappen ook bruikbaar voor migratie.
2. On-premises [VMware](vmware-azure-tutorial-prepare-on-premises.md)- of [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-servers voorbereiden. Als u fysieke machines migreert, hoeft u niets voor te bereiden. Controleer slechts de [ondersteuningsmatrix](vmware-physical-azure-support-matrix.md).


## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

Selecteer wat u wilt repliceren en waarnaar u wilt repliceren.
1. Klik op **Recovery Services-kluizen** > kluis.
2. Klik in het resourcemenu op **Site Recovery** > **Infra structuur voorbereiden** > **Beveiligingsdoel**.
3. Selecteer bij **Beveiligingsdoel** wat u wilt migreren.
    - **VMware**: selecteer **Naar Azure** > **Ja, met VMWare vSphere Hypervisor**.
    - **Fysieke machine**: selecteer **Naar Azure** > **Niet gevirtualiseerde/overige**.
    - **Hyper-V**: selecteer **Naar Azure** > **Ja, met Hyper-V**. Als Hyper-V-machines worden beheerd door VMM, selecteert u **Ja**.


## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

**Scenario** | **Details**
--- | --- 
VMware | Stel de [bronomgeving](vmware-azure-set-up-source.md) en de [configuratieserver](vmware-azure-deploy-configuration-server.md) in.
Fysieke machine | [Stel](physical-azure-set-up-source.md) de bronomgeving en de configuratieserver in.
Hyper-V | [Bronomgeving](hyper-v-azure-tutorial.md#set-up-the-source-environment) instellen<br/><br/> Stel de [bronomgeving](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) in voor Hyper-V dat is geïmplementeerd met System Center VMM.

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Klik op **Infrastructuur voorbereiden** > **Doel** en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het Resource Manager-implementatiemodel op.
3. De Azure-resources worden door Site Recovery gecontroleerd.
    - Als u virtuele VMware-machines of fysieke servers migreert, wordt door Site Recovery gecontroleerd of u een Azure-netwerk hebt waarin de virtuele Azure-machines zich bevinden wanneer ze na een failover worden gemaakt.
    - Als u virtuele Hyper-V-machines migreert, wordt door Site Recovery gecontroleerd of er een compatibel Azure-opslagaccount en -netwerk is.
4. Als u virtuele Hyper-V-machines migreert die worden beheerd door System Center VMM, stelt u [netwerktoewijzing](hyper-v-vmm-azure-tutorial.md#configure-network-mapping) in.

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

**Scenario** | **Details**
--- | --- 
VMware | Stel een [replicatiebeleid](vmware-azure-set-up-replication.md) voor VMware-VM's in.
Fysieke machine | Stel een [replicatiebeleid](physical-azure-disaster-recovery.md#create-a-replication-policy) in voor fysieke servers.
Hyper-V | [Replicatiebeleid](hyper-v-azure-tutorial.md#set-up-a-replication-policy) instellen<br/><br/> Stel een [replicatiebeleid](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) in voor Hyper-V dat is geïmplementeerd met System Center VMM.

## <a name="enable-replication"></a>Replicatie inschakelen

**Scenario** | **Details**
--- | --- 
VMware | [Replicatie inschakelen](vmware-azure-enable-replication.md) voor VMware VM's.
Fysieke machine | [Schakel replicatie in](physical-azure-disaster-recovery.md#enable-replication) voor fysieke machines.
Hyper-V | [Replicatie inschakelen](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Schakel replicatie in](hyper-v-vmm-azure-tutorial.md#enable-replication) voor Hyper-V dat is geïmplementeerd met System Center VMM.


## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren

Voer een [testfailover](tutorial-dr-drill-azure.md) naar Azure uit om te controleren of alles goed werkt.


## <a name="migrate-to-azure"></a>Migreren naar Azure

Een failover uitvoeren voor de machines die u wilt migreren.

1. Klik in **Instellingen** > **Gerepliceerde items** op de machine > **Failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. Selecteer het meest recente herstelpunt.
3. De instelling voor de coderingssleutel is niet relevant in dit scenario.
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery zal proberen om virtuele machines af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover volgen op de pagina **Taken**.
5. Controleer of de virtuele Azure-machine in Azure wordt weergegeven zoals verwacht.
6. Klik in **Gerepliceerde items** met de rechtermuisknop op de virtuele machine > **Migratie voltooien**. Er gebeurt nu het volgende:

   - Het migratieproces wordt voltooid, de replicatie voor de virtuele on-premises machine wordt gestopt en Site Recovery-facturering voor de virtuele machine wordt gestopt.
   - Met deze stap worden de replicatiegegevens opgeschoond. De gemigreerde VM's worden niet verwijderd.

     ![Migratie voltooien](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Annuleer nooit een failover die in uitvoering is**: De VM-replicatie wordt gestopt voordat de failover start. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.

In sommige scenario's vereist de failover extra verwerking die circa acht tot tien minuten duurt. U zou langere failover-tijden kunnen waarnemen voor fysieke servers, VMware Linux-computers, VMware VM's waarop de DHCP-service niet is ingeschakeld, en VMware VM's die niet de volgende opstartstuurprogramma’s hebben: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Na de migratie

Wanneer machines zijn gemigreerd naar Azure, zijn er nog een aantal stappen die u moet voltooien.

Enkele stappen kunnen worden geautomatiseerd als onderdeel van het migratieproces met behulp van de ingebouwde automation-scripts voor [herstelplannen](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Stappen na de migratie in Azure

- Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties. 
- Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
- De [Azure VM-agent](../virtual-machines/extensions/agent-windows.md) beheert de interactie van de VM met de Azure-Infrastructuurcontroller. Dit is vereist voor sommige Azure-services, zoals Azure Backup, Azure Site Recovery en Azure-beveiliging.
    - Als u VMware-machines en fysieke servers wilt migreren, installeert het installatieprogramma van de Mobility-service de beschikbaar Azure VM-agent op Windows-machines. Voor Linux VM‘s raden we u aan om de agent te installeren na failover.
    - Als u Azure VM's migreert naar een secundaire regio, moet de Azure VM-agent vóór de migratie worden ingericht op de virtuele machine.
    - Als u Hyper-V VM's naar Azure migreert, installeert u de Azure VM-agent na de migratie op de Azure VM.
- Verwijder eventuele Site Recovery-providers/agenten handmatig van de VM. Als u virtuele VMware-machines of fysieke servers migreert, verwijdert u de Mobility-service van de VM.
- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](azure-to-azure-quickstart.md).
- Voor betere beveiliging:
    - De toegang van binnenkomend verkeer vergrendelen en beperken met [Just-in-time-beheer](../security-center/security-center-just-in-time.md) van Azure Security Center
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](../virtual-network/security-overview.md).
    - Implementeer [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) om uw resourcegebruik en uitgaven te bewaken.

### <a name="post-migration-steps-on-premises"></a>Stappen om na de migratie on-premises uit te voeren

- Leid appverkeer via de app die wordt uitgevoerd op het gemigreerde Azure VM-exemplaar.
- Verwijder de on-premises VM's uit uw lokale VM-inventaris.
- Verwijder de on-premises VM's uit de lokale back-ups.
- Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's.




## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u on-premises virtuele machines naar virtuele Azure-machines gemigreerd. Stel nu

> [!div class="nextstepaction"]
> [herstel na noodgeval in](azure-to-azure-replicate-after-migration.md) voor een secundaire Azure-regio voor de Azure-VM's.

  
