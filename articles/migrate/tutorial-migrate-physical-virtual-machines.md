---
title: Migreer machines als fysieke server naar Azure met Azure Migrate.
description: In dit artikel wordt beschreven hoe u fysieke machines migreert naar Azure met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 1824fc6c7cbc0fd0390770027f4a15d9130139de
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535380"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Machines migreren als fysieke servers naar Azure

In dit artikel ziet u hoe u machines als fysieke servers migreert naar Azure, met behulp van het hulpprogramma Azure Migreren:Servermigratie. Het migreren van machines door ze als fysieke servers te behandelen is nuttig in een aantal scenario's:

- Migreren on-premises fysieke servers.
- Vm's migreren die worden gevirtualiseerd door platforms zoals Xen, KVM.
- Migreer Hyper-V- of VMware-VM's als u om de een of andere reden het standaardmigratieproces niet gebruiken voor [Hyper-V-](tutorial-migrate-hyper-v.md)of [VMware-migratie.](server-migrate-overview.md)
- Vm's migreren die worden uitgevoerd in privéwolken.
- Vm's migreren die worden uitgevoerd in openbare clouds, zoals Amazon Web Services (AWS) of Google Cloud Platform (GCP).


Deze zelfstudie is de derde in een reeks die laat zien hoe fysieke servers naar Azure kunnen worden beoordeeld en gemigreerd. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bereid u voor om Azure te gebruiken met Azure Migrate:Server Migration.
> * Controleer de vereisten voor machines die u wilt migreren en bereid een machine voor op het Azure Migrate-replicatietoestel dat wordt gebruikt om machines te ontdekken en te migreren naar Azure.
> * Voeg het hulpprogramma voor migratie van Azure Migrate Server toe in de azure-migratiehub.
> * Stel het replicatietoestel in.
> * Installeer de Mobiliteitsservice op machines die u wilt migreren.
> * Schakel replicatie in.
> * Voer een testmigratie uit om te zien of alles werkt zoals verwacht.
> * Voer een volledige migratie naar Azure uit.

> [!NOTE]
> In zelfstudies ziet u het eenvoudigste implementatiepad voor een scenario, zodat u snel een proof-of-concept instellen. Tutorials maken gebruik van standaardopties waar mogelijk, en niet alle mogelijke instellingen en paden weer te geven. Bekijk de how-tos voor Azure Migreren voor gedetailleerde instructies.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/pricing/free-trial/) voordat u begint.


## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

[Bekijk](migrate-architecture.md) de migratiearchitectuur.




## <a name="prepare-azure"></a>Azure voorbereiden

Azure voorbereiden op migratie met Servermigratie.

**Taak** | **Details**
--- | ---
**Een Azure-migratieproject maken** | Uw Azure-account heeft machtigingen voor bijdragen de bijdrage er of eigenaar nodig om een project te maken.
**Machtigingen voor uw Azure-account verifiëren** | Uw Azure-account heeft machtigingen nodig om een VM te maken en naar een door Azure beheerde schijf te schrijven.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen om project te maken

1. Open het abonnement in de Azure-portal en selecteer **Toegangsbeheer (IAM)**.
2. Zoek **in Toegang controleren**het relevante account en klik erop om machtigingen weer te geven.
3. U moet **machtigingen voor inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, werkt u samen met de eigenaar om de rol toe te wijzen.


### <a name="assign-azure-account-permissions"></a>Azure-accountmachtigingen toewijzen

Wijs de rol Virtuele machineinzender toe aan het Azure-account. Dit biedt machtigingen voor:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijf naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

Een Virtueel Azure-netwerk (VNet) [instellen.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) Wanneer u deze nabootst naar Azure, worden Azure VM's gemaakt en samengevoegd met het Azure VNet dat u opgeeft wanneer u migratie instelt.

## <a name="prepare-for-migration"></a>Voorbereiden op migratie

Als u zich wilt voorbereiden op fysieke servermigratie, moet u de fysieke serverinstellingen verifiëren en u voorbereiden op het implementeren van een replicatietoestel.

