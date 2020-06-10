---
title: Microsoft Azure Backup Server voor de Azure VMware-oplossing (AVS) instellen
description: Stel uw Azure VMware-oplossing (AVS)-omgeving in voor het maken van back-ups van virtuele machines met behulp van Microsoft Azure Backup Server.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 23c29f453587ac7a232c21e43fa6fb45193881bc
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613108"
---
# <a name="set-up-microsoft-azure-backup-server-for-avs"></a>Microsoft Azure Backup-Server voor AVS instellen

Microsoft Azure Backup Server is een robuust systeem voor back-up en herstel op bedrijfs niveau dat bijdraagt aan de strategie voor bedrijfscontinuïteit en herstel na nood gevallen (BCDR). Tijdens de preview-versie van AVS kunt u alleen back-ups op virtuele-machine niveau configureren met behulp van Azure Backup Server. 

Azure Backup Server kunnen back-upgegevens opslaan naar:

- **Schijf**: voor kortetermijnbeveiliging-opslag Azure backup server back-ups maken van gegevens naar schijf groepen.

- **Azure**: voor opslag op korte en lange termijn kunt u Azure backup server gegevens die zijn opgeslagen in schijf groepen, met behulp van de Azure backup-service maken van een back-up van de Microsoft Azure Cloud.

Als er storingen optreden en de bron gegevens niet beschikbaar zijn, kunt u Azure Backup Server gebruiken om gegevens eenvoudig te herstellen naar de bron of een andere locatie. Op die manier, als de oorspronkelijke gegevens niet beschikbaar zijn vanwege geplande of onverwachte problemen, kunt u eenvoudig gegevens naar een andere locatie herstellen.

