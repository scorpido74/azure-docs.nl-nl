---
title: Amazon Web Services-VM's (AWS) ontdekken, beoordelen en migreren naar Azure
description: In dit artikel wordt beschreven hoe AWS-VM's naar Azure migreert met Azure Migrate.
ms.topic: tutorial
ms.date: 06/16/2020
ms.custom: MVC
ms.openlocfilehash: 739439f63c81ef75cdcbe0b9e1d3f367d073d43b
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/22/2020
ms.locfileid: "85198811"
---
# <a name="discover-assess-and-migrate-amazon-web-services-aws-vms-to-azure"></a>Amazon Web Services-VM's (AWS) ontdekken, beoordelen en migreren naar Azure

In deze zelfstudie ziet u hoe u AWS-VM’s (Amazon Web Services) ontdekt, beoordeelt en naar Azure migreert met behulp van Azure Migrate: Hulpprogramma's voor serverevaluatie en servermigratie

> [!NOTE]
> Wanneer u uw AWS-VM's naar Azure migreert, worden de VM's behandeld als fysieke servers. U gebruikt de servermigratiestroom voor de migratie van fysieke machines, om uw AWS-VM's naar Azure te migreren.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Controleer de vereisten voor migratie.
> * Zo bereidt u Azure-resources voor met Azure Migrate: Server Migration. Stel machtigingen in voor uw Azure-account en -resources om met Azure Migrate te werken.
> * Bereid AWS EC2-instanties voor op migratie.
> * Voeg het hulpprogramma Azure Migrate: Server Migration toe in de Azure Migrate-hub.
> * Configureer het replicatieapparaat en implementeer de configuratie.
> * Installeer de Mobility-service op de AWS-VM's die u wilt migreren.
> * Schakel replicatie in voor VM's.
> * Traceer en bewaak de replicatiestatus. 
> * Voer een testmigratie uit om te controleren of alles goed werkt.
> * Voer een volledige migratie naar Azure uit.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.

## <a name="discover-and-assess-aws-vms"></a>AWS-VM's detecteren en evalueren  

Voordat u naar Azure migreert, raden we u aan een evaluatie van de VM-detectie en -migratie uit te voeren. Deze evaluatie helpt u bij een correcte instelling van de grootte van de AWS-VM's voor migratie naar Azure en bij het inschatten van de potentiële Azure-uitvoeringskosten.

U stelt als volgt een evaluatie in:

1. Een evaluatie kan worden uitgevoerd door uw AWS-VM's als fysieke machines te behandelen voor het uitvoeren van een evaluatie met behulp van de Azure Migrate: Serverevaluatieprogramma. Volg de [zelfstudie](https://docs.microsoft.com/azure/migrate/tutorial-prepare-physical) om Azure in te stellen en uw AWS-VM's voor te bereiden voor een evaluatie.
2. Volg vervolgens deze [zelfstudie](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) om een Azure Migrate-project en -apparaat in te stellen om uw AWS-VM's te ontdekken en te evalueren.

We raden u aan om een evaluatie uit te voeren, maar het uitvoeren van een evaluatie is geen verplichte stap voor het migreren van VM's.

## <a name="migrate-aws-vms"></a>AWS-VM's migreren   

## <a name="1-prerequisites-for-migration"></a>1. Vereisten voor migratie

