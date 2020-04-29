---
title: On-premises machines migreren met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u on-premises VM en fysieke machines naar Azure migreert met behulp van Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: b978190776aee3c89d3beadde76d20c4327b012f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80388913"
---
# <a name="migrate-on-premises-machines-to-azure"></a>On-premises machines migreren naar Azure


In dit artikel wordt beschreven hoe u on-premises machines naar Azure migreert met behulp van de [Azure site Recovery](site-recovery-overview.md). 

> [!TIP]
> U moet nu Azure Migrate gebruiken om on-premises machines te migreren naar Azure, in plaats van de Azure Site Recovery-service. [Meer informatie](../migrate/migrate-services-overview.md).


Deze zelfstudie laat u zien hoe u on-premises VM's en fysieke servers naar Azure migreert met Site Recovery. Procedures voor:

> [!div class="checklist"]
> * De bron-en doel omgeving voor migratie instellen
> * Een replicatiebeleid instellen
> * Replicatie inschakelen
> * Een testfailover uitvoeren om te controleren of alles goed werkt
> * Een eenmalige failover uitvoeren naar Azure


> [!TIP]
> U kunt nu on-premises servers migreren naar Azure met behulp van de Azure Migrate-service. [Meer informatie](../migrate/migrate-services-overview.md)

## <a name="before-you-start"></a>Voordat u begint

Apparaten die zijn geëxporteerd door geparavirtualiseerde stuurprogramma's worden niet ondersteund.


## <a name="prepare-azure-and-on-premises"></a>Azure en on-premises voorbereiden

1. Maak Azure, zoals beschreven in [dit artikel](tutorial-prepare-azure.md). Hoewel in dit artikel de voorbereidende stappen voor herstel na nood gevallen worden beschreven, zijn de stappen ook geldig voor migratie.
2. On-premises [VMware](vmware-azure-tutorial-prepare-on-premises.md)- of [Hyper-V](hyper-v-prepare-on-premises-tutorial.md)-servers voorbereiden. Als u fysieke machines migreert, hoeft u niets voor te bereiden. U hoeft alleen de [ondersteunings matrix](vmware-physical-azure-support-matrix.md)te controleren.


## <a name="select-a-protection-goal"></a>Een beveiligingsdoel selecteren

Selecteer wat u wilt repliceren en waarnaar u wilt repliceren.
1. Klik op **Recovery Services-kluizen** > kluis.
2. Klik in het menu resource op **site Recovery** > doel**infrastructuur** > **beveiliging**voorbereiden.
3. Selecteer bij **Beveiligingsdoel** wat u wilt migreren.
    - **VMware**: selecteer **naar Azure** > **Ja, met VMWare vSphere Hypervisor**.
    - **Fysieke machine**: selecteer **Naar Azure** > **Niet gevirtualiseerde/overige**.
    - **Hyper-V**: selecteer **Naar Azure** > **Ja, met Hyper-V**. Als Hyper-V-machines worden beheerd door VMM, selecteert u **Ja**.


## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

