---
title: Azure Backup Server installeren op Azure Stack | Microsoft Docs
description: In dit artikel vindt u informatie over het gebruik van Azure Backup Server voor het beveiligen of maken van een back-up van werk belastingen in Azure Stack.
author: dcurwin
manager: carmonm
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: dacurwin
ms.openlocfilehash: bdcd7cbd24ca7023070585df46aa8cea7bdc70eb
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747286"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Backup Server installeren op Azure Stack

In dit artikel wordt uitgelegd hoe u Azure Backup Server installeert op Azure Stack. Met Azure Backup Server kunt u IaaS-workloads (Infrastructure as a Service) beveiligen, zoals virtuele machines die in Azure Stack worden uitgevoerd. Een voor deel van het gebruik van Azure Backup Server voor het beveiligen van uw workloads is dat u alle werkbelasting beveiliging kunt beheren vanuit één console.

> [!NOTE]
> Raadpleeg de [documentatie over Azure backup beveiligings functies](backup-azure-security-feature.md)voor meer informatie over de beveiligings mogelijkheden.
>

## <a name="azure-backup-server-protection-matrix"></a>Beveiligingsmatrix voor Azure Backup Server

Azure Backup Server beveiligt de volgende Azure Stack werk belasting van virtuele machines.

| Beveiligde gegevens bron | Beveiliging en herstel |
| --------------------- | ----------------------- |
| Windows Server Semi-Annual-kanaal-Data Center/Enter prise/Standard | Volumes, bestanden, mappen |
| Windows Server 2016-Data Center/Enter prise/Standard | Volumes, bestanden, mappen |
| Windows Server 2012 R2-Data Center/Enter prise/Standard | Volumes, bestanden, mappen |
| Windows Server 2012-Data Center/Enter prise/Standard | Volumes, bestanden, mappen |
| Windows Server 2008 R2-Data Center/Enter prise/Standard | Volumes, bestanden, mappen |
| SQL Server 2016 | Database |
| SQL Server 2014 | Database |
| SQL Server 2012 SP1 | Database |
| Share point 2016 | Farm, Data Base, front-end, webserver |
| SharePoint 2013 | Farm, Data Base, front-end, webserver |
| Share point 2010 | Farm, Data Base, front-end, webserver |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Vereisten voor de Azure Backup Server omgeving

Bekijk de aanbevelingen in deze sectie wanneer u Azure Backup Server installeert in uw Azure Stack omgeving. Het Azure Backup Server-installatie programma controleert of uw omgeving voldoet aan de vereisten, maar u bespaart tijd door u voor te bereiden voordat u de installatie uitvoert.

### <a name="determining-size-of-virtual-machine"></a>Grootte van virtuele machine bepalen

