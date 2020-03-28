---
title: Hyper-V VM's migreren naar Azure met Migratie van Azure-migratie servermigreren
description: Meer informatie over het migreren van on-premises Hyper-V VM's naar Azure met Azure Migrate Server Migration
ms.topic: tutorial
ms.date: 11/18/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2b9ed56186649b7644adbd1237ad74af50474cc5
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80279705"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V-VM's migreren naar Azure 

In dit artikel ziet u hoe u on-premises Hyper-V VM's migreert naar Azure, met behulp van agentless migratie met het hulpprogramma Azure Migrate: Server Migration.

[Azure Migrate](migrate-services-overview.md) biedt een centrale hub om detectie, beoordeling en migratie van uw on-premises apps en workloads en private/public cloud VM's naar Azure bij te houden. De hub biedt Azure Migrate-tools voor beoordeling en migratie, evenals isv-aanbiedingen (onafhankelijke softwareleveranciers) van derden.

Deze zelfstudie is de derde in een reeks die laat zien hoe u Hyper-V beoordelen en migreren naar Azure met Azure Migrate Server Assessment and Migration. In deze zelfstudie leert u het volgende:


> [!div class="checklist"]
> * Azure en uw on-premises Hyper-V-omgeving voorbereiden
> * Stel de bronomgeving in en implementeer een replicatietoestel.
> * Stel de doelomgeving in.
> * Schakel replicatie in.
> * Voer een testmigratie uit om te zien of alles werkt zoals verwacht.
> * Voer een volledige migratie naar Azure uit.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Bekijk](hyper-v-migration-architecture.md) de Hyper-V-migratiearchitectuur.
2. [Voer de eerste zelfstudie](tutorial-prepare-hyper-v.md) in deze serie uit om Azure en Hyper-V in te stellen voor migratie. In de eerste zelfstudie, je:
    - [Azure voorbereiden op](tutorial-prepare-hyper-v.md#prepare-azure) migratie.
    - [Bereid de on-premises omgeving](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) voor op migratie.
3. We raden u aan hyperv-VM's te beoordelen met Azure Migrate: Server Assessment voordat u ze migreert naar Azure. Om dit te doen, [voltooi t u de tweede zelfstudie](tutorial-assess-hyper-v.md) in deze serie. Hoewel we u aanraden een beoordeling uit te proberen, hoeft u geen beoordeling uit te voeren voordat u VM's migreert.
4. Zorg ervoor dat aan uw Azure-account de rol Virtuele machineinzender is toegewezen, zodat u machtigingen hebt voor:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijf naar een door Azure beheerde schijf.
5. [Een Azure-netwerk instellen](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Wanneer u migreert naar Azure, worden de gemaakte Azure VM's samengevoegd met een Azure-netwerk dat u opgeeft wanneer u migratie instelt.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Het hulpprogramma voor migratie van Azure-migrerende server toevoegen

Als u de tweede zelfstudie voor het beoordelen van Hyper-V VM's niet hebt gevolgd, moet u [deze instructies volgen](how-to-add-tool-first-time.md) om een Azure Migrate-project in te stellen en het hulpprogramma voor azure-beoordeling van de server migreren aan het project toe te voegen.

Als u de tweede zelfstudie hebt gevolgd en al een Azure Migrate-project hebt, voegt u het hulpprogramma Azure Migreren: Servermigratie als volgt toe:

1. Klik in het Azure Migrate-project op **Overzicht**. 
2. Klik in **Servers voor ontdekken, beoordelen en migreren**op Servers beoordelen en **migreren.**
3. Selecteer in **migratiehulpprogramma's** **Klik hier om een migratiegereedschap toe te voegen wanneer u klaar bent om te migreren.**

    ![Een gereedschap selecteren](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Selecteer **Azure Migrate: Hulpprogramma Voor servermigratie** > **toevoegen** in de lijst met hulpprogramma's voor hulpprogramma voor hulpprogramma voor hulpprogramma voor het migreren

    ![Servermigratie, gereedschap](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Het Azure Migrate-toestel instellen

Azure Migrate Server Migration voert een softwareagent uit op Hyper-V Hosts of clusterknooppunten om gegevens te orkestreren en te repliceren naar Azure Migrate en vereist geen speciaal toestel voor migratie.

- Het Azure-migrateen: serverbeoordelingstoestel voert VM-detectie uit en verzendt VM-metagegevens en prestatiegegevens naar Azure Migrate Server Migration.
- Migratieorkestratie en gegevensreplicatie worden afgehandeld door de Microsoft Azure Site Recovery-provider en de Microsoft Azure Recovery Service-agent.

Ga als het gaat om het instellen van het apparaat:
- Als u de tweede zelfstudie hebt gevolgd om Hyper-V VM's te beoordelen, hebt u het apparaat al ingesteld tijdens die zelfstudie en hoeft u het niet opnieuw te doen.
- Als u die zelfstudie niet hebt gevolgd, moet u het apparaat nu instellen. Om dit te doen, u: 

    - Download een gecomprimeerde Hyper-V VHD van de Azure-portal.
    - Maak het toestel en controleer of het verbinding kan maken met Azure Migrate Server Assessment. 
    - Configureer het toestel voor de eerste keer en registreer het met het Azure Migrate-project.

    Volg de gedetailleerde instructies in [dit artikel](how-to-set-up-appliance-hyper-v.md) om het apparaat in te stellen.

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-hosts voorbereiden

1. Klik in het Azure Migrate project > **Servers**in **Azure Migrate: Servermigratie**op **Ontdekken**.
2. In **Discover machines** > Worden uw machines **Yes, with Hyper-V****gevirtualiseerd?**
3. Selecteer **in doelgebied**het Azure-gebied waarnaar u de machines wilt migreren.
6. Selecteer **Bevestigen dat het doelgebied voor migratie een regionaam is.**
7. Klik **op Resources maken**. Hiermee wordt een Azure Site Recovery-kluis op de achtergrond geopperd.
    - Als u migratie al hebt ingesteld met Azure Migrate Server Migration, wordt deze optie niet weergegeven omdat resources eerder zijn ingesteld.
    - U het doelgebied voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.
    
8. Download in **Hyper-V-hostservers voorbereiden**de Hyper-V-replicatieprovider en het bestand met registratiesleutel.
    - De registratiesleutel is nodig om de Hyper-V-host te registreren bij Azure Migrate Server Migration.
    - De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Downloadprovider en sleutel](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Kopieer het bestand en registratiesleutelbestand van de provider naar elke Hyper-V-host (of clusterknooppunt) met VM's die u wilt repliceren.
5. Voer het installatiebestand van de provider uit op elke host, zoals beschreven in de volgende procedure.
6. Nadat u de provider op hosts hebt geïnstalleerd, klikt u in **Machines ontdekken**op **Registratie afronden**.

    ![Registratie afronden](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Het kan tot 15 minuten duren nadat de registratie is afgerond totdat gedetecteerde VM's worden weergegeven in Azure Migrate Server Migration. Als VM's worden ontdekt, de **Discovered servers** tellen stijgt.

![Ontdekte servers](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Hyper-V hosts registreren

Installeer het gedownloade installatiebestand (AzureSiteRecoveryProvider.exe) op elke relevante Hyper-V-host.

1. Voer het installatiebestand van de provider uit op elke host- of clusterknooppunt.
2. In de wizard Providersetup > **Microsoft Update**u Microsoft Update gebruiken om te controleren op providerupdates.
3. Accepteer in **Installatie**de standaardinstallatielocatie voor de provider en agent en selecteer **Installeren**.
4. Selecteer na de installatie in de wizard Registratie > **Vault Instellingen**, selecteer **Bladeren**en selecteer in **Sleutelbestand**het kluissleutelbestand dat u hebt gedownload.
5. Geef in **proxy-instellingen**op hoe de provider die op de host wordt uitgevoerd, verbinding maakt met internet.
    - Als het toestel zich achter een proxyserver bevindt, moet u proxy-instellingen opgeven.
    - Geef de proxynaam op als **http://ip-address**, of **http://FQDN**. HTTPS-proxyservers worden niet ondersteund.
   

6. Zorg ervoor dat de provider de [vereiste URL's](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts)kan bereiken.
7. Klik **in Registratie,** nadat de host is geregistreerd, op **Voltooien**.

## <a name="replicate-hyper-v-vms"></a>Hyper-V VM's repliceren

Als detectie is voltooid, u beginnen met het repliceren van Hyper-V VM's naar Azure.

> [!NOTE]
> U maximaal 10 machines samen repliceren. Als u meer moet repliceren, repliceer ze dan tegelijkertijd in batches van 10.

1. In het Azure Migrate project > **Servers**, **Azure Migrate: Server Migration**, klik op **Repliceren**.
2. In **Repliceren,**> **Broninstellingen** > Worden uw **Yes, with Hyper-V****machines gevirtualiseerd?** Klik vervolgens op **Volgende: Virtuele machines**.
3. Selecteer in **Virtuele machines** de machines die u wilt repliceren.
    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.

        ![Evaluatie selecteren](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Zoek in **Virtuele machines** naar eigen inzicht naar VM's en controleer elke VM die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Selecteer **in Doelinstellingen**het doelgebied waarnaar u migreert, het abonnement en de brongroep waarin de Azure VM's zich na migratie bevinden.
7. Selecteer in **replicatieopslagaccount**het Azure Storage-account waarin gerepliceerde gegevens worden opgeslagen in Azure.
8. **Virtueel netwerk**, selecteer het Azure VNet/subnet waaraan de Azure VM's na migratie worden samengevoegd.
9. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik vervolgens op **Volgende.**
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik vervolgens op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **VM-grootte:** als u beoordelingsaanbevelingen gebruikt, bevat de vervolgkeuzelijst vm-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **OS-schijf**: Geef de schijf OS (opstart) op voor de VM. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset:** Als de VM zich in een Azure-beschikbaarheidsset moet bevinden na migratie, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![VM-rekeninstellingen](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik vervolgens op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Disks](./media/tutorial-migrate-hyper-v/disks.png)

10. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart in**Replicerende machines** **beheren.** >  De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.

## <a name="provisioning-for-the-first-time"></a>Voorziening voor de eerste keer

Als dit de eerste vm is die u in het Azure Migrate-project repliceert, worden in Azure Migrate: ServerMigration deze resources automatisch in dezelfde brongroep als het project voorziet.

- **Servicebus**: Azure Migreren: Servermigratie gebruikt de Servicebus om replicatieorchestration-berichten naar het toestel te verzenden.
- **Gatewayopslagaccount:** Azure Migrate: Servermigratie gebruikt het gatewayopslagaccount om statusgegevens op te slaan over de VM's die worden gerepliceerd.
- **Logboekopslagaccount:** het Azure Migrate-toestel uploadt replicatielogboeken voor VM's naar een logboekopslagaccount. Azure Migrate past de replicatiegegevens toe op de door replica's beheerde schijven.
- **Sleutelkluis:** het Azure Migrate-toestel gebruikt de sleutelkluis om verbindingstekenreeksen voor de servicebus te beheren en toegangssleutels voor de opslagaccounts die in replicatie worden gebruikt. U moet de machtigingen hebben ingesteld die de sleutelkluis nodig heeft om toegang te krijgen tot het opslagaccount wanneer u [Azure voorbereidt](tutorial-prepare-hyper-v.md#prepare-azure) op Hyper-V VM-beoordeling en -migratie. 


## <a name="track-and-monitor"></a>Bijhouden en bewaken


- Wanneer u op Een taak voor startreplicatie op **repliceren** klikt, begint deze. 
- Wanneer de taak Replicatie starten is voltooid, beginnen de machines met hun eerste replicatie naar Azure.
- Nadat de eerste replicatie is voltooid, wordt deltareplicatie gestart. Incrementele wijzigingen in on-premises schijven worden periodiek gerepliceerd naar Azure.

U de taakstatus bijhouden in de portalmeldingen.

U de replicatiestatus controleren door te klikken op **Servers repliceren** in **Azure Migrate: Servermigratie**.
![Replicatie controleren](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer deltareplicatie begint, u een testmigratie uitvoeren voor de VM's voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan dit minstens één keer voor elke machine te doen, voordat u deze migreert.

- Het uitvoeren van een testmigratie controleert of migratie werkt zoals verwacht, zonder dat dit gevolgen heeft voor de on-premises machines, die operationeel blijven en blijven repliceren. 
- Testmigratie simuleert de migratie door een Azure VM te maken met behulp van gerepliceerde gegevens (meestal migreren naar een niet-productie Azure VNet in uw Azure-abonnement).
- U de gerepliceerde test Azure VM gebruiken om de migratie te valideren, app-tests uit te voeren en eventuele problemen op te lossen voordat de volledige migratie wordt uitgevoerd.

Doe een testmigratie als volgt:


1. Klik in **Migratiedoelen** > **Servers** > **Azure Migreren: Servermigratie**, klik op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Selecteer **in Testmigratie**het virtuele Azure-netwerk waarin de Azure VM zich na de migratie bevindt. We raden u aan een niet-productievirtueel netwerk te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie werkt zoals verwacht, u de on-premises machines migreren.

1. Klik in het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie**, klik op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. Selecteer In **Het afsluiten van** > virtuele machines migreren**en een geplande migratie uitvoeren zonder gegevensverlies,** de optie **Ja** > **OK**.
    - Azure Migrate sluit standaard de on-premises VM af en voert de replicatie op aanvraag uit om VM-wijzigingen die sinds de laatste replicatie zijn opgetreden, te synchroniseren. Zo gaan er geen gegevens verloren.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is uitgevoerd, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - Hiermee stopt u de replicatie voor de on-premises machine.
    - Hiermee verwijdert u de machine uit het aantal **replicerende servers** in Azure Migrate: Servermigratie.
    - Ruimt de informatie over replicatiestatus voor de VM op.
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