- Zorg ervoor dat de AWS-VM’s die u wilt migreren, worden uitgevoerd met een ondersteunde versie van het besturingssysteem. Voor het doel van de migratie worden AWS-VM's behandeld als fysieke machines. Bekijk de [ondersteunde besturingssystemen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) voor de migratiewerkstroom voor fysieke servers. U wordt aangeraden een testmigratie (testfailover) uit te voeren om te controleren of de virtuele machine werken zoals verwacht, voordat u doorgaat met de daadwerkelijke migratie.
- Zorg ervoor dat de AWS-VM's voldoen aan de [ondersteunde configuraties](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical-migration#physical-server-requirements) voor migratie naar Azure.
- Controleer of de AWS-VM's die u naar Azure repliceert, voldoen aan de [VM-vereisten voor Azure](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical-migration#azure-vm-requirements).
- U moet enkele wijzigingen doorvoeren in de virtuele machines voordat u ze naar Azure migreert.
    - Voor sommige besturingssystemen worden deze wijzigingen automatisch door Azure Migrate aangebracht.
    - Het is belangrijk dat u deze wijzigingen aanbrengt voordat u begint met de migratie. Als u de VM migreert voordat u de wijzigingen doorvoert, start de VM mogelijk niet op in Azure.
Controleer de voor [Windows](prepare-for-migration.md#windows-machines) en [Linux](prepare-for-migration.md#linux-machines) vereiste wijzigingen.

## <a name="2-prepare-azure-resources-for-migration"></a>2. Azure-resources voorbereiden op migratie

Zo bereidt u Azure voor op migratie met Azure Migrate: tool Server Migration van Azure Migrate.

**Taak** | **Details**
--- | ---
**Maak een Azure Migrate-project** | Uw Azure-account heeft Inzender- of Eigenaarsmachtigingen nodig om een project te maken.
**Machtigingen verifiëren voor uw Azure-account** | U hebt voor uw Azure-account machtigingen nodig om een virtuele machine te maken en naar een beheerde Azure-schijf te schrijven.

### <a name="assign-permissions-to-create-project"></a>Machtigingen toewijzen voor het maken van een project

1. Open in de Azure-portal het abonnement en selecteer **Toegangsbeheer (IAM)** .
2. In **Toegang controleren**, zoekt u het relevante account en klikt u hierop om machtigingen weer te geven.
3. U moet de machtigingen **Inzender** of **Eigenaar** hebben.
    - Als u net pas een gratis Azure-account hebt gemaakt, bent u de eigenaar van uw abonnement.
    - Als u niet de eigenaar van het abonnement bent, kunt u met de eigenaar samenwerken om de rol toe te wijzen.

### <a name="assign-azure-account-permissions"></a>Machtigingen voor Azure-accounts toewijzen

Wijs de rol van Inzender voor virtuele machines toe aan het Azure-account. Deze biedt machtigingen voor het volgende:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijf naar een door Azure beheerde schijf. 

### <a name="create-an-azure-network"></a>Een Azure-netwerk maken

[Stel](../virtual-network/manage-virtual-network.md#create-a-virtual-network) een virtueel Azure-netwerk (VNet) in. Wanneer u repliceert naar Azure, worden de Azure-VM's gemaakt en gekoppeld aan het Azure VNet dat u opgeeft wanneer u de migratie instelt.

## <a name="3-prepare-aws-instances-for-migration"></a>3. AWS-instanties voorbereiden voor migratie

Om AWS voor te bereiden op migratie naar Azure, moet u een replicatieapparaat voorbereiden en implementeren voor migratie.

### <a name="prepare-a-machine-for-the-replication-appliance"></a>Een computer voorbereiden voor het replicatieapparaat

Azure Migrate: Server Migration gebruikt het replicatieapparaat om machines naar Azure te repliceren. Met het replicatieapparaat worden de volgende onderdelen uitgevoerd.

- **Configuratieserver**: De configuratieserver coördineert de communicatie tussen de AWS-omgeving en Azure, en beheert de gegevensreplicatie.
- **Processerver**: De processerver fungeert als replicatiegateway. Deze ontvangt replicatiegegevens, optimaliseert de gegevens met caching, compressie en versleuteling, en verzendt ze naar het account voor cacheopslag in Azure.

Bereid de implementatie van het apparaat als volgt voor:

- Stel een afzonderlijke EC2-VM in om het replicatieapparaat te hosten. Op deze instantie moet Windows Server 2012 R2 of Windows Server 2016 worden uitgevoerd. [Bekijk](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance#appliance-requirements) de hardware-, software- en netwerkvereisten voor het apparaat.
- Het apparaat mag niet worden geïnstalleerd op een bron-VM die u wilt repliceren. Het moet op een andere VM worden geïmplementeerd.
- De bron-AWS-VM's die u wilt migreren, moeten een netwerkregel hebben die is gericht op het replicatieapparaat. Configureer de noodzakelijke beveiligingsgroepsregels om dit in te schakelen. Het is raadzaam dat het replicatieapparaat wordt geïmplementeerd in dezelfde VPC als de bron-VM's die moeten worden gemigreerd. Als het replicatie apparaat zich in een andere VPC moet bevinden, moeten de VPC's worden verbonden via VPC-peering.
- De bron-AWS-VM's communiceren met het replicatieapparaat via de poorten HTTPS 443 (indeling van controlekanaal) en TCP 9443 (gegevenstransport) inkomend voor replicatiebeheer en de gegevensoverdracht voor replicatie. Het replicatieapparaat organiseert en verzendt replicatiegegevens naar Azure via poort HTTPS 443 uitgaand. Als u deze regels wilt configureren, bewerkt u de regels voor inkomende/uitgaande regels van de beveiligingsgroep met de juiste poorten en de bron-IP-gegevens.

   ![AWS-beveiligingsgroepen ](./media/tutorial-migrate-aws-virtual-machines/aws-security-groups.png)
     
 
   ![Beveiligingsinstellingen bewerken ](./media/tutorial-migrate-aws-virtual-machines/edit-security-settings.png)

- Het replicatieapparaat maakt gebruik van MySQL. Bekijk de [opties](migrate-replication-appliance.md#mysql-installation) voor het installeren van MySQL op het apparaat.
- Controleer de vereiste Azure-URL's voor het replicatieapparaat om toegang te krijgen tot [openbare](migrate-replication-appliance.md#url-access) clouds en [overheidsclouds](migrate-replication-appliance.md#azure-government-url-access).

## <a name="4-add-the-server-migration-tool"></a>4. Het hulpprogramma Server Migration toevoegen

Stel een Azure Migrate-project in en voeg daarna het hulpprogramma Server Migration eraan toe.

1. Zoek in de Azure-portal in **Alle services** naar **Azure Migrate**.
2. Onder **Services** selecteert u **Azure Migrate**.
3. Klik in **Overzicht** op **Servers evalueren en migreren**.
4. Klik onder **Servers detecteren, evalueren en migreren** op **Servers evalueren en migreren**.

    ![Servers detecteren en evalueren](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. Klik in **Servers detecteren, evalueren en migreren** op **Hulpprogramma's toevoegen**.
6. Selecteer in **Project migreren** uw Azure-abonnement en maak een resourcegroep als u er nog geen hebt.
7. Geef in **Projectdetails** de projectnaam en geografie op waarin u het project wilt maken en klik op **Volgende**. Bekijk ondersteunde geografische regio's voor [openbare](migrate-support-matrix.md#supported-geographies-public-cloud) clouds en [overheidsclouds](migrate-support-matrix.md#supported-geographies-azure-government).
    - Het geografische gebied voor het project wordt alleen gebruikt om de metagegevens op te slaan die worden verzameld van AWS-computers.
    - Wanneer u een migratie uitvoert, kunt u elke gewenste doelregio selecteren.

    ![Een Azure Migrate-project maken](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. Selecteer in **Evaluatieprogramma selecteren** de optie **Het toevoegen van een evaluatieprogramma voorlopig overslaan** > **Volgende**.
9. Selecteer in **Migratieprogramma selecteren** de optie **Azure Migrate: Server Migration** > **Volgende**.
10. Controleer in **Beoordelen en hulpprogramma's toevoegen** de instellingen en klik op **Hulpmiddelen toevoegen**
11. Nadat u het hulpprogramma hebt toegevoegd, wordt het weergegeven in het Azure Migrate-project > **Servers** > **Migratieprogramma's**.

## <a name="5-set-up-the-replication-appliance"></a>5. Het replicatieapparaat instellen

De eerste stap van de migratie is het instellen van het replicatieapparaat. Als u het apparaat voor de migratie van AWS-VM's wilt instellen, downloadt u het installatiebestand voor het apparaat. Vervolgens voert u het uit op de [VM die u hebt voorbereid](#prepare-a-machine-for-the-replication-appliance).

### <a name="download-the-replication-appliance-installer"></a>Het installatieprogramma voor het replicatieapparaat downloaden

1. In het Azure Migrate-project > **Servers**, in **Azure Migrate: Server Migration**, klikt u op **Ontdekken**.

    ![VM's detecteren](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. In **Machines ontdekken** > **Zijn uw machines gevirtualiseerd?** selecteert u **Niet gevirtualiseerd/Anders**.
3. Selecteer in **Doelregio** de Azure-regio waarnaar u de machines wilt migreren.
4. Selecteer **Bevestig dat de doelregio voor migratie <regionaam> is**.
5. Klik op **Resources maken**. Hiermee maakt u een Azure Site Recovery-kluis op de achtergrond.
    - Als u migratie al hebt ingesteld met Azure Migrate Server Migration, kunt u deze doeloptie niet configureren, omdat er eerder resources zijn ingesteld.
    - U kunt de doelregio voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Als u uw virtuele machines naar een andere regio wilt migreren, moet u een nieuw/ander Azure Migrate-project maken.

6. Selecteer in **Wilt u een nieuw replicatieapparaat installeren?** **Een replicatieapparaat installeren**.
7. Download in **De software voor het replicatie apparaat downloaden en installeren** het installatieprogramma voor het apparaat en de registratiesleutel. U moet de sleutel opgeven om het apparaat te kunnen registreren. De sleutel blijft na de download vijf dagen geldig.

    ![Provider downloaden](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. Kopieer het installatiebestand voor het apparaat en het sleutelbestand naar de Windows Server 2016-machine of Windows Server 2012 AWS-VM die u voor het replicatieapparaat hebt gemaakt.
9. Voer het installatiebestand voor het replicatieapparaat uit, zoals is beschreven in de volgende procedure.  
    9.1. Selecteer onder **Voordat u begint** de optie **De configuratieserver en processerver installeren**. Selecteer vervolgens **Volgende**.   
    9.2 Selecteer in **Softwarelicentie van derden** de optie **Ik ga akkoord met de licentieovereenkomst van de derde partij**, en selecteer vervolgens **Volgende**.   
    9.3 Selecteer in **Registratie** de optie **Bladeren** en ga naar de locatie waar u het registratiesleutelbestand voor de kluis hebt opgeslagen. Selecteer **Next**.  
    9.4 Selecteer in **Internetinstellingen** de optie **Rechtstreeks verbinding maken met Azure Site Recovery zonder proxyserver**. Selecteer vervolgens **Volgende**.  
    9.5 Op de pagina **Controle op vereisten** worden controles voor verschillende items uitgevoerd. Wanneer dit is voltooid, selecteert u **Volgende**.  
    9.6 Geef in **MySQL-configuratie** een wachtwoord op voor de MySQL-database en selecteer **Volgende**.  
    9.7 Selecteer in **Details van omgeving** de optie **Nee**. U hoeft uw VM's niet te beveiligen. Selecteer vervolgens **Volgende**.  
    9.8 Selecteer in **Installatielocatie** de optie **Volgende** om de standaardinstelling te accepteren.  
    9.9 Selecteer in **Netwerkselectie** de optie **Volgende** om de standaardinstelling te accepteren.  
    9.10 Selecteer in **Samenvatting** de optie **Installeren**.   
    9.11 Bij **Voortgang van de installatie** ziet u informatie over hoe de installatie vordert. Wanneer dit is voltooid, selecteert u **Voltooien**. Er wordt een venster weergegeven met daarin een bericht over opnieuw opstarten. Selecteer **OK**.   
    9.12 Vervolgens wordt een bericht weergegeven over de wachtwoordzin voor de configuratieserververbinding. Kopieer de wachtwoordzin naar het klembord en sla de wachtwoordzin op in een tijdelijk tekst bestand op de bron-VM's. U hebt deze wachtwoordzin later nodig tijdens het installatieproces van de Mobility-service.
10. Wanneer de installatie is voltooid, wordt de wizard Apparaat configureren automatisch gestart (u kunt de wizard ook handmatig starten met behulp van de cspsconfigtool-snelkoppeling die op het bureaublad van het apparaat is gemaakt). Gebruik het tabblad Accounts beheren van de wizard om accountgegevens toe te voegen die moeten worden gebruikt voor de push-installatie van de Mobility-service. In deze zelfstudie wordt de Mobility-service handmatig geïnstalleerd op de te repliceren bron-VM's. Daarom moet u in deze stap een dummyaccount maken en doorgaan.
11. Nadat het apparaat na de installatie opnieuw is opgestart in **Machines detecteren**, selecteert u het nieuwe apparaat in **Configuratieserver selecteren** en klikt u op **Registratie voltooien**. Bij het voltooien van de registratie worden enkele laatste taken uitgevoerd om het replicatieapparaat voor te bereiden.

    ![Registratie voltooien](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="6-install-the-mobility-service"></a>6. De Mobility-service installeren

Er moet een Mobility-serviceagent zijn geïnstalleerd op de bron-AWS-VM's die moeten worden gemigreerd. De installatieprogramma's voor de agent zijn beschikbaar op het replicatieapparaat. Na het vinden van het juiste installatie programma installeert u de agent op elke computer die u wilt migreren. Ga als volgt te werk:

1. Meld u aan bij het replicatieapparaat.
2. Ga naar **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. Zoek het installatieprogramma voor het besturingssysteem en de versie van de bron-AWS-VM's. Controleer [ondersteunde besturingssystemen](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines).
4. Kopieer het installatiebestand naar de bron-AWS-VM die u wilt migreren.
5. U hebt zo dadelijk het tekstbestand met de wachtwoordzin nodig dat is gemaakt tijdens de installatie van het replicatieapparaat.
    - Als u bent vergeten de wachtwoordzin op te slaan, kunt u de wachtwoordzin voor het replicatieapparaat weergeven met deze stap. Voer vanaf de opdrachtregel **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe-v** uit om de huidige wachtwoordzin weer te geven.
    - Kopieer de wachtwoordzin naar het klembord en sla deze op in een tijdelijk tekstbestand op de bron-VM's.

### <a name="installation-guide-for-windows-aws-vms"></a>Installatiehandleiding voor Windows AWS-VM's

1. Pak de inhoud van het installatiebestand als volgt uit naar een lokale map (bijvoorbeeld C:\Temp) op de AWS-VM:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. Voer het installatieprogramma van de Mobility-service uit:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. Registreer de agent bij het replicatieapparaat:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-aws-vms"></a>Installatiehandleiding voor Linux AWS VM's

1. Pak de inhoud van de tarball van het installatieprogramma als volgt uit naar een lokale map (bijvoorbeeld /tmp/MobSvcInstaller) op de AWS-VM:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. Download het script van het installatieprogramma:
    ```
    sudo ./install -r MS -q
    ```  

3. Registreer de agent bij het replicatieapparaat:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="7-enable-replication-for-aws-vms"></a>7. Schakel replicatie in voor AWS-VM's

> [!NOTE]
> Via de portal kunt u maximaal 10 VM's voor replicatie tegelijk toevoegen. Als u meer VM's tegelijk wilt repliceren, kunt u ze toevoegen in batches van 10.

1. In het Azure Migrate-project > **Servers**, **Azure Migrate: Servermigratie** klikt u op **Repliceren**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. Selecteer in **Repliceren** > **Broninstellingen** > **Zijn uw machines gevirtualiseerd?** **Niet gevirtualiseerd/Overige**.
3. In **On-premises apparaat** selecteert u de naam van het Azure Migrate-apparaat dat u instelt.
4. Selecteer in **Processerver** de naam van het replicatieapparaat.
6. In **Gastreferenties** geeft u een dummyaccount op dat wordt gebruikt voor het handmatig installeren van de Mobility-service (pushinstallatie wordt niet ondersteund). Klik vervolgens op **Volgende: Virtuele machines**.

    ![VM's repliceren](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. Houd bij **Virtuele machines** in **Migratie-instellingen importeren uit een evaluatie?** de standaardinstelling **Nee, ik geef de migratie-instellingen handmatig op** aan.
8. Controleer elke virtuele machine die u wilt migreren. Klik vervolgens op **Volgende: Doelinstellingen**.

    ![VM's selecteren](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

9. Selecteer in **Doelinstellingen** het abonnement en de doelregio waarnaar u migreert en geef de resourcegroep op waarin de Azure-VM's na de migratie moeten worden geplaatst.
10. Selecteer in **Virtual Network** het Azure VNet/subnet waaraan de Azure-VM's na migratie worden toegevoegd.
11. In **Azure Hybrid Benefit**:
    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik op **Volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik op **Volgende**.

    ![Doelinstellingen](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **VM-grootte**: Standaard kiest Azure Migrate Server Migration een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**.
    - **Besturingssysteemschijf**: Geef de besturingssysteemschijf (opstarten) voor de VM op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset**: Als de VM na de migratie in een Azure-beschikbaarheidsset moet worden geplaatst, geeft u deze set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![Rekeninstellingen](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Schijfinstellingen](./media/tutorial-migrate-physical-virtual-machines/disks.png)

14. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt de replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie begint, **Beheren** > **Machines repliceren**. De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.

## <a name="8-track-and-monitor-replication-status"></a>8. Traceer en bewaak replicatiestatus

- Wanneer u op **Repliceren** klikt, wordt een taak voor het starten van de replicatie gestart.
- Wanneer deze taak is voltooid, beginnen de VM's hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de deltareplicatie. Incrementele wijzigingen van AWS-VM-schijven worden periodiek gerepliceerd naar de replicaschijven in Azure.

U kunt de taakstatus volgen in de portalmeldingen.

U kunt de replicatiestatus controleren door te klikken op **Replicerende servers** in **Azure Migrate: Server Migration**.  

![Replicatie controleren](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="9-run-a-test-migration"></a>9. Een testmigratie uitvoeren

Wanneer de deltareplicatie begint, kunt u een testmigratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. De testmigratie wordt sterk aanbevolen en biedt de mogelijkheid om mogelijke problemen op te sporen en op te lossen voordat u doorgaat met de daadwerkelijke migratie. U wordt geadviseerd om dit ten minste één keer te doen voor elke VM voordat u deze migreert.

- Bij het uitvoeren van een testmigratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de AWS-VM's - die operationeel blijven - en u door kunt gaan met repliceren.
- Met een testmigratie wordt de migratie gesimuleerd door een Azure-VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-VNet in uw Azure-abonnement).
- U kunt de gerepliceerde Azure-VM gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een testmigratie uit te voeren:

1. In **Migratiedoelen** > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. Selecteer in **Migratie testen** het Azure Vnet waarin de Azure-VM zich na migratie bevindt. We raden u aan geen productie-VNet te gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="10-migrate-aws-vms"></a>10. AWS-VM's migreren

Nadat u hebt geverifieerd dat de testmigratie naar verwachting werkt, kunt u de AWS-VM's migreren.

1. In het Azure Migrate-project > **Servers** > **Azure Migrate: Servermigratie** klikt u op **Servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. In **Migreren** > **Virtuele machines afsluiten en geplande migratie uitvoeren zonder gegevensverlies** selecteert u **Ja** > **OK**.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**.
4. Er wordt een migratietaak gestart voor de VM. U kunt de taakstatus weergeven door rechtsboven op de portalpagina op het belpictogram te klikken of door naar de taakpagina van het hulpprogramma Server Migration te gaan (klik op Overzicht op de tegel van het hulpprogramma > selecteer Taken in het linkermenu).
5. Wanneer de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina Virtuele machines.

### <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechtermuisknop op de VM > **Migratie stoppen**. Er gebeurt nu het volgende:
    - Stopt de replicatie voor de AWS-VM.
    - Verwijdert de AWS-VM uit de telling van **Replicerende servers** in Azure Migrate: Server Migration.
    - De informatie over de replicatiestatus voor de virtuele machine wordt opgeschoond.
2. Installeer de [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)-agent op de gemigreerde computers. De Windows-agent van Azure VM is vooraf geïnstalleerd tijdens het migratieproces.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Leid het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang van binnenkomend verkeer met [Just-in-time-beheer van Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
    - Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.

## <a name="next-steps"></a>Volgende stappen

Onderzoek de [cloudmigratiereis](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud Adoption Framework.

## <a name="troubleshooting--tips"></a>Probleemoplossing/tips

**Vraag:** Ik kan mijn AWS-VM niet zien in de lijst met gedetecteerde servers voor migratie   
**Antwoord:** Controleer of het replicatieapparaat aan de vereisten voldoet. Controleer of de Mobility-agent is geïnstalleerd op de bron-VM die moet worden gemigreerd en of de configuratieserver is geregistreerd. Controleer de netwerkinstellingen en firewallregels om een netwerkpad in te schakelen tussen het replicatieapparaat en de bron-AWS-VM's.  

**Vraag:** Hoe weet ik of mijn virtuele machine is gemigreerd   
**Antwoord:** Na de migratie kunt u de VM bekijken en beheren vanaf de pagina Virtuele machines. Maak verbinding met de gemigreerde VM om te valideren.  

**Vraag:** Ik kan geen VM's importeren voor migratie vanuit mijn eerder gemaakte serverevaluatieresultaten   
**Antwoord:** Op dit moment bieden we geen ondersteuning voor het importeren van evaluatieresultaten voor deze werkstroom. Als tijdelijke oplossing kunt u de evaluatie exporteren en vervolgens handmatig de VM-aanbeveling selecteren tijdens de stap Replicatie inschakelen.
  
**Vraag:** Ik krijg de fout 'Kan BIOS-GUID niet ophalen' tijdens het detecteren van mijn AWS-VM's   
**Antwoord:** Controleer ondersteunde besturingssystemen voor AWS-VM's.  

**Vraag:** Er zit geen voortgang in mijn replicatiestatus    
**Antwoord:** Controleer of het replicatieapparaat aan de vereisten voldoet. Controleer of u op de TCP-poort 9443 en HTTPS 443 van de replicatie-apparaat de vereiste poorten hebt ingeschakeld voor gegevensoverdracht. Controleer of er geen verouderde dubbele versies van het replicatieapparaat zijn verbonden met hetzelfde project.   

**Vraag:** Ik kan geen AWS-instanties detecteren met Azure Migrate als gevolg van een HTTP-statuscode 504 van de externe Windows-beheerservice    
**Antwoord:** Controleer de vereisten van het Azure Migrate-apparaat en de URL-toegangsbehoeften. Zorg ervoor dat er geen proxyinstellingen zijn die de registratie van het apparaat blokkeren.   