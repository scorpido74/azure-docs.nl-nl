---
title: VMware VM's zonder amansfunctie migreren Azure Migrate Server Migration
description: Meer informatie over het uitvoeren van een agentloze migratie van VMware VM's met Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 825d6ff16a1f51fa476541ee10fea5f8a1c2972e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78304205"
---
# <a name="migrate-vmware-vms-to-azure-agentless"></a>VMware VM's migreren naar Azure (zonder agent)

In dit artikel ziet u hoe u on-premises Vm's vMware naar Azure migreert, met behulp van agentloze migratie met het hulpprogramma Azure Migrate Server Migration.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en AWS/GCP VM-exemplaren naar Azure bij te houden. De hub biedt Azure Migrate-tools voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden.

Deze zelfstudie is de derde in een reeks die laat zien hoe vmware VM's naar Azure kunnen worden beoordeeld en gemigreerd met Azure Migrate Server Assessment and Migration. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * VM's voorbereiden op migratie.
> * Voeg het hulpprogramma voor migratievan Azure Migration Server toe.
> * Ontdek VM's die u wilt migreren.
> * Begin met het repliceren van VM's.
> * Voer een testmigratie uit om te zien of alles werkt zoals verwacht.
> * Voer een volledige VM-migratie uit.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.

## <a name="migration-methods"></a>Migratiemethoden

U VMware VM's migreren naar Azure met het hulpprogramma Azure Migrate Server Migration. Deze tool biedt een aantal opties voor VMware VM-migratie:

- Migratie met behulp van agentless replicatie. Migreer VM's zonder er iets op te hoeven installeren.
- Migratie met een agent voor replicatie. Installeer een agent op de VM voor replicatie.

Als u wilt beslissen of u agentloze of op agentgebaseerde migratie wilt gebruiken, raadpleegt u deze artikelen:

- [Ontdek hoe](server-migrate-overview.md) agentless migratie werkt en [vergelijk migratiemethoden](server-migrate-overview.md#compare-migration-methods).
- [Lees dit artikel](tutorial-migrate-vmware-agent.md) als u de op agentgebaseerde methode wilt gebruiken.

## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Voer de eerste zelfstudie](tutorial-prepare-vmware.md) in deze reeks uit om Azure en VMware in te stellen voor migratie. Specifiek, in deze tutorial moet je:
    - [Azure voorbereiden op](tutorial-prepare-vmware.md#prepare-azure) migratie.
    - [Bereid de on-premises omgeving](tutorial-prepare-vmware.md#prepare-for-agentless-vmware-migration) voor op migratie.
    
2. We raden u aan vmware VM's te beoordelen met Azure Migrate Server Assessment voordat u ze migreert naar Azure. Voer [de tweede zelfstudie](tutorial-assess-vmware.md) in deze serie in om een beoordeling in te stellen. Als u geen VM's wilt beoordelen, u deze zelfstudie overslaan. Hoewel we u aanraden een beoordeling uit te proberen, maar u hoeft geen beoordeling uit te voeren voordat u een migratie probeert.



## <a name="add-the-azure-migrate-server-migration-tool"></a>Het hulpprogramma voor migratie van Azure-migrerende server toevoegen

Als u de tweede zelfstudie voor het beoordelen van VMware-VM's niet hebt gevolgd, moet u [deze instructies volgen](how-to-add-tool-first-time.md) en een Azure Migrate-project instellen en het hulpprogramma azure migrateservermigratie selecteren. 

Als u de tweede zelfstudie hebt gevolgd en al een Azure Migrate-project hebt ingesteld, voegt u het hulpprogramma Azure Migrate Server Migration als volgt toe:

1. Klik in het Azure Migrate-project op **Overzicht**. 
2. Klik in **Servers voor ontdekken, beoordelen en migreren**op Servers beoordelen en **migreren.**

     ![Servers beoordelen en migreren](./media/tutorial-migrate-vmware/assess-migrate.png)

3. Selecteer in **migratiehulpprogramma's** **Klik hier om een migratiegereedschap toe te voegen wanneer u klaar bent om te migreren.**

    ![Een gereedschap selecteren](./media/tutorial-migrate-vmware/select-migration-tool.png)

4. Selecteer **Azure Migrate: Hulpprogramma Voor servermigratie** > **toevoegen** in de lijst met hulpprogramma's voor hulpprogramma voor hulpprogramma voor hulpprogramma voor het migreren

    ![Hulpprogramma voor de migratie van servers](./media/tutorial-migrate-vmware/server-migration-tool.png)

## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-toestel instellen

Azure Migrate Server Migration voert een lichtgewicht VMware VM-toestel uit. Het toestel voert VM-detectie uit en verzendt VM-metagegevens en prestatiegegevens naar Azure Migrate Server Migration. Hetzelfde toestel wordt ook gebruikt door het Azure Migrate Server Assessment-hulpprogramma.

Als u de tweede zelfstudie hebt gevolgd om VMware-VM's te beoordelen, hebt u het apparaat al ingesteld tijdens die zelfstudie. Als u die zelfstudie niet hebt gevolgd, moet u het apparaat nu instellen. Om dit te doen, u: 

- Download een OVA-sjabloonbestand en importeer het naar vCenter Server.
- Maak het toestel en controleer of het verbinding kan maken met Azure Migrate Server Assessment. 
- Configureer het toestel voor de eerste keer en registreer het met het Azure Migrate-project.

Volg de instructies in [dit artikel](how-to-set-up-appliance-vmware.md) om het apparaat in te stellen.


## <a name="prepare-vms-for-migration"></a>VM's voorbereiden op migratie

Azure Migrate vereist enkele VM-wijzigingen om ervoor te zorgen dat VM's kunnen worden gemigreerd naar Azure.

- Voor sommige besturingssystemen voert Azure Migrate deze wijzigingen automatisch uit. [Meer informatie](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)
- Als u een vm migreert die niet over een van deze besturingssystemen beschikt, volgt u de instructies om de VM voor te bereiden.
- Het is belangrijk om deze wijzigingen aan te brengen voordat u met migratie begint. Als u de vm migreert voordat u de wijziging aanbrengt, wordt de VM mogelijk niet opgestart in Azure.
- Configuratiewijzigingen die u op on-premises VM's aanbrengt, worden gerepliceerd naar Azure nadat replicatie voor de VM is ingeschakeld. Als u ervoor wilt zorgen dat wijzigingen worden gerepliceerd, moet u ervoor zorgen dat het herstelpunt waarnaar u migreert later is dan het tijdstip waarop de configuratiewijzigingen on-premises zijn aangebracht.


### <a name="prepare-windows-server-vms"></a>Windows Server VM's voorbereiden

**Actie** | **Details** | **Instructies**
--- | --- | ---
Controleer of Windows-volumes in Azure VM dezelfde stationslettertoewijzingen gebruiken als de on-premises VM. | Configureer het SAN-beleid als Online Alles. | 1. Meld u aan bij de VIRTUELE V.T. met een beheerdersaccount en open een opdrachtvenster.<br/> 2. Typ **schijfonderdeel** om het diskpart-hulpprogramma uit te voeren.<br/> 3. Type **SAN POLICY=OnlineAll**<br/> 4. Typ Afsluiten om Diskpart te verlaten en sluit de opdrachtprompt.
Azure-seriële toegangsconsole inschakelen voor de Azure VM | Dit helpt bij het oplossen van problemen. U hoeft de VM niet opnieuw op te starten. De Azure VM wordt opgestart met de schijfafbeelding en dit is gelijk aan een reboot voor de nieuwe VM. | Volg [deze instructies](https://docs.microsoft.com/azure/virtual-machines/windows/serial-console) om in te schakelen.
Hyper-V-gastintegratie installeren | Als u machines met Windows Server 2003 migreert, installeert u Hyper-V Guest Integration Services op het VM-besturingssysteem. | [Meer informatie](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).
Extern bureaublad | Schakel Extern bureaublad in op de virtuele machine en controleer of de Windows Firewall de toegang tot Extern bureaublad op geen enkele netwerkprofiel blokkeert. | [Meer informatie](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).

### <a name="prepare-linux-vms"></a>Linux VM's voorbereiden

**Actie** | **Details** 
--- | --- | ---
Hyper-V Linux Integration Services installeren | De meeste nieuwe versies van Linux-distributies hebben dit standaard opgenomen.
Herbouw de Linux init afbeelding om de nodige Hyper-V drivers te bevatten | Dit zorgt ervoor dat de VM wordt opgestart in Azure en alleen vereist is voor sommige distributies.
Logboekregistratie van Azure-seriële console inschakelen | Dit helpt bij het oplossen van problemen. U hoeft de VM niet opnieuw op te starten. De Azure VM wordt opgestart met de schijfafbeelding en dit is gelijk aan een reboot voor de nieuwe VM.<br/> Volg [deze instructies](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) om in te schakelen.
Apparaatkaartbestand bijwerken | Het apparaatkaartbestand met de naam van het apparaat bijwerken naar volumekoppelingen om permanente apparaat-id's te gebruiken
Fstab-vermeldingen bijwerken | Items bijwerken om permanente volume-id's te gebruiken.
Udev-regel verwijderen | Verwijder udev-regels die interfacenamen reserveert op basis van mac-adres, enz.
Netwerkinterfaces bijwerken | Netwerkinterfaces bijwerken om IP-adres te ontvangen op basis van DHCP.
SSH inschakelen | Zorg ervoor dat ssh is ingeschakeld en de sshd-service is ingesteld om automatisch te starten bij het opnieuw opstarten.<br/> Zorg ervoor dat binnenkomende ssh-verbindingsaanvragen niet worden geblokkeerd door de OS-firewall of scriptbare regels.

[Volg dit artikel](https://docs.microsoft.com/azure/virtual-machines/linux/create-upload-generic) waarin deze stappen voor het uitvoeren van een Linux-VM op Azure worden besproken en instructies bevatten voor enkele van de populaire Linux-distributies.  


## <a name="replicate-vms"></a>VM's repliceren

Als de detectie is voltooid, kunt u de replicatie van VMware-VM's naar Azure beginnen. 

> [!NOTE]
> U maximaal 10 machines samen repliceren. Als u meer moet repliceren, repliceer ze dan tegelijkertijd in batches van 10. Voor agentloze migratie u tot 100 gelijktijdige replicaties uitvoeren.

1. In het Azure Migrate project > **Servers**, **Azure Migrate: Server Migration**, klik op **Repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-vmware/select-replicate.png)

2. In **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** selecteert u **Ja, met VMware vSphere**.
3. In **On-premises apparaat** selecteert u de naam van het Azure Migrate-apparaat dat u instelt > **OK**. 

    ![Broninstellingen](./media/tutorial-migrate-vmware/source-settings.png)

    - Deze stap gaat ervan uit dat u al een apparaat hebt ingesteld wanneer u de zelfstudie hebt voltooid.
    - Als u nog geen apparaat hebt ingesteld, volgt u de instructies in [dit artikel.](how-to-set-up-appliance-vmware.md)

4. Selecteer in **Virtuele machines** de machines die u wilt repliceren.
    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.

    ![Evaluatie selecteren](./media/tutorial-migrate-vmware/select-assessment.png)

5. Zoek in **Virtuele machines** naar eigen inzicht naar VM's en controleer elke VM die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-vmware/select-vms.png)

6. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
7. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik vervolgens op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik vervolgens op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-vmware/target-settings.png)

8. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

    - **VM-grootte:** als u beoordelingsaanbevelingen gebruikt, bevat de vervolgkeuzelijst vm-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **OS-schijf**: Geef de schijf OS (opstart) op voor de VM. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset:** Als de VM zich in een Azure-beschikbaarheidsset moet bevinden na migratie, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![VM-rekeninstellingen](./media/tutorial-migrate-vmware/compute-settings.png)

9. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik vervolgens op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Disks](./media/tutorial-migrate-vmware/disks.png)

10. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart in**Replicerende machines** **beheren.** >  De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.

### <a name="provisioning-for-the-first-time"></a>Voorziening voor de eerste keer

Als dit de eerste vm is die u in het Azure Migrate-project repliceert, worden deze resources automatisch in dezelfde brongroep als het project ingericht.

- **Servicebus**: Azure Migrate Server Migration gebruikt de servicebus om replicatieorchestration-berichten naar het toestel te verzenden.
- **Gatewayopslagaccount:** Servermigratie gebruikt het gatewayopslagaccount om statusgegevens op te slaan over de VM's die worden gerepliceerd.
- **Logboekopslagaccount:** het Azure Migrate-toestel uploadt replicatielogboeken voor VM's naar een logboekopslagaccount. Azure Migrate past de replicatiegegevens toe op de door replica's beheerde schijven.
- **Sleutelkluis:** het Azure Migrate-toestel gebruikt de sleutelkluis om verbindingstekenreeksen voor de servicebus te beheren en toegangssleutels voor de opslagaccounts die in replicatie worden gebruikt. U moet de machtigingen hebben ingesteld die de sleutelkluis nodig heeft om toegang te krijgen tot het opslagaccount wanneer u zich voorbereidt. [Bekijk deze machtigingen](tutorial-prepare-vmware.md#assign-permissions-to-create-a-key-vault).   


## <a name="track-and-monitor"></a>Bijhouden en bewaken


- Wanneer u op Een taak voor startreplicatie op **repliceren** klikt, begint deze. 
- Wanneer de taak Replicatie starten is voltooid, beginnen de machines met hun eerste replicatie naar Azure.
- Tijdens de eerste replicatie wordt een VM-momentopname gemaakt. Schijfgegevens van de momentopname worden gerepliceerd naar door replica beheerde schijven in Azure.
- Nadat de eerste replicatie is voltooid, wordt deltareplicatie gestart. Incrementele wijzigingen in on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

U de taakstatus bijhouden in de portalmeldingen.

U de replicatiestatus controleren door te klikken op **Servers repliceren** in **Azure Migrate: Servermigratie**.
![Replicatie controleren](./media/tutorial-migrate-vmware/replicating-servers.png)




## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer deltareplicatie begint, u een testmigratie uitvoeren voor de VM's voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan dit minstens één keer voor elke machine te doen, voordat u deze migreert.

- Het uitvoeren van een testmigratie controleert of migratie werkt zoals verwacht, zonder dat dit gevolgen heeft voor de on-premises machines, die operationeel blijven en blijven repliceren. 
- Testmigratie simuleert de migratie door een Azure VM te maken met behulp van gerepliceerde gegevens (meestal migreren naar een niet-productieVNet in uw Azure-abonnement).
- U de gerepliceerde test Azure VM gebruiken om de migratie te valideren, app-tests uit te voeren en eventuele problemen op te lossen voordat de volledige migratie wordt uitgevoerd.

Doe een testmigratie als volgt:


1. Klik in **Migratiedoelen** > **Servers** > **Azure Migreren: Servermigratie**, klik op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-vmware/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-vmware/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-vmware/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie werkt zoals verwacht, u de on-premises machines migreren.

1. Klik in het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie**, klik op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-vmware/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. Selecteer In **Het afsluiten van** > virtuele machines migreren**en een geplande migratie uitvoeren zonder gegevensverlies,** de optie **Ja** > **OK**.
    - Azure Migrate sluit standaard de on-premises VM af en voert de replicatie op aanvraag uit om VM-wijzigingen die sinds de laatste replicatie zijn opgetreden, te synchroniseren. Zo gaan er geen gegevens verloren.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is uitgevoerd, klikt u met de rechtermuisknop op de VM > **Replicatie stoppen**. Hiermee wordt de replicatie voor de on-premises machine gestopt en worden de replicatiestatusgegevens voor de vm opgeschoond.
2. Installeer de Azure VM [Windows-](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) of [Linux-agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) op de gemigreerde machines.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Verkeer naar de gemigreerde Azure VM-instantie omknippen.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Aanbevolen procedures na migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - De toegang tot binnenkomend verkeer vergrendelen en beperken met [Azure Security Center - Net op tijd beheer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
-  Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.


## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.