In dit artikel helpen we u bij het voorbereiden van uw AVS-omgeving tot het maken van back-ups van virtuele machines (Vm's) met behulp van Azure Backup Server.  We begeleiden u door de volgende stappen: 

> [!div class="checklist"]
> * Bepaal het aanbevolen type schijf en de grootte van de virtuele machine die u wilt gebruiken
> * Een Recovery Services kluis maken waarin de herstel punten worden opgeslagen
> * De opslag replicatie voor een Recovery Services kluis instellen
> * Opslag toevoegen aan Azure Backup Server

## <a name="supported-vmware-features"></a>Ondersteunde VMware-functies

- **Back-up zonder agent:** Azure Backup Server geen agent moet worden geïnstalleerd op de vCenter-of ESXi-server om een back-up van de virtuele machine te maken. Geef in plaats daarvan alleen het IP-adres of de Fully Qualified Domain Name (FQDN) op en aanmeldings referenties die worden gebruikt voor het verifiëren van de VMware-Server met Azure Backup Server.

- **Met de Cloud geïntegreerde back-up:** Azure Backup Server beveiligt workloads op schijf en in de Cloud. De werk stroom voor back-up en herstel van Azure Backup Server helpt u bij het beheren van lange termijn retentie en externe back-ups.

- **Vm's detecteren en beveiligen die worden beheerd door vCenter:** Azure Backup Server detecteert en beveiligt Vm's die op een vCenter-of ESXi-server zijn geïmplementeerd. Azure Backup Server detecteert ook Vm's die worden beheerd door vCenter, zodat u grote implementaties kunt beveiligen.

- **Automatische beveiliging op mapniveau:** met vCenter kunt u uw virtuele machines in VM-mappen ordenen. Azure Backup Server detecteert deze mappen en stelt u in staat om Vm's op mapniveau te beschermen en alle submappen te bevatten. Bij het beveiligen van mappen beschermt Azure Backup Server niet alleen de virtuele machines in die map, maar beveiligt u ook de virtuele machines die later worden toegevoegd. Azure Backup Server detecteert dagelijks nieuwe Vm's en beveiligt deze automatisch. Bij het indelen van uw Vm's in recursieve mappen, Azure Backup Server automatisch detecteert en beveiligt de nieuwe Vm's die in de recursieve mappen zijn geïmplementeerd.

- **Azure backup server blijft VMotioned vm's in het cluster beveiligen:** Als Vm's vMotioned zijn voor taak verdeling binnen het cluster, detecteert Azure Backup Server automatisch de VM-beveiliging.

- **Nood zakelijke bestanden sneller herstellen:** Azure Backup Server kunt bestanden/mappen van een Windows-VM herstellen zonder de volledige VM te herstellen.

## <a name="limitations"></a>Beperkingen

- Update pakket 1 voor Azure Backup Server v3 moet zijn geïnstalleerd.

- U kunt geen back-up maken van moment opnamen van de gebruiker voor de eerste Azure Backup Server back-up. Als Azure Backup Server de eerste back-up hebt voltooid, kunt u back-ups maken van gebruikers momentopnamen.

- Azure Backup Server kan geen virtuele VMware-machines beveiligen met doorgangs schijven en fysieke onbewerkte apparaattoewijzing (pRDM).

- Azure Backup Server kan VMware-vApps niet detecteren of beveiligen.

**Als u Microsoft Azure Backup Server voor de Azure VMware-oplossing (AVS) wilt instellen, moet u de volgende stappen uitvoeren:**

- De vereisten en omgeving instellen

- Azure Recovery Services-kluis maken

- Down load en installeer de Azure Backup Server 

- Opslag toevoegen aan Azure Backup Server 

**Implementatie architectuur**  
Microsoft Azure Backup Server wordt geïmplementeerd als Azure IaaS VM om AVS-Vm's te beveiligen.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Architectuur van AVS-implementatie" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Vereisten voor de Azure Backup Server omgeving

Bekijk de aanbevelingen in deze sectie bij het installeren van de Azure Backup Server in uw Azure-omgeving.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Zorg ervoor dat u [netwerken configureert voor uw VMware privécloud in azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>De grootte van de virtuele machine bepalen

U moet een virtuele Windows-machine maken in het virtuele netwerk dat u in de vorige stap hebt gemaakt. Wanneer u een server kiest voor het uitvoeren van Azure Backup Server, is het raadzaam om te beginnen met een galerie-afbeelding van Windows Server 2019 Data Center. Met de zelf studie [uw eerste virtuele Windows-Azure Portal machine maken](../virtual-machines/windows/quick-create-portal.md) kunt u aan de slag met de aanbevolen vm in azure, zelfs als u Azure nog nooit hebt gebruikt.

De volgende tabel bevat een overzicht van het maximum aantal beveiligde werk belastingen voor elke Azure Backup Server grootte van de virtuele machine. De informatie is gebaseerd op interne prestatie- en schaaltests met canonieke waarden voor de grootte van de werkbelasting en het verloop. De werkelijke werkbelasting grootte kan groter zijn, maar moet voldoen aan de schijven die zijn gekoppeld aan de virtuele machine van Azure Backup Server.

| Maximum aantal beveiligde werkbelastingen | Gemiddelde werkbelastingsgrootte | Gemiddeld werkbelastingsverloop (dagelijks) | Min. aantal opslag-IOPS | Aanbevolen schijf type/-grootte      | Aanbevolen VM-grootte |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Net 5% verloop                   | 2000             | Standard-HDD (8 TB of hogere grootte per schijf)  | A4V2       |
| 40                      | 150 GB                | Net 10% verloop                  | 4500             | Premium-SSD * (1 TB of hogere grootte per schijf) | DS3_V2     |
| 60                      | 200 GB                | Net 10% verloop                  | 10500            | Premium-SSD * (8 TB of hogere grootte per schijf) | DS3_V2     |

* Als u de vereiste IOPs wilt ophalen, gebruikt u minimale aanbevolen of schijven met een grotere grootte. Schijven met een kleinere grootte bieden een lagere IOPs.

> [!NOTE]
> Azure Backup Server is ontworpen om te worden uitgevoerd op een toegewezen server met één doel. U kunt Azure Backup Server niet installeren op een computer:
> * Wordt uitgevoerd als een domein controller
> * Is de functie toepassings server geïnstalleerd
> * Dat is een System Center Operations Manager-beheer server
> * Exchange Server wordt uitgevoerd
> * Dit is een knoop punt van een cluster

### <a name="disks-and-storage"></a>Schijven en opslag

Azure Backup Server vereist schijven voor de installatie, inclusief systeem bestanden, installatie bestanden, vereiste software, database bestanden en speciale schijven voor de opslag groep.

| Vereiste                      | Aanbevolen grootte  |
|----------------------------------|-------------------------|
| Installatie van Azure Backup Server                | Installatie locatie: 3 GB<br />Station voor databasebestanden: 900 MB<br />Systeem station: 1 GB voor SQL-installatie<br /><br />Daarnaast hebt u ruimte nodig voor het Azure Backup Server kopiëren van de bestands catalogus naar een tijdelijke installatie locatie tijdens het archiveren.      |
| Schijf voor opslaggroep<br />(Hierbij worden standaard volumes gebruikt, kan dit geen dynamische schijf zijn.) | 2 tot 3 keer de grootte van de beveiligde gegevens<br />Raadpleeg [DPM capacity planner](https://www.microsoft.com/download/details.aspx?id=54301)voor gedetailleerde opslag berekening.   |

De [een beheerde gegevens schijf koppelen aan een virtuele Windows-machine met behulp van de Azure Portal](../virtual-machines/windows/attach-managed-disk-portal.md) artikelen laten zien hoe u een nieuwe beheerde gegevens schijf koppelt aan een bestaande virtuele machine van Azure.

> [!NOTE]
> Een enkele Azure Backup Server heeft een zachte limiet van 120 TB voor de opslag groep.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Back-upgegevens opslaan op een lokale schijf en in azure

Het opslaan van back-upgegevens in azure reduceert de back-upinfrastructuur op Azure Backup Server VM. Azure Backup Server back-upgegevens op Azure-schijven die zijn gekoppeld aan de VM, worden opgeslagen voor operationele herstel (back-up). Zodra de schijven en opslag ruimte aan de virtuele machine zijn gekoppeld, beheert Azure Backup Server de opslag voor u. De hoeveelheid back-upgegevens opslag is afhankelijk van het aantal en de grootte van de schijven die zijn gekoppeld aan elke virtuele machine van Azure en elke grootte van de virtuele machine van Azure heeft een maximum aantal schijven dat kan worden bijgevoegd. A2 is bijvoorbeeld vier schijven. A3 is acht schijven. A4 is 16 schijven. De grootte en het aantal schijven bepalen de totale capaciteit van de back-upopslaggroep.

> [!IMPORTANT]
> U moet operationele herstel gegevens **niet** langer dan vijf dagen bewaren op schijven die zijn gekoppeld aan Azure backup server. Als gegevens meer dan vijf dagen oud zijn, slaat u deze op in azure Recovery Services kluis.

Om back-upgegevens op te slaan in azure, maakt of gebruikt u een Recovery Services kluis. Wanneer u een back-up van de Azure Backup Server workload wilt voorbereiden, [configureert u de Recovery Services kluis](#create-a-recovery-services-vault). Zodra een online back-uptaak is geconfigureerd, wordt een herstel punt in de kluis gemaakt. Elke Recovery Services kluis bevat Maxi maal 9999 herstel punten. Afhankelijk van het aantal gemaakte herstel punten en hoe lang ze bewaard blijven, kunt u back-upgegevens gedurende een aantal jaren bewaren. U kunt bijvoorbeeld maandelijkse herstel punten maken en deze gedurende vijf jaar bewaren.

> [!IMPORTANT]
> Of u back-upgegevens naar Azure verzendt of lokaal blijft, u moet Azure Backup Server registreren bij een Recovery Services kluis.

### <a name="scale-deployment"></a>Implementatie schalen

Als u de schaal van uw implementatie wilt aanpassen, hebt u de volgende opties:

- **Omhoog schalen** : Verhoog de grootte van de Azure Backup Server VM van een serie naar DS3-serie en verg root de lokale opslag.

- **Offload data** : Verstuur oudere gegevens naar Azure en behoud alleen de nieuwste gegevens op de opslag die is gekoppeld aan de Azure backup server.

- **Scale-out** : voeg meer Azure backup servers toe om de werk belastingen te beveiligen.

### <a name="net-framework"></a>.NET Framework

Op de VM moet .NET Framework 3,5 SP1 of hoger zijn geïnstalleerd.

### <a name="join-a-domain"></a>Lid worden van een domein

De Azure Backup Server virtuele machine moet lid zijn van een domein en een domein gebruiker met beheerders bevoegdheden op de VM moet Azure Backup Server installeren.

Hoewel er op het moment van de preview-versie niet wordt ondersteund, kan Azure Backup Server geïmplementeerd in een Azure-VM back-ups maken van werk belastingen op de virtuele machines in AVS, maar ze moeten zich in hetzelfde domein bevinden om back-upbewerking in te scha kelen.

## <a name="create-a-recovery-services-vault"></a>Een Recovery Services-kluis maken

Een Recovery Services kluis is een opslag entiteit waarin de herstel punten worden opgeslagen die in de loop van de tijd zijn gemaakt. Het bevat ook een back-upbeleid dat is gekoppeld aan beveiligde items.

1. Meld u aan bij uw abonnement in [Azure Portal](https://portal.azure.com/).

1. Selecteer in het menu links **alle services**.

   ![Alle services selecteren](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Voer in het dialoog venster **alle services** het *Recovery Services* in en selecteer **Recovery Services kluizen** in de lijst.

   ![Recovery Services-kluizen invoeren en kiezen](../backup/media/backup-create-rs-vault/all-services.png)

   De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

1. Selecteer **toevoegen**op het **Recovery Services kluizen** -dash board.

   ![Een Recovery Services kluis toevoegen](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   Het dialoogvenster **Recovery Service-kluis** wordt geopend.

1. Geef waarden op voor de **naam**, het **abonnement**, de **resource groep**en de **locatie**.

   ![De Recovery Services kluis configureren](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Naam**: Voer een beschrijvende naam in om de kluis aan te duiden. De naam moet uniek zijn voor het Azure-abonnement. Geef een naam op met ten minste twee, maar niet meer dan 50 tekens. De naam moet beginnen met een letter en mag alleen uit letters, cijfers en afbreekstreepjes bestaan.

   - **Abonnement**: Kies het abonnement dat u wilt gebruiken. Als u lid bent van maar één abonnement, ziet u die naam. Als u niet zeker weet welk abonnement u moet gebruiken, gebruikt u het standaardabonnement (voorgesteld). Er zijn alleen meerdere mogelijkheden als uw werk- of schoolaccount is gekoppeld aan meerdere Azure-abonnementen.

   - **Resourcegroep**: Gebruik een bestaande resourcegroep of maak een nieuwe. Als u de lijst met beschik bare resource groepen in uw abonnement wilt weer geven, selecteert u **bestaande gebruiken**en selecteert u een resource in de vervolg keuzelijst. Als u een nieuwe resourcegroep wilt maken, selecteert u **Nieuwe maken** en voert u de naam in.

   - **Locatie**: Selecteer de geografische regio voor de kluis. De kluis *moet* zich in dezelfde regio bevinden als de privécloud van de AVS om een kluis te maken om de virtuele AVS-machines te beveiligen.

1. Wanneer u klaar bent om de Recovery Services kluis te maken, selecteert u **maken**.

   ![De Recovery Services kluis maken](../backup/media/backup-create-rs-vault/click-create-button.png)

   Het kan even duren om de Recovery Services kluis te maken. Bewaak de status meldingen in het gebied **meldingen** in de rechter bovenhoek van de portal. Nadat de kluis is gemaakt, wordt deze weer gegeven in de lijst met Recovery Services kluizen. Als uw kluis niet wordt weer geven, selecteert u **vernieuwen**.

   ![De lijst met back-upkluizen vernieuwen](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Opslagreplicatie instellen

Met de optie voor opslag replicatie kunt u kiezen tussen geo-redundante opslag (de standaard instelling) en lokaal redundante opslag. Met geografisch redundante opslag worden de gegevens in uw opslag account gekopieerd naar een secundaire regio, waardoor uw gegevens duurzaam zijn. Lokaal redundante opslag is een goedkopere optie die niet zo duurzaam is. Meer informatie over de opties voor geografisch redundante en lokaal redundante opslag vindt u in de [Azure Storage redundantie](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Het wijzigen van het **type opslag replicatie** (lokaal redundant/geo-redundant) voor een Recovery Services-kluis moet worden uitgevoerd voordat u back-ups in de kluis configureert. Zodra u een back-up hebt geconfigureerd, is de optie om deze te wijzigen uitgeschakeld en kunt u het **type opslag replicatie**niet meer wijzigen.

1. Klik in **Recovery Services kluizen**op de nieuwe kluis. 

1. Onder **instellingen**, selecteert u **Eigenschappen**en klikt u onder **back-upconfiguratie**op **bijwerken**.

1. Selecteer het type opslag replicatie en klik op **Opslaan**.

   ![De opslagconfiguratie voor nieuwe kluis instellen](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-software-package"></a>Software pakket downloaden en installeren

### <a name="downloading-the-software-package"></a>Het software pakket downloaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Als er al een Recovery Services kluis is geopend, gaat u verder met de volgende stap. Als er geen Recovery Services kluis is geopend, maar wel in de Azure Portal, klikt u in het hoofd menu op **Bladeren**.

   1. Typ in de lijst met resources **Recovery Services**.

   1. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Wanneer **Recovery Services-kluizen** wordt weergegeven, klikt u erop.

   ![Een Recovery Services-kluis maken, stap 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Selecteer in de lijst met Recovery Services-kluizen een kluis.

   Het geselecteerde kluisdashboard wordt geopend.

   ![Blade Kluis openen](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   De **instellingen** worden standaard geopend. Als deze optie is gesloten, selecteert u **instellingen** om deze te openen.

   ![Blade Kluis openen](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Klik op **back-up** om de wizard aan de slag te openen.

   ![Back-up aan de slag](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Ga als volgt te werk in het venster dat wordt geopend:

   1. Selecteer **on-premises**in het menu **waar is de werk belasting die wordt uitgevoerd** .

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Waar wordt uw werk belasting uitgevoerd?":::

   1. Selecteer in het menu **waarover wilt u een back** -up maken de werk belastingen die u wilt beveiligen met Azure backup server.

   1. Klik op **infra structuur voorbereiden** om Azure backup server en de kluis referenties te downloaden en te installeren.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Infra structuur voorbereiden":::

1. Ga als volgt te werk in het venster **infra structuur voorbereiden** dat wordt geopend:

   1. Klik op de **Download** koppeling om Azure backup server te installeren.

   1. Down load de kluis referenties door het selectie vakje **al gedownload of met de nieuwste Azure backup server installatie** te selecteren in en klik vervolgens op **downloaden**. U gebruikt de kluis referenties tijdens de registratie van Azure Backup Server aan de Recovery Services-kluis. Met de koppelingen gaat u naar het Download centrum, waar u het software pakket downloadt.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Infra structuur voorbereiden-Azure Backup Server":::

1. Selecteer op de pagina downloaden alle bestanden en klik op **volgende**.

   > [!NOTE]
   > U moet alle bestanden naar dezelfde map downloaden.  Omdat de download grootte van de bestanden samen > 3 GB is, kan het tot 60 minuten duren voordat de down load is voltooid. 

   ![Down load Center 1](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extracting-the-software-package"></a>Het software pakket extra heren

Als u het software pakket naar een andere server hebt gedownload, kopieert u de bestanden naar de virtuele machine die u hebt gemaakt om de Azure Backup Server te implementeren.

> [!WARNING]
> Er is ten minste 4 GB beschik bare ruimte nodig om de Setup-bestanden uit te pakken.

1. Nadat u alle bestanden hebt gedownload, dubbelklikt u op **MicrosoftAzureBackupInstaller. exe** om de **wizard Microsoft Azure Backup Setup** te openen en klikt u vervolgens op **volgende**.

1. Selecteer de locatie waar u de bestanden wilt extra heren en klik op **volgende**.

1. Klik op **extra heren** om het extractie proces te starten.

   ![Wizard Microsoft Azure Backup-installatie](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Zodra het bestand is geëxtraheerd, selecteert u de optie voor het **uitvoeren van Setup. exe** en klikt u vervolgens op **volt ooien**.

> [!TIP]
> U kunt ook het bestand Setup. exe vinden in de map waar u het software pakket hebt uitgepakt.

### <a name="installing-the-software-package"></a>Het software pakket installeren

1. Klik in het venster Setup onder installeren op **Microsoft Azure backup** om de installatie wizard te openen.

   ![Wizard Microsoft Azure Backup-installatie](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Klik in het welkomst scherm op **volgende** om door te gaan met de controles op vereisten.

1. Klik op **controleren** om te bepalen of aan de hardware-en software vereisten voor Azure backup server wordt voldaan. Klik op **volgende**als u dit hebt bereikt.

   ![Azure Backup Server-welkom en vereisten controleren](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Het Azure Backup Server-installatie pakket wordt geleverd met de juiste SQL Server binaire bestanden die nodig zijn. Als u een nieuwe Azure Backup Server-installatie wilt starten, selecteert u de optie **nieuw exemplaar van SQL Server installeren met deze setup** en klikt u op **controleren en installeren**.

   ![Azure Backup Server-SQL-controle](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Als u uw eigen SQL Server wilt gebruiken, zijn de ondersteunde SQL Server versies SQL Server 2014 SP1 of hoger, 2016 en 2017. Alle SQL Server versies moeten Standard of ENTER prise 64-bit zijn. Azure Backup Server werkt niet met een extern SQL Server exemplaar. Het exemplaar dat door Azure Backup Server wordt gebruikt, moet lokaal zijn. Als u een bestaande SQL Server voor Azure Backup Server gebruikt, ondersteunt de installatie alleen het gebruik van *benoemde exemplaren* van SQL Server.

   Als er een fout optreedt met een aanbeveling om de computer opnieuw op te starten, moet u dit doen en op **controleren**klikken. Als er SQL-configuratie problemen zijn, moet u SQL opnieuw configureren volgens de SQL-richt lijnen en opnieuw proberen om Azure Backup Server te installeren of bij te werken met behulp van het bestaande exemplaar van SQL.

   **Handmatige configuratie**

   Wanneer u uw eigen exemplaar van SQL gebruikt, moet u builtin\Administrators toevoegen aan de rol sysadmin voor de hoofd database.

   **SSRS-configuratie met SQL 2017**

   Wanneer u uw eigen exemplaar van SQL 2017 gebruikt, moet u SSRS hand matig configureren. Controleer na de configuratie van SSRS of de eigenschap *IsInitialized* van SSRS is ingesteld op *True*. Als deze waarde is ingesteld op True, wordt ervan uitgegaan dat SSRS al is geconfigureerd en wordt de SSRS-configuratie overgeslagen.

   Als u de configuratie status van SSRS wilt controleren, voert u de volgende opdracht uit:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Gebruik de volgende waarden voor de SSRS-configuratie:
   * Service account: ' ingebouwd account gebruiken ' moet netwerk service zijn
   * URL van webservice: ' virtuele map ' moet worden ReportServer_\<SQLInstanceName>
   * Data Base: DATABASENAME moet Report Server $\<SQLInstanceName>
   * URL van webportal: ' virtuele map ' moet worden Reports_\<SQLInstanceName>

   Meer [informatie](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) over de configuratie van SSRS.

   > [!NOTE]
   > [Micro soft Online Services-voor waarden](https://www.microsoft.com/licensing/product-licensing/products) (OST) bepaalt de licentie voor SQL Server gebruikt als de data base voor Azure backup server. Volgens OST kunnen SQL Server gebundeld met Azure Backup Server alleen worden gebruikt als de Data Base voor Azure Backup Server.

1. Klik op **volgende**om de installatie te vervolledigen.

1. Geef een locatie op voor de installatie van Microsoft Azure Backup Server-bestanden en klik op **volgende**.

   > [!NOTE]
   > De Scratch locatie is vereist voor het maken van een back-up naar Azure. Zorg ervoor dat de Scratch locatie ten minste 5% van de gegevens bevindt waarvan een back-up naar de Cloud wordt gepland. Voor schijf beveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie [opslag groepen en schijf opslag configureren](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12))voor meer informatie over opslag groepen.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Geef een sterk wacht woord op voor beperkte lokale gebruikers accounts en klik op **volgende**.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Selecteer of u Microsoft Update wilt gebruiken om te controleren op updates en klik op **volgende**.

   > [!NOTE]
   > U kunt het beste Windows Update omleiden naar Microsoft Update, waarmee beveiligings-en belang rijke updates voor Windows en andere producten zoals Microsoft Azure Backup Server worden geboden.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Bekijk het *overzicht van instellingen* en klik op **installeren**.

   De installatie vindt plaats in fasen. De eerste fase installeert de Microsoft Azure Recovery Services agent en de tweede fase controleert op Internet verbinding. Als Internet connectiviteit beschikbaar is, kunt u door gaan met de installatie. Als dat niet het geval is, moet u proxy gegevens opgeven om verbinding te maken met internet. In de laatste fase wordt de vereiste software gecontroleerd en als dit niet is geïnstalleerd, wordt de ontbrekende software samen met de Microsoft Azure Recovery Services agent geïnstalleerd.

1. Klik op **Bladeren** om uw kluis referenties te zoeken om de computer te registreren bij de Recovery Services kluis en klik vervolgens op **volgende**.

1. Kies een wachtwoordzin voor het versleutelen/ontsleutelen van de gegevens die tussen Azure en uw locatie worden verzonden.

   > [!TIP]
   > U kunt automatisch een wachtwoordzin genereren of uw eigen minimale 16-teken wachtwoordzin opgeven.

1. Geef de locatie op waar u de wachtwoordzin wilt opslaan en klik vervolgens op **volgende** om de server te registreren.

   > [!IMPORTANT]
   > Het is belang rijk om ook de wachtwoordzin op een veilige locatie op te slaan, behalve de lokale server. Micro soft raadt u ten zeerste aan een Azure Key Vault te gebruiken voor het opslaan van de wachtwoordzin.

   Nadat de Microsoft Azure Recovery Services-agent is geïnstalleerd, wordt deze verplaatst naar de installatie en configuratie van SQL Server en de Azure Backup Server onderdelen.

   ![Azure Backup-server](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Zodra de installatie stap is voltooid, klikt u op **sluiten**.

### <a name="install-update-rollup-1"></a>Update pakket 1 installeren

Het installeren van update pakket 1 voor Microsoft Azure Backup Server v3 is verplicht voordat u de werk belastingen beveiligt. Zie KB-artikel [4534062](https://support.microsoft.com/en-us/help/4534062/)als u de lijst met fout oplossingen en de installatie-instructies voor Microsoft Azure Backup Server v3 UR1 wilt weer geven.

## <a name="add-storage-to-azure-backup-server"></a>Opslag toevoegen aan Azure Backup Server

Azure Backup Server v3 ondersteunt Modern Backup Storage die het volgende biedt:

-  Besparing van de opslag van 50%

-  Back-ups die drie keer sneller zijn

-  Efficiëntere opslag

-  Werkbelasting bewuste opslag

### <a name="volumes-in-backup-server"></a>Volumes in back-upserver

Voeg de gegevens schijven met de vereiste opslag capaciteit toe aan de virtuele machine van de Azure Backup Server als deze nog niet is toegevoegd.

Backup Server v3 accepteert alleen opslag volumes. Wanneer u een volume toevoegt, formatteert de back-upserver het volume naar een ReFS-bestands systeem dat Modern Backup Storage vereist.

### <a name="add-volumes-to-backup-server-disk-storage"></a>Volumes toevoegen aan de back-upserver schijf opslag

1. Scan in het deel venster **beheer** de opslag opnieuw en selecteer vervolgens **toevoegen**. 

1. Selecteer een van de beschik bare volumes om toe te voegen aan de opslag groep. 

1. Nadat de beschik bare volumes zijn toegevoegd, geeft u deze een beschrijvende naam waarmee u ze kunt beheren. 

1. Klik op **OK** om deze volumes op ReFS op te maken zodat de back-upserver de voor delen van modern Backup Storage kan gebruiken.

![Beschik bare volumes toevoegen](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Volgende stappen

Ga door naar de volgende zelf studie voor meer informatie over het configureren van een back-up van virtuele VMware-machines die worden uitgevoerd op de Azure VMware-oplossing (AVS) met Azure Backup Server.

> [!div class="nextstepaction"]
> [Back-ups van AVS-Vm's configureren](backup-avs-vms-with-mabs.md)