**Scenario** | **Nadere**
--- | --- 
VMware | Stel de [bron omgeving](vmware-azure-set-up-source.md)in en stel de [Configuratie server](vmware-azure-deploy-configuration-server.md)in.
Fysieke machine | [Stel](physical-azure-set-up-source.md) de bron omgeving en configuratie server in.
Hyper-V | De [bron omgeving](hyper-v-azure-tutorial.md#set-up-the-source-environment) instellen<br/><br/> Stel de [bron omgeving](hyper-v-vmm-azure-tutorial.md#set-up-the-source-environment) voor Hyper-V in die is geïmplementeerd met System Center VMM.

## <a name="set-up-the-target-environment"></a>De doelomgeving instellen

Selecteer en controleer doelbronnen.

1. Klik op **infra structuur** > voorbereiden**doel**en selecteer het Azure-abonnement dat u wilt gebruiken.
2. Geef het Resource Manager-implementatiemodel op.
3. Site Recovery controleert de Azure-resources.
    - Als u virtuele VMware-machines of fysieke servers migreert, wordt door Site Recovery gecontroleerd of u een Azure-netwerk hebt waarin de virtuele Azure-machines zich bevinden wanneer ze na een failover worden gemaakt.
    - Als u virtuele Hyper-V-machines migreert, wordt door Site Recovery gecontroleerd of er een compatibel Azure-opslag account en netwerk is.
4. Als u virtuele Hyper-V-machines wilt migreren die worden beheerd door System Center VMM, stelt u [netwerk toewijzing](hyper-v-vmm-azure-tutorial.md#configure-network-mapping)in.

## <a name="set-up-a-replication-policy"></a>Een replicatiebeleid instellen

**Scenario** | **Nadere**
--- | --- 
VMware | Een [replicatie beleid](vmware-azure-set-up-replication.md) instellen voor VMware-vm's.
Fysieke machine | Een [replicatie beleid](physical-azure-disaster-recovery.md#create-a-replication-policy) instellen voor fysieke machines.
Hyper-V | Een [replicatie beleid](hyper-v-azure-tutorial.md#set-up-a-replication-policy) instellen<br/><br/> Een [replicatie beleid](hyper-v-vmm-azure-tutorial.md#set-up-a-replication-policy) instellen voor Hyper-V dat is geïmplementeerd met System Center VMM.

## <a name="enable-replication"></a>Replicatie inschakelen

**Scenario** | **Nadere**
--- | --- 
VMware | [Schakel replicatie in](vmware-azure-enable-replication.md) voor VMware-vm's.
Fysieke machine | [Schakel replicatie in](physical-azure-disaster-recovery.md#enable-replication) voor fysieke machines.
Hyper-V | [Replicatie inschakelen](hyper-v-azure-tutorial.md#enable-replication)<br/><br/> [Schakel replicatie](hyper-v-vmm-azure-tutorial.md#enable-replication) in voor Hyper-V die is geïmplementeerd met System Center VMM.


## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren

Voer een [testfailover](tutorial-dr-drill-azure.md) naar Azure uit om te controleren of alles goed werkt.


## <a name="migrate-to-azure"></a>Migreren naar Azure

Een failover uitvoeren voor de machines die u wilt migreren.

1. Klik in **instellingen** > **gerepliceerde items** op de machine > **failover**.
2. Selecteer in **Failover** een **Herstelpunt** waarnaar u de failover wilt uitvoeren. Selecteer het meest recente herstelpunt.
3. De instelling voor de coderingssleutel is niet relevant in dit scenario.
4. Selecteer **Sluit de computer af voordat de failover wordt gestart**. Site Recovery zal proberen om virtuele machines af te sluiten voordat de failover wordt geactiveerd. De failover wordt voortgezet zelfs als het afsluiten is mislukt. U kunt de voortgang van de failover op de pagina **taken** volgen.
5. Controleer of de virtuele Azure-machine in Azure wordt weergegeven zoals verwacht.
6. Klik in **Gerepliceerde items** met de rechtermuisknop op de virtuele machine > **Migratie voltooien**. Er gebeurt nu het volgende:

   - Hiermee voltooit u het migratie proces, stopt u de replicatie voor de on-premises VM en stopt u Site Recovery facturering voor de virtuele machine.
   - Met deze stap worden de replicatiegegevens opgeschoond. De gemigreerde VM's worden niet verwijderd.

     ![Migratie voltooien](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Annuleer een failover die in voortgang is niet**: voordat de failover wordt gestart, wordt de VM-replicatie gestopt. Als u een failover die in voortgang is annuleert, wordt de failover gestopt, maar de VM wordt niet meer gerepliceerd.

In sommige scenario's vereist de failover extra verwerking die circa acht tot tien minuten duurt. U zou langere failover-tijden kunnen waarnemen voor fysieke servers, VMware Linux-machines, VMware-Vm's waarop de DHCP-service niet is ingeschakeld, en VMware-Vm's die niet de volgende opstart Stuur Programma's hebben: storvsc, vmbus, storflt, Intelide, ATAPI.

## <a name="after-migration"></a>Na de migratie

Wanneer machines zijn gemigreerd naar Azure, zijn er nog een aantal stappen die u moet voltooien.

Enkele stappen kunnen worden geautomatiseerd als onderdeel van het migratieproces met behulp van de ingebouwde automation-scripts voor [herstelplannen](site-recovery-runbook-automation.md)   


### <a name="post-migration-steps-in-azure"></a>Stappen na de migratie in Azure

- Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties. 
- Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
- De [Azure VM-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) beheert de interactie van de VM met de Azure-Infrastructuurcontroller. Dit is vereist voor sommige Azure-services, zoals Azure Backup, Azure Site Recovery en Azure-beveiliging.
    - Als u VMware-machines en fysieke servers wilt migreren, installeert het installatieprogramma van de Mobility-service de beschikbaar Azure VM-agent op Windows-machines. Voor Linux VM‘s raden we u aan om de agent te installeren na failover.
    - Als u virtuele machines van Azure migreert naar een secundaire regio, moet de Azure VM-agent worden ingericht op de VM vóór de migratie.
    - Als u virtuele Hyper-V-machines naar Azure migreert, installeert u de Azure VM-agent op de virtuele Azure-machine na de migratie.
- Verwijder eventuele Site Recovery-providers/agenten handmatig van de VM. Als u virtuele VMware-machines of fysieke servers migreert, verwijdert u de Mobility-service van de VM.
- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](azure-to-azure-quickstart.md).
- Voor betere beveiliging:
    - De toegang van binnenkomend verkeer vergrendelen en beperken met [Just-in-time-beheer]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time) van Azure Security Center
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/ ).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.

### <a name="post-migration-steps-on-premises"></a>Stappen om na de migratie on-premises uit te voeren

- Leid appverkeer via de app die wordt uitgevoerd op het gemigreerde Azure VM-exemplaar.
- Verwijder de on-premises VM's uit uw lokale VM-inventaris.
- Verwijder de on-premises VM's uit de lokale back-ups.
- Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's.




## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u on-premises virtuele machines naar virtuele Azure-machines gemigreerd. Now

> [!div class="nextstepaction"]
> [Stel herstel na nood](azure-to-azure-replicate-after-migration.md) geval in voor een secundaire Azure-regio voor de Azure-vm's.

  
