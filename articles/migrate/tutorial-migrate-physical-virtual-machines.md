---
title: Migreer machines als fysieke server naar Azure met Azure Migrate.
description: In dit artikel wordt beschreven hoe u fysieke machines naar Azure migreert met Azure Migrate.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 1824fc6c7cbc0fd0390770027f4a15d9130139de
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535380"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Machines als fysieke servers migreren naar Azure

In dit artikel wordt beschreven hoe u computers als fysieke servers naar Azure migreert met behulp van de Azure Migrate: hulp programma voor server migratie. Computers migreren door ze te behandelen als fysieke servers is handig in een aantal scenario's:

- On-premises fysieke servers migreren.
- Migreer Vm's die zijn gevirtualiseerd door platforms zoals xen, KVM.
- Migreer Hyper-V-of VMware-Vm's, als u om een of andere reden het standaard migratie proces voor [hyper-v](tutorial-migrate-hyper-v.md)of [VMware](server-migrate-overview.md) -migratie niet kunt gebruiken.
- Migreer Vm's die in persoonlijke Clouds worden uitgevoerd.
- Migreer Vm's die worden uitgevoerd in open bare Clouds, zoals Amazon Web Services (AWS) of Google Cloud Platform (GCP).


Deze zelf studie is de derde in een serie die laat zien hoe u fysieke servers kunt beoordelen en migreren naar Azure. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Bereid u voor op het gebruik van Azure met Azure Migrate: Server migratie.
> * Controleer de vereisten voor de machines die u wilt migreren en bereid een machine voor op het Azure Migrate replicatie apparaat dat wordt gebruikt om computers te detecteren en te migreren naar Azure.
> * Voeg het hulp programma voor migratie van Azure Migrate-server toe in de Azure Migrate hub.
> * Stel het replicatie apparaat in.
> * Installeer de Mobility-service op de computers die u wilt migreren.
> * Schakel replicatie in.
> * Voer een test migratie uit om te controleren of alles werkt zoals verwacht.
> * Voer een volledige migratie naar Azure uit.

> [!NOTE]
> In zelf studies ziet u het eenvoudigste installatiepad voor een scenario, zodat u snel een haalbaarheids test kunt instellen. Zelf studies gebruiken waar mogelijk standaard opties en worden niet alle mogelijke instellingen en paden weer gegeven. Raadpleeg de procedures voor Azure Migrate voor gedetailleerde instructies.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

[Bekijk](migrate-architecture.md) de migratie architectuur.




## <a name="prepare-azure"></a>Azure voorbereiden

Azure voorbereiden voor migratie met server migratie.

**Taak** | **Details**
--- | ---
**Een Azure Migrate-project maken** | Uw Azure-account heeft Contribute of eigenaars machtigingen nodig om een project te maken.
**Controleer de machtigingen voor uw Azure-account** | Uw Azure-account heeft machtigingen nodig om een virtuele machine te maken en te schrijven naar een Azure Managed disk.


### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in het Azure Portal het abonnement en selecteer **toegangs beheer (IAM)**.
2. Zoek in **toegang controleren**het relevante account en klik erop om de machtigingen weer te geven.
3. U moet machtigingen voor **Inzender** of **eigenaar** hebben.
    - Als u zojuist een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samen werken om de rol toe te wijzen.


### <a name="assign-azure-account-permissions"></a>Machtigingen voor een Azure-account toewijzen

Wijs de rol Inzender voor virtuele machines toe aan het Azure-account. Dit biedt machtigingen voor het volgende:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijven naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