### <a name="check-machine-requirements-for-migration"></a>Machinevereisten controleren voor migratie

Zorg ervoor dat machines voldoen aan de vereisten voor migratie naar Azure. 

> [!NOTE]
> Bij het migreren van fysieke machines gebruikt Azure Migrate:Server Migration dezelfde replicatiearchitectuur als op agent gebaseerd disaster recovery in de Azure Site Recovery-service en delen sommige onderdelen dezelfde codebasis. Sommige inhoud kan worden gekoppeld aan documentatie voor siteherstel.

1. [Controleer](migrate-support-matrix-physical-migration.md#physical-server-requirements) de vereisten van de fysieke server.
2. Controleer of on-premises machines die u naar Azure repliceert voldoen aan [azure VM-vereisten.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Een machine voorbereiden op het replicatietoestel

Azure Migreren:Servermigratie gebruikt een replicatietoestel om machines te repliceren naar Azure. In het replicatietoestel worden de volgende onderdelen uitgevoerd.

- **Configuratieserver:** de configuratieserver coördineert de communicatie tussen on-premises en Azure en beheert gegevensreplicatie.
- **Processerver:** de processerver fungeert als replicatiegateway. Het ontvangt replicatiegegevens; optimaliseert het met caching, compressie en versleuteling en stuurt het naar een cacheopslagaccount in Azure. 

Bereid u als volgt voor op de implementatie van apparaten:

- U bereidt een machine voor om het replicatietoestel te hosten. [Bekijk](migrate-replication-appliance.md#appliance-requirements) de machinevereisten. Het apparaat mag niet worden geïnstalleerd op een bronmachine die u wilt repliceren.
- Het replicatietoestel maakt gebruik van MySQL. Bekijk de [opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het toestel.
- Controleer de Azure-URL's die nodig zijn voor het replicatietoestel om toegang te krijgen tot [openbare](migrate-replication-appliance.md#url-access) en [overheidsclouds.](migrate-replication-appliance.md#azure-government-url-access)
- Controleer de toegangsvereisten voor de toegangsvereisten voor het replicatietoestel (migrate-replication-appliance.md#port-access).

## <a name="add-the-server-migration-tool"></a>Het gereedschap Servermigratie toevoegen

Stel een Azure Migrate-project in en voeg er het hulpprogramma servermigratie aan toe.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik **onder Servers ontdekken, beoordelen en migreren**op Servers beoordelen en **migreren.**

    ![Servers ontdekken en beoordelen](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
6. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
7. Geef in **Projectdetails** de projectnaam en geografie op waarin u het project wilt maken en klik op **Volgende**. Bekijk ondersteunde regio's voor [publieke](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheidswolken.](migrate-support-matrix.md#supported-geographies-azure-government)

    ![Een Azure-migratieproject maken](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. Selecteer **in Het beoordelingsprogramma selecteren**de optie Een **beoordelingstool overslaan voor nu** > **Volgende**.
9. Selecteer Azure Migreren in **Het migratiehulpprogramma selecteren:** **Servermigratie** > **volgende**.
10. Controleer in **Beoordelen en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**
11. Na het toevoegen van het hulpprogramma wordt het weergegeven in de hulpprogramma's voor azure migreren > > **serversmigratie.** **Servers**

## <a name="set-up-the-replication-appliance"></a>Het replicatietoestel instellen

De eerste stap van migratie is het instellen van het replicatietoestel. Als u het toestel wilt instellen voor fysieke servermigratie, downloadt u het installatiebestand voor het toestel en voert u het uit op de [machine die u hebt voorbereid.](#prepare-a-machine-for-the-replication-appliance) Nadat u het toestel hebt geïnstalleerd, registreert u het bij Azure Migrate Server Migration.


### <a name="download-the-replication-appliance-installer"></a>Het installatieprogramma voor replicatietoestel downloaden

1. Klik in het Azure Migrate project > **Servers**in **Azure Migrate: Servermigratie**op **Ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. In **Machines** > ontdekken**Worden uw machines gevirtualiseerd?**, klikt u op **Niet gevirtualiseerd/Ander.**
4. Selecteer **in doelgebied**het Azure-gebied waarnaar u de machines wilt migreren.
5. Selecteer **Bevestigen dat het doelgebied voor migratie een regionaam is.**
6. Klik **op Resources maken**. Hiermee wordt een Azure Site Recovery-kluis op de achtergrond geopperd.
    - Als u migratie al hebt ingesteld met Azure Migrate Server Migration, kan de doeloptie niet worden geconfigureerd, omdat resources eerder zijn ingesteld.
    - U het doelgebied voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.

7. Wilt u in Wilt u een nieuw **Install a replication appliance** **replicatietoestel installeren?**
9. Download **en installeer in De software voor het replicatietoestel,** download het toestelinstallatieprogramma en de registratiesleutel. U moet de sleutel gebruiken om het apparaat te registreren. De sleutel is vijf dagen geldig nadat deze is gedownload.

    ![Provider downloaden](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopieer het apparaatinstallatiebestand en het sleutelbestand naar de Windows Server 2016-machine die u voor het toestel hebt gemaakt.
11. Voer het installatiebestand voor replicatietoestel uit, zoals beschreven in de volgende procedure. Nadat de installatie is voltooid, wordt de wizard Toestelconfiguratie automatisch gestart (U de wizard ook handmatig starten met behulp van de snelkoppeling cspsconfigtool die op het bureaublad van het toestel is gemaakt). Gebruik het tabblad Accounts beheren van de wizard om accountgegevens toe te voegen die u wilt gebruiken voor de push-installatie van de mobiliteitsservice. In deze zelfstudie installeren we handmatig de Mobiliteitsservice op machines die moeten worden gerepliceerd, dus maak een dummy-account in deze stap en ga verder.

12. Nadat het toestel opnieuw is opgestart na de installatie, selecteert u in **Machines ontdekken**het nieuwe toestel in **Configuratieserver selecteren**en klikt u op Registratie **voltooien**. De registratie afronden voert een aantal laatste taken uit om het replicatietoestel voor te bereiden.

    ![Registratie afronden](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Het kan enige tijd duren na het afronden van de registratie totdat gedetecteerde machines worden weergegeven in Azure Migrate Server Migration. Als VM's worden ontdekt, de **Discovered servers** tellen stijgt.

![Ontdekte servers](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>De Mobility-service installeren

Op machines die u wilt migreren, moet u de Mobiliteitsserviceagent installeren. De agentinstallateurs zijn beschikbaar op het replicatietoestel. U vindt de juiste installateur en installeert de agent op elke machine die u wilt migreren. Doe dit als volgt:

1. Meld u aan bij het replicatietoestel.
2. Navigeer naar **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Zoek de installer voor het besturingssysteem en de versie van de machine. Bekijk [ondersteunde besturingssystemen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Kopieer het installatiebestand naar de machine die u wilt migreren.
5. Zorg ervoor dat u de wachtwoordzin hebt die is gegenereerd toen u het apparaat hebt geïmplementeerd.
    - Sla het bestand op in een tijdelijk tekstbestand op de machine.
    - U de wachtwoordzin op het replicatietoestel verkrijgen. Voer **c:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v uit** in de opdrachtregel om de huidige wachtwoordzin weer te geven.
    - Regenereerde de wachtwoordzin niet. Hierdoor wordt de verbinding verbroken en moet u het replicatietoestel opnieuw registreren.


### <a name="install-on-windows"></a>Installeren in Windows

1. Haal de inhoud van het installatiebestand als volgt uit naar een lokale map (bijvoorbeeld C:\Temp) op de machine:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Voer de Mobility Service Installer uit:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registreer de agent bij het replicatietoestel:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installeren in Linux

1. Haal de inhoud van de installer tarball naar een lokale map (bijvoorbeeld /tmp/MobSvcInstaller) op de machine, als volgt:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Voer het installatiescript uit:
    ```
    sudo ./install -r MS -q
    ```
3. Registreer de agent bij het replicatietoestel:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Machines repliceren

Selecteer nu machines voor migratie. 

> [!NOTE]
> U maximaal 10 machines samen repliceren. Als u meer moet repliceren, repliceer ze dan tegelijkertijd in batches van 10.

1. In het Azure Migrate project > **Servers**, **Azure Migrate: Server Migration**, klik op **Repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. In **Repliceren,**> **Broninstellingen** > Worden uw **Not virtualized/Other****machines gevirtualiseerd?**
3. Selecteer **in On-premises toestel**de naam van het Azure Migrate-toestel dat u hebt ingesteld.
4. Selecteer **in Process Server**de naam van het replicatietoestel.
6. In **gastreferenties**geeft u een dummy-account op dat wordt gebruikt voor het handmatig installeren van de Mobiliteitsservice (push-installatie wordt niet ondersteund in Fysiek). Klik vervolgens op **Volgende: Virtuele machines**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. In **Virtuele machines**, in **Migratie-instellingen importeren uit een beoordeling?**, de standaardinstelling verlaten **Nee, ik geef de migratie-instellingen handmatig op.**
8. Controleer elke vm die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
10. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
11. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik vervolgens op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik vervolgens op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-grootte**: Azure Migrate Server Migration kiest standaard een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **OS-schijf**: Geef de schijf OS (opstart) op voor de VM. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset:** Als de VM zich in een Azure-beschikbaarheidsset moet bevinden na migratie, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![Rekeninstellingen](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Geef in **schijven**op of de VM-schijven moeten worden gerepliceerd naar Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik vervolgens op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Schijfinstellingen](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart,**Replicerende machines** **beheren** > . De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.



## <a name="track-and-monitor"></a>Bijhouden en bewaken

- Wanneer u op Een taak voor startreplicatie op **repliceren** klikt, begint deze. 
- Wanneer de taak Replicatie starten is voltooid, beginnen de machines met hun eerste replicatie naar Azure.
- Nadat de eerste replicatie is voltooid, wordt deltareplicatie gestart. Incrementele wijzigingen in on-premises schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.


U de taakstatus bijhouden in de portalmeldingen.

U de replicatiestatus controleren door te klikken op **Servers repliceren** in **Azure Migrate: Servermigratie**.
![Replicatie controleren](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer deltareplicatie begint, u een testmigratie uitvoeren voor de VM's voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan dit minstens één keer voor elke machine te doen, voordat u deze migreert.

- Het uitvoeren van een testmigratie controleert of migratie werkt zoals verwacht, zonder dat dit gevolgen heeft voor de on-premises machines, die operationeel blijven en blijven repliceren. 
- Testmigratie simuleert de migratie door een Azure VM te maken met behulp van gerepliceerde gegevens (meestal migreren naar een niet-productieVNet in uw Azure-abonnement).
- U de gerepliceerde test Azure VM gebruiken om de migratie te valideren, app-tests uit te voeren en eventuele problemen op te lossen voordat de volledige migratie wordt uitgevoerd.

Doe een testmigratie als volgt:


1. Klik in **Migratiedoelen** > **Servers** > **Azure Migreren: Servermigratie**, klik op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt geverifieerd dat de testmigratie werkt zoals verwacht, u de on-premises machines migreren.

1. Klik in het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie**, klik op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. Selecteer In **Het afsluiten van** > virtuele machines migreren**en een geplande migratie uitvoeren zonder gegevensverlies,** de optie **Ja** > **OK**.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**

    Opmerking: Voor fysieke servermigratie is de aanbeveling om de toepassing naar beneden te halen als onderdeel van het migratievenster (laat de toepassingen geen verbindingen accepteren) en start vervolgens de migratie (De server moet blijven draaien, zodat de resterende wijzigingen kunnen worden gesynchroniseerd) voordat de migratie is voltooid.

4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is uitgevoerd, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - Hiermee stopt u de replicatie voor de on-premises machine.
    - Hiermee verwijdert u de machine uit het aantal **replicerende servers** in Azure Migrate: Servermigratie.
    - Reinigt de informatie over replicatiestatus voor de machine.
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
    - Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.


## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.