Als u Azure Backup Server wilt uitvoeren op een Azure Stack virtuele machine, gebruikt u de grootte a2 of groter. Voor hulp bij het kiezen van de grootte van een virtuele machine downloadt u de [Azure stack VM-grootte Calculator](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Virtuele netwerken op Azure Stack virtuele machines

Alle virtuele machines die worden gebruikt in een Azure Stack workload, moeten behoren tot hetzelfde virtuele Azure-netwerk en Azure-abonnement.

### <a name="azure-backup-server-vm-performance"></a>VM-prestaties Azure Backup Server

Als het wordt gedeeld met andere virtuele machines, worden de grootte van het opslag account en de IOPS beperkt Azure Backup Server de prestaties van de virtuele machine. Daarom moet u een afzonderlijk opslag account voor de virtuele machine van Azure Backup Server gebruiken. De Azure Backup-agent die op de Azure Backup Server wordt uitgevoerd, heeft tijdelijke opslag nodig voor:

- eigen gebruik (een cache locatie),
- gegevens die zijn hersteld vanuit de Cloud (lokaal faserings gebied)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure Backup tijdelijke schijf opslag configureren

Elke Azure Stack virtuele machine wordt geleverd met tijdelijke schijf opslag, die beschikbaar is voor de gebruiker als volume `D:\`. Het lokale faserings gebied dat door Azure Backup nodig is, kan zodanig worden geconfigureerd dat het zich in `D:\`bevindt en de cache locatie kan op `C:\`worden geplaatst. Op deze manier moet er geen opslag gehaald worden verwijderd van de gegevens schijven die zijn gekoppeld aan de virtuele machine van Azure Backup Server.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Back-upgegevens opslaan op een lokale schijf en in azure

Azure Backup Server back-upgegevens opgeslagen op Azure-schijven die zijn gekoppeld aan de virtuele machine, voor operationeel herstel. Zodra de schijven en opslag ruimte aan de virtuele machine zijn gekoppeld, beheert Azure Backup Server opslag voor u. De hoeveelheid back-upgegevens opslag is afhankelijk van het aantal en de grootte van de schijven die aan elke [Azure stack virtuele machine](/azure-stack/user/azure-stack-storage-overview)zijn gekoppeld. Elke grootte van Azure Stack VM heeft een maximum aantal schijven dat aan de virtuele machine kan worden gekoppeld. A2 is bijvoorbeeld vier schijven. A3 is acht schijven. A4 is 16 schijven. De grootte en het aantal schijven bepalen de totale opslag groep voor back-ups.

> [!IMPORTANT]
> Bewaar op Azure Backup Server gekoppelde schijven meer dan vijf dagen **geen** back-upgegevens.
>

Het opslaan van back-upgegevens in azure vermindert de back-upinfrastructuur van Azure Stack. Als gegevens meer dan vijf dagen oud zijn, moeten deze worden opgeslagen in Azure.

Om back-upgegevens op te slaan in azure, maakt of gebruikt u een Recovery Services kluis. Wanneer u een back-up van de Azure Backup Server workload wilt voorbereiden, [configureert u de Recovery Services kluis](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Zodra een back-uptaak wordt uitgevoerd, wordt er een herstel punt in de kluis gemaakt. Elke Recovery Services kluis bevat Maxi maal 9999 herstel punten. Afhankelijk van het aantal gemaakte herstel punten en hoe lang ze bewaard blijven, kunt u back-upgegevens gedurende een aantal jaren bewaren. U kunt bijvoorbeeld maandelijkse herstel punten maken en deze gedurende vijf jaar bewaren.

### <a name="scaling-deployment"></a>Implementatie schalen

Als u de schaal van uw implementatie wilt aanpassen, hebt u de volgende opties:

- Omhoog schalen: Verhoog de grootte van de Azure Backup Server virtuele machine van een serie naar D-serie en verg root de lokale opslag [volgens de instructies voor de Azure stack virtuele machine](/azure-stack/user/azure-stack-manage-vm-disks).
- Offload data: Verstuur oudere gegevens naar Azure en behoud alleen de nieuwste gegevens op de opslag die is gekoppeld aan de Azure Backup Server.
- Uitschalen: Voeg meer Azure Backup servers toe om de werk belastingen te beveiligen.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3,5 SP1 of hoger moet zijn geïnstalleerd op de virtuele machine.

### <a name="joining-a-domain"></a>Lid worden van een domein

De Azure Backup Server virtuele machine moet lid zijn van een domein. Een domein gebruiker met beheerders bevoegdheden moet Azure Backup Server installeren op de virtuele machine.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Een IaaS-VM gebruiken in Azure Stack

Wanneer u een server voor Azure Backup Server kiest, start u met een installatie kopie van Windows Server 2012 R2 Data Center of Windows Server 2016 Data Center. In het artikel [maakt u uw eerste virtuele Windows-machine in de Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Hier volgt een zelf studie om aan de slag te gaan met de aanbevolen virtuele machine. De aanbevolen minimum vereisten voor de virtuele machine van de server (VM) moeten zijn: a2 Standard met twee kernen en 3,5 GB RAM-geheugen.

Het beveiligen van werk belastingen met Azure Backup Server heeft veel nuances. Het artikel [Installeer DPM als een virtuele machine van Azure](https://technet.microsoft.com/library/jj852163.aspx), en helpt deze nuances te verklaren. Lees dit artikel volledig voordat u de computer implementeert.

> [!NOTE]
> Azure Backup Server is ontworpen om te worden uitgevoerd op een specifieke virtuele machine met één doel. U kunt Azure Backup Server niet installeren op:
>
> - Een computer die wordt uitgevoerd als een domeincontroller
> - Een computer waarop de toepassingsserverfunctie is geïnstalleerd
> - Een computer waarop Exchange Server wordt uitgevoerd
> - Een computer die een knooppunt van een cluster is

Voeg Azure Backup Server altijd toe aan een domein. Als u Azure Backup Server naar een ander domein wilt verplaatsen, installeert u eerst Azure Backup Server en voegt u deze vervolgens toe aan het nieuwe domein. Wanneer u Azure Backup Server implementeert, kunt u het niet verplaatsen naar een nieuw domein.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Opslagreplicatie instellen

Met de optie voor opslag replicatie van Recovery Services kluis kunt u kiezen tussen geografisch redundante opslag en lokaal redundante opslag. Recovery Services kluizen gebruiken standaard geografisch redundante opslag. Als deze kluis uw primaire kluis is, houdt u de opslag optie ingesteld op geografisch redundante opslag. Kies lokaal redundante opslag als u een goedkopere optie wilt die minder duurzaam is. U vindt meer informatie over de opties voor [geografisch redundante](../storage/common/storage-redundancy-grs.md) en [lokaal redundante](../storage/common/storage-redundancy-lrs.md) opslag in het [overzicht van Azure Storage-replicatie](../storage/common/storage-redundancy.md).

De instelling voor opslagreplicatie bewerken:

1. Selecteer uw kluis om het dash board kluis en het menu instellingen te openen. Als het menu **instellingen** niet wordt geopend, klikt u op **alle instellingen** in het kluis dashboard.
2. Klik in het menu **instellingen** op **back-upinfrastructuur** > **back-upconfiguratie** om het menu **back-up configuratie** te openen. Kies in het menu **back-upconfiguratie** de optie voor opslag replicatie voor uw kluis.

    ![Lijst met back-upkluizen](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Azure Backup Server-installatie programma downloaden

Er zijn twee manieren om het Azure Backup Server-installatie programma te downloaden. U kunt het Azure Backup Server-installatie programma downloaden van het [micro soft Download centrum](https://www.microsoft.com/en-us/download/details.aspx?id=55269). U kunt Azure Backup Server Installer ook downloaden tijdens het configureren van een Recovery Services kluis. De volgende stappen helpen u bij het downloaden van het installatie programma van de Azure Portal tijdens het configureren van een Recovery Services kluis.

1. Meld u vanaf uw Azure Stack virtuele machine [aan bij uw Azure-abonnement in de Azure Portal](https://portal.azure.com/).
2. Selecteer in het menu links **alle services**.

    ![Kies de optie alle services in het hoofd menu](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. Typ *Recovery Services*in het dialoog venster **alle services** . Wanneer u begint te typen, wordt de lijst met resources door de invoer gefilterd. Selecteer **Recovery Services kluizen**zodra u deze ziet.

    ![Typ Recovery Services in het dialoog venster alle services](./media/backup-mabs-install-azure-stack/all-services.png)

    De lijst met Recovery Services-kluizen in het abonnement wordt weergeven.

4. Selecteer uw kluis in de lijst met Recovery Services kluizen om het dash board ervan te openen.

    ![Typ Recovery Services in het dialoog venster alle services](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Klik in het menu aan de kluis aan de slag op **back-up** om de wizard aan de slag te openen.

    ![Back-up aan de slag](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Het menu back-up wordt geopend.

    ![Back-up-doel stellingen-standaard-geopend](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Selecteer in het menu back-up in het menu **waar wordt uw werk belasting uitgevoerd** de optie **on-premises**. Selecteer in de vervolg keuzelijst **waarover wilt u een back-up maken?** de workloads die u wilt beveiligen met Azure backup server. Als u niet zeker weet welke workloads u wilt selecteren, kiest u **Hyper-V virtual machines** en klikt u vervolgens op **infra structuur voorbereiden**.

    ![on-premises en workloads als doel stellingen](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    Het menu **infra structuur voorbereiden** wordt geopend.

7. Klik in het menu **infra structuur voorbereiden** op **downloaden** om een webpagina te openen om Azure backup server installatie bestanden te downloaden.

    ![Wizard aan de slag](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    De webpagina van micro soft die als host fungeert voor de Download bare bestanden voor Azure Backup Server, wordt geopend.

8. Selecteer op de pagina Microsoft Azure Backup Server downloaden een taal en klik op **downloaden**.

    ![Download centrum wordt geopend](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Het installatie programma van Azure Backup Server bestaat uit acht bestanden: een installatie programma en zeven. bin-bestanden. Controleer de **Bestands naam** om alle vereiste bestanden te selecteren en klik op **volgende**. Down load alle bestanden naar dezelfde map.

    ![Down load Center 1](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    De download grootte van alle installatie bestanden is groter dan 3 GB. Bij een download koppeling van 10 Mbps kan het downloaden van alle installatie bestanden tot 60 minuten duren. De bestanden worden gedownload naar de opgegeven Download locatie.

## <a name="extract-azure-backup-server-install-files"></a>Azure Backup Server installatie bestanden uitpakken

Nadat u alle bestanden naar uw Azure Stack virtuele machine hebt gedownload, gaat u naar de download locatie. De eerste fase van de installatie van Azure Backup Server bestaat uit het extra heren van de bestanden.

![Down load Center 1](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Als u de installatie wilt starten, klikt u in de lijst met gedownloade bestanden op **MicrosoftAzureBackupserverInstaller. exe**.

    > [!WARNING]
    > Er is ten minste 4 GB beschik bare ruimte nodig om de Setup-bestanden uit te pakken.
    >

2. Klik in de wizard Azure Backup Server op **volgende** om door te gaan.

    ![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Kies het pad voor de Azure Backup Server bestanden en klik op **volgende**.

   ![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Controleer de uitpak locatie en klik op **extra heren**.

   ![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. De wizard pakt de bestanden uit en leest het installatie proces.

   ![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Zodra het uitpak proces is voltooid, klikt u op **volt ooien**. **Setup. exe uitvoeren** is standaard geselecteerd. Wanneer u op **volt ooien**klikt, installeert Setup. exe Microsoft Azure backup server op de opgegeven locatie.

   ![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Het software pakket installeren

In de vorige stap klikt u op **volt ooien** om de uitpak fase af te sluiten en start u de wizard Azure backup server Setup.

![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Server deelt code met Data Protection Manager. Verwijzingen naar Data Protection Manager en DPM vindt u in het installatie programma van Azure Backup Server. Hoewel Azure Backup Server en Data Protection Manager afzonderlijke producten zijn, zijn deze producten nauw verwant.

1. Klik op **Microsoft Azure backup server**om de installatie wizard te starten.

   ![Wizard Microsoft Azure Backup-installatie](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. Klik in het **welkomst** scherm op **volgende**.

    ![Azure Backup Server-welkom en vereisten controleren](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. Klik op het scherm **vereisten controles** op **controleren** om te bepalen of aan de hardware-en software vereisten voor Azure backup server is voldaan.

    ![Azure Backup Server-welkom en vereisten controleren](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Als uw omgeving voldoet aan de vereisten, wordt er een bericht weer gegeven waarin staat dat de computer aan de eisen voldoet. Klik op **Volgende**.  

    ![Controle van vereisten voor Azure Backup Server](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Als uw omgeving niet voldoet aan de vereiste vereisten, worden de problemen opgegeven. De vereisten die niet worden voldaan, worden ook vermeld in het DpmSetup. log. Los de fouten van de vereisten op en voer de **controle opnieuw**uit. De installatie kan pas worden voortgezet als aan alle vereisten wordt voldaan.

    ![Azure Backup Server niet voldaan aan de vereisten voor installatie](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup-Server vereist SQL Server. Het Azure Backup Server-installatie pakket wordt geleverd met de juiste SQL Server binaire bestanden. Als u uw eigen SQL-installatie wilt gebruiken, kunt u. Met de aanbevolen optie kan het installatie programma echter een nieuw exemplaar van SQL Server toevoegen. Klik op **controleren en installeren**om ervoor te zorgen dat uw keuze werkt met uw omgeving.

   > [!NOTE]
   > Azure Backup Server werkt niet met een extern SQL Server exemplaar. Het exemplaar dat door Azure Backup Server wordt gebruikt, moet lokaal zijn.
   >

    ![Azure Backup Server-welkom en vereisten controleren](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Nadat u hebt gecontroleerd of de virtuele machine de benodigde vereisten heeft om Azure Backup Server te installeren, klikt u op **volgende**.

    ![Azure Backup Server-welkom en vereisten controleren](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Als er een fout optreedt met een aanbeveling om de computer opnieuw op te starten, start u de computer opnieuw op. Nadat de computer opnieuw is opgestart, start u het installatie programma opnieuw en klikt u op **opnieuw controleren**wanneer u het scherm **SQL-instellingen** weer gegeven.

5. Geef in de **installatie-instellingen**een locatie op voor de installatie van Microsoft Azure backup server-bestanden en klik op **volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    De Scratch locatie is vereist voor het maken van een back-up naar Azure. Zorg ervoor dat de grootte van de Scratch locatie gelijk is aan ten minste 5% van de gegevens die zijn gepland om een back-up te maken naar Azure. Voor schijf beveiliging moeten afzonderlijke schijven worden geconfigureerd zodra de installatie is voltooid. Zie [opslag groepen en schijf opslag configureren](https://technet.microsoft.com/library/hh758075.aspx)voor meer informatie over opslag groepen.

6. Geef in het scherm **beveiligings instellingen** een sterk wacht woord op voor beperkte lokale gebruikers accounts en klik op **volgende**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. Selecteer op het scherm **opt-In Microsoft Update** of u *Microsoft Update* wilt gebruiken om te controleren of er updates zijn en klik op **volgende**.

   > [!NOTE]
   > U kunt het beste Windows Update omleiden naar Microsoft Update, waarmee beveiligings-en belang rijke updates voor Windows en andere producten zoals Microsoft Azure Backup Server worden geboden.
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. Bekijk het *overzicht van instellingen* en klik op **installeren**.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Wanneer de installatie van Azure Backup Server is voltooid, wordt het installatie programma van Microsoft Azure Recovery Services agent direct gestart.

9. Het installatie programma van Microsoft Azure Recovery Services agent wordt geopend en controleert op Internet connectiviteit. Als er verbinding met internet beschikbaar is, gaat u verder met de installatie. Als er geen verbinding is, geeft u proxy gegevens op om verbinding te maken met internet. Klik op **volgende**nadat u de proxy instellingen hebt opgegeven.

    ![Microsoft Azure Backup PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Klik op **installeren**om de Microsoft Azure Recovery Services-agent te installeren.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    De Microsoft Azure Recovery Services-agent, ook wel de Azure Backup-Agent genoemd, configureert de Azure Backup Server voor de Recovery Services kluis. Zodra de gegevens zijn geconfigureerd, worden er door Azure Backup Server altijd back-ups gemaakt van de Recovery Services kluis.

11. Zodra de Microsoft Azure Recovery Services-agent is geïnstalleerd, klikt u op **volgende** om de volgende fase te starten: Azure backup server registreren bij de Recovery Services kluis.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Het installatie programma start de **wizard Server registreren**.

12. Schakel over naar uw Azure-abonnement en uw Recovery Services kluis. Klik in het menu **infra structuur voorbereiden** op **downloaden** om de kluis referenties te downloaden. Als de knop **downloaden** in stap 2 niet actief is, selecteert u **al gedownload of gebruikt u de nieuwste Azure backup server-installatie** om de knop te activeren. De kluis referenties worden gedownload naar de locatie waar u down loads opslaat. Let op deze locatie, want u hebt deze nodig voor de volgende stap.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. Klik in het menu **kluis identificatie** op **bladeren** om de Recovery Services kluis referenties te vinden.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    Ga in het dialoog venster **kluis referenties selecteren** naar de download locatie, selecteer uw kluis referenties en klik op **openen**.

    Het pad naar de referenties wordt weer gegeven in het menu kluis identificatie. Klik op **volgende** om door te gaan naar de versleutelings instelling.

14. Geef in het dialoog venster **versleutelings instelling** een wachtwoordzin op voor de versleuteling van de back-up en een locatie voor het opslaan van de wachtwoordzin en klik op **volgende**.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    U kunt uw eigen wachtwoordzin opgeven, of de wachtwoordzin gebruiken om er een te maken. De wachtwoordzin is u en micro soft slaat deze wachtwoordzin niet op of beheert deze. Sla uw wachtwoordzin op een toegankelijke locatie op om u voor te bereiden op een nood geval.

    Nadat u op **volgende**hebt geklikt, wordt de Azure backup server geregistreerd bij de Recovery Services kluis. Het installatie programma gaat door met het installeren van SQL Server en de Azure Backup Server.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Wanneer het installatie programma is voltooid, ziet u in de status dat alle software is geïnstalleerd.

    ![Azure Backup Server PreReq2](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Wanneer de installatie is voltooid, worden de Azure Backup Server-console en de Azure Backup Server Power shell-pictogrammen op het bureau blad van de server gemaakt.

## <a name="add-backup-storage"></a>Back-upopslag toevoegen

De eerste back-upkopie wordt opgeslagen op de opslag die is gekoppeld aan de Azure Backup Server machine. Zie voor meer informatie over het toevoegen van schijven [moderne back-upopslag toevoegen](https://docs.microsoft.com/system-center/dpm/add-storage?view=sc-dpm-1801).

> [!NOTE]
> U moet ook back-upopslag toevoegen als u van plan bent om gegevens naar Azure te verzenden. In de Azure Backup Server architectuur bevat de Recovery Services kluis de *tweede* kopie van de gegevens terwijl de lokale opslag de eerste (en verplichte) back-up bevat.
>
>

## <a name="network-connectivity"></a>Netwerk verbinding

Azure Backup Server moet verbinding hebben met de Azure Backup-service om het product goed te laten werken. Als u wilt controleren of de computer de verbinding met Azure heeft, gebruikt u de cmdlet ```Get-DPMCloudConnection``` in de Azure Backup Server Power shell-console. Als de uitvoer van de cmdlet TRUE is, is er verbinding, maar is er geen verbinding.

Op hetzelfde moment moet het Azure-abonnement de status in orde hebben. Als u de status van uw abonnement wilt weten en wilt beheren, meldt u zich aan bij de [Portal voor abonnementen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

Zodra u de status van de Azure-verbinding en het Azure-abonnement kent, kunt u de onderstaande tabel gebruiken om de gevolgen van de functionaliteit voor back-up/herstel te bepalen.

| Connectiviteits status | Azure-abonnement | Back-ups maken naar Azure | Back-up op schijf maken | Herstellen vanuit Azure | Herstellen uit een schijf |
| --- | --- | --- | --- | --- | --- |
| Verbonden |Actief |Toegestaan |Toegestaan |Toegestaan |Toegestaan |
| Verbonden |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbonden |Ongedaan gemaakt |Gestopt |Gestopt |Gestopt en Azure-herstel punten zijn verwijderd |Gestopt |
| Verbroken Connectiviteit > 15 dagen |Actief |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbroken Connectiviteit > 15 dagen |Verlopen |Gestopt |Gestopt |Toegestaan |Toegestaan |
| Verbroken Connectiviteit > 15 dagen |Ongedaan gemaakt |Gestopt |Gestopt |Gestopt en Azure-herstel punten zijn verwijderd |Gestopt |

### <a name="recovering-from-loss-of-connectivity"></a>Herstellen van connectiviteits verlies

Als een firewall of proxy de toegang tot Azure blokkeert, moet u de volgende domein adressen toevoegen aan de lijst met toegestane firewall-en proxy profielen:

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

Zodra de verbinding met Azure is hersteld naar het Azure Backup Server, bepaalt de status van het Azure-abonnement welke bewerkingen kunnen worden uitgevoerd. Zodra de server is **verbonden**, gebruikt u de tabel in [netwerk connectiviteit](backup-mabs-install-azure-stack.md#network-connectivity) om de beschik bare bewerkingen weer te geven.

### <a name="handling-subscription-states"></a>Abonnements statussen verwerken

Het is mogelijk om een Azure-abonnement te wijzigen van de status *verlopen* of niet- *ingericht* naar *actief* . De status van het abonnement is niet *actief*:

- Terwijl een abonnement wordt *verwijderd, gaat*de functionaliteit verloren. Als u het abonnement terugzet op actief, wordt de functionaliteit voor back-up/herstel opnieuw *geactiveerd*. Als er back-upgegevens op de lokale schijf werden bewaard met een voldoende Bewaar periode, kunnen de back-upgegevens worden opgehaald. Back-upgegevens in azure zijn echter IRRETRIEVABLY kwijt wanneer het abonnement de status van *oningerichte* heeft ingevoerd.
- Wanneer een abonnement is *verlopen*, verliest dit de functionaliteit. Geplande back-ups worden niet uitgevoerd terwijl een abonnement is *verlopen*.

## <a name="troubleshooting"></a>Problemen oplossen

Als Microsoft Azure Backup Server mislukt met fouten tijdens de installatie fase (of een back-up of herstel), raadpleegt u het [document met fout codes](https://support.microsoft.com/kb/3041338).
U kunt ook verwijzen naar [Azure backup gerelateerde Veelgestelde vragen](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Volgende stappen

Het artikel [voor het voorbereiden van uw omgeving voor dpm](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-1801)bevat informatie over ondersteunde Azure backup server configuraties.

U kunt de volgende artikelen gebruiken om een beter inzicht te krijgen in de beveiliging van werk belasting met behulp van Microsoft Azure Backup-Server.

- [SQL Server back-up](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
- [Share Point server-back-up](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Alternatieve server back-up](backup-azure-alternate-dpm-server.md)