[Stel](../virtual-network/manage-virtual-network.md#create-a-virtual-network) een Azure Virtual Network (VNet) in. Wanneer u naar Azure repliceert, worden er Azure-Vm's gemaakt en gekoppeld aan het Azure-VNet dat u opgeeft wanneer u de migratie hebt ingesteld.

## <a name="prepare-for-migration"></a>Voorbereiden op migratie

Om de migratie van de fysieke server voor te bereiden, moet u de instellingen voor de fysieke server controleren en de implementatie van een replicatie apparaat voorbereiden.

### <a name="check-machine-requirements-for-migration"></a>Controleer de computer vereisten voor migratie

Zorg ervoor dat de computers voldoen aan de vereisten voor migratie naar Azure. 

> [!NOTE]
> Bij het migreren van fysieke machines, Azure Migrate: Server migratie maakt gebruik van dezelfde replicatie architectuur als herstel na nood gevallen in de Azure Site Recovery-service en sommige onderdelen delen dezelfde code basis. Sommige inhoud kan worden gekoppeld aan Site Recovery documentatie.

1. [Controleer](migrate-support-matrix-physical-migration.md#physical-server-requirements) de vereisten voor de fysieke server.
2. Controleer of de on-premises machines die u naar Azure repliceert, voldoen aan de vereisten van de [Azure-VM](migrate-support-matrix-physical-migration.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Een machine voorbereiden voor het replicatie apparaat

Azure Migrate: Server migratie gebruikt een replicatie apparaat om machines te repliceren naar Azure. Het replicatie apparaat voert de volgende onderdelen uit.

- **Configuratie server**: de configuratie server coördineert de communicatie tussen on-premises en Azure, en beheert de gegevens replicatie.
- **Proces server**: de proces server fungeert als replicatie gateway. Het ontvangt replicatie gegevens; optimaliseert het met caching, compressie en versleuteling, en verzendt het naar een cache-opslag account in Azure. 

Bereid de implementatie van het apparaat als volgt voor:

- U bereidt een machine voor op het hosten van het replicatie apparaat. [Bekijk](migrate-replication-appliance.md#appliance-requirements) de computer vereisten. Het apparaat mag niet worden geïnstalleerd op een bron machine die u wilt repliceren.
- Het replicatie apparaat maakt gebruik van MySQL. Bekijk de [Opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
- Bekijk de Azure-Url's die vereist zijn voor het replicatie apparaat om toegang te krijgen tot [open bare](migrate-replication-appliance.md#url-access) en [overheids](migrate-replication-appliance.md#azure-government-url-access) Clouds.
- Controleer de toegangs vereisten voor [Port] (migrate-Replication-Appliance. MD # Port-Access) voor het replicatie apparaat.

## <a name="add-the-server-migration-tool"></a>Het hulp programma voor server migratie toevoegen

Stel een Azure Migrate project in en voeg vervolgens het hulp programma voor server migratie toe.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik onder **servers detecteren, evalueren en migreren**op **servers beoordelen en migreren**.

    ![Servers detecteren en beoordelen](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
6. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
7. Geef in **Projectdetails** de projectnaam en geografie op waarin u het project wilt maken en klik op **Volgende**. Bekijk ondersteunde geografies voor [open bare](migrate-support-matrix.md#supported-geographies-public-cloud) en [overheids Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Een Azure Migrate-project maken](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. Selecteer in **hulp programma voor beoordeling selecteren** **de optie overs Laan een evaluatie programma toevoegen voor nu** > **volgende**.
9. Selecteer in **hulp programma voor migratie selecteren**de optie **Azure migrate: Server migratie** > **volgende**.
10. Controleer in **Beoordelen en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**
11. Nadat u het hulp programma hebt toegevoegd, wordt het weer gegeven in de Azure migrate project > **servers** > **Migration Tools**.

## <a name="set-up-the-replication-appliance"></a>Het replicatie apparaat instellen

De eerste stap van de migratie is het instellen van het replicatie apparaat. Als u het apparaat voor de migratie van fysieke servers wilt instellen, downloadt u het installatie bestand voor het apparaat en voert u het uit op de [computer die u hebt voor bereid](#prepare-a-machine-for-the-replication-appliance). Na de installatie van het apparaat registreert u het met Azure Migrate server migratie.


### <a name="download-the-replication-appliance-installer"></a>Het installatie programma voor het replicatie apparaat downloaden

1. Klik in het Azure Migrate project > **servers**in **Azure migrate: Server migratie**op **ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. Zijn uw machines in **Discover-computers** > **gevirtualiseerde?** Klik op **niet gevirtualiseerd/Overig**.
4. Selecteer in **doel regio**de Azure-regio waarnaar u de machines wilt migreren.
5. Selecteer **controleren of de doel regio voor de migratie de regio naam is**.
6. Klik op **resources maken**. Hiermee maakt u een Azure Site Recovery kluis op de achtergrond.
    - Als u de migratie al hebt ingesteld met Azure Migrate server migratie, kan de doel optie niet worden geconfigureerd, omdat de resources eerder zijn ingesteld.
    - U kunt de doel regio voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.

7. In wilt **u een nieuw replicatie apparaat installeren?** Selecteer **een replicatie apparaat installeren**.
9. In **down load en installeer de software voor het replicatie apparaat**downloadt u het installatie programma van het toestel en de registratie sleutel. U moet de sleutel opgeven om het apparaat te kunnen registreren. De sleutel is vijf dagen geldig nadat deze is gedownload.

    ![Provider downloaden](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Kopieer het installatie bestand voor het toestel en het sleutel bestand naar de Windows Server 2016-computer die u hebt gemaakt voor het apparaat.
11. Voer het installatie bestand voor het replicatie-apparaat uit, zoals beschreven in de volgende procedure. Nadat de installatie is voltooid, wordt de wizard toestel configureren automatisch gestart (u kunt de wizard ook hand matig starten met behulp van de cspsconfigtool-snelkoppeling die op het bureau blad van het apparaat is gemaakt). Gebruik het tabblad Accounts beheren van de wizard om account details toe te voegen die moeten worden gebruikt voor de push-installatie van de Mobility-service. In deze zelf studie wordt de Mobility-service hand matig geïnstalleerd op computers die moeten worden gerepliceerd. Daarom moet u in deze stap een dummy-account maken en door gaan.

12. Nadat het apparaat opnieuw is opgestart na de installatie, selecteert u in **computers detecteren**het nieuwe apparaat in **Configuratie server selecteren**en klikt u op **registratie volt ooien**. Bij het volt ooien van de registratie worden de laatste taken uitgevoerd om het replicatie apparaat voor te bereiden.

    ![Registratie volt ooien](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Het kan enige tijd duren nadat de registratie is voltooid totdat gedetecteerde computers worden weer gegeven in Azure Migrate server migratie. Naarmate er Vm's worden gedetecteerd, nemen het aantal **gedetecteerde servers** toe.

![Gedetecteerde servers](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>De Mobility-service installeren

Op machines die u wilt migreren, moet u de Mobility Service-agent installeren. De agent installatie Programma's zijn beschikbaar op het replicatie apparaat. U vindt het juiste installatie programma en installeert de agent op elke computer die u wilt migreren. Doe dit als volgt:

1. Meld u aan bij het replicatie apparaat.
2. Navigeer naar **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Zoek het installatie programma voor het besturings systeem en de versie van de machine. Bekijk [ondersteunde besturings systemen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Kopieer het installatie bestand naar de computer die u wilt migreren.
5. Zorg ervoor dat u beschikt over de wachtwoordzin die is gegenereerd bij het implementeren van het apparaat.
    - Sla het bestand op in een tijdelijk tekst bestand op de computer.
    - U kunt de wachtwoordzin op het replicatie apparaat verkrijgen. Voer **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** uit vanaf de opdracht regel om de huidige wachtwoordzin weer te geven.
    - Genereer de wachtwoordzin niet opnieuw. Hierdoor wordt de verbinding verbroken en moet u het replicatie apparaat opnieuw registreren.


### <a name="install-on-windows"></a>Installeren in Windows

1. Pak de inhoud van het installatie bestand uit naar een lokale map (bijvoorbeeld C:\Temp) op de computer, als volgt:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Het installatie programma van de Mobility-service uitvoeren:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Registreer de agent bij het replicatie apparaat:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Installeren in Linux

1. Pak de inhoud van het installatie programma tarball naar een lokale map (bijvoorbeeld/tmp/MobSvcInstaller) op de computer, als volgt:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Voer het installatie script uit:
    ```
    sudo ./install -r MS -q
    ```
3. Registreer de agent bij het replicatie apparaat:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Computers repliceren

Selecteer nu machines voor migratie. 

> [!NOTE]
> U kunt Maxi maal 10 computers tegelijk repliceren. Als u meer wilt repliceren, repliceert u ze tegelijkertijd in batches van 10.

1. Klik in het Azure Migrate project > **servers**, **Azure migrate: Server migratie**op **repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. In **repliceren**, > **bron instellingen** > **zijn uw machines gevirtualiseerd?**, selecteert u **niet gevirtualiseerd/Overig**.
3. Selecteer in **on-premises apparaat**de naam van het Azure migrate apparaat dat u hebt ingesteld.
4. Selecteer in **proces server**de naam van het replicatie apparaat.
6. Bij **gast referenties**geeft u een dummy-account op dat wordt gebruikt voor het hand matig installeren van de Mobility-service (push installatie wordt niet ondersteund in fysiek). Klik vervolgens op **volgende: virtuele machines**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. In **virtual machines**, in **migratie-instellingen importeren uit een evaluatie?**, de standaard instelling **Nee, ik geef de migratie-instellingen hand matig**op.
8. Controleer elke VM die u wilt migreren. Klik vervolgens op **volgende: doel instellingen**.

    ![Vm's selecteren](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
10. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
11. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik vervolgens op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik vervolgens op **Volgende**.

    ![Doel instellingen](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-grootte**: standaard wordt door Azure migrate server migratie een grootte gekozen op basis van het dichtstbijzijnde resultaat van het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **Besturingssysteem schijf**: Geef de opstart schijf van het besturings systeem voor de virtuele machine op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset**: als de virtuele machine na de migratie moet worden besteld in een Azure-beschikbaarheidsset, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![Reken instellingen](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Geef in **schijven**op of de VM-schijven moeten worden gerepliceerd naar Azure en selecteer het schijf type (standaard SSD/HDD of Premium Managed disks) in Azure. Klik vervolgens op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Schijf instellingen](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt de replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart, de**replicerende machines** **beheren** > . De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.



## <a name="track-and-monitor"></a>Bijhouden en controleren

- Wanneer **u op replicatie** starten klikt, begint de taak. 
- Wanneer de taak replicatie starten is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de replicatie van verschillen. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar de replica schijven in Azure.


U kunt de taak status volgen in de portal meldingen.

U kunt de replicatie status controleren door te klikken op **replicerende servers** in **Azure migrate: Server migratie**.
![Replicatie controleren](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer Delta replicatie begint, kunt u een test migratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om dit ten minste één keer te doen voor elke machine voordat u deze migreert.

- Bij het uitvoeren van een test migratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de on-premises machines, die operationeel blijven, en door gaan met repliceren. 
- Met test migratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde virtuele machine van Azure gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een test migratie uit te voeren:


1. In **migratie doelen** > **servers** > **Azure migrate: Server migratie**klikt u op **gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt gecontroleerd of de test migratie werkt zoals verwacht, kunt u de on-premises machines migreren.

1. Klik in het Azure migrate project > **servers** > **Azure migrate: Server migratie**op **servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. Selecteer **Migrate** >  **Yes**Ja > **OK**bij het migreren van**virtuele machines afsluiten en het uitvoeren van een geplande migratie zonder gegevens verlies**.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**

    Opmerking: voor de migratie van een fysieke server is het aanbeveling om de toepassing uit te voeren als onderdeel van het migratie venster (laat de toepassingen geen enkele verbinding accepteren) en start vervolgens de migratie (de server moet worden uitgevoerd, zodat de resterende wijzigingen kunnen worden gesynchroniseerd) voordat de migratie is voltooid.

4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechter muisknop op de virtuele machine > de **migratie stoppen**. Er gebeurt nu het volgende:
    - Hiermee wordt de replicatie voor de on-premises computer gestopt.
    - Hiermee verwijdert u de computer van het aantal **replicerende servers** in azure migrate: Server migratie.
    - Hiermee wordt de replicatie status informatie voor de machine opgeschoond.
2. Installeer de Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -of [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -agent op de gemigreerde computers.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Verdeel het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang tot inkomend verkeer met [Azure Security Center-just-in-time-beheer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.


## <a name="next-steps"></a>Volgende stappen

Onderzoek de [reis voor Cloud migratie](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud-acceptatie Framework.
