---
title: Offline back-up met Azure Data Box
description: Lees hoe u Azure Data Box gebruiken om grote initiële back-upgegevens offline te houden van de MARS-agent naar een vault voor Herstelservices.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672973"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup offline back-up met Azure Data Box

U [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) gebruiken om uw grote initiële Mars-back-ups (Microsoft Azure Recovery Services) offline te zetten (zonder netwerk te gebruiken) in een kluis van Recovery Services. Dit proces bespaart tijd en netwerkbandbreedte die anders zouden worden verbruikt door grote hoeveelheden back-upgegevens online te verplaatsen via een netwerk met hoge latentie. Deze verbetering is momenteel in preview. Offline back-up op basis van Azure Data Box biedt twee duidelijke voordelen ten opzichte van [offline back-up op basis van de Azure Import/Export-service:](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

* Het is niet nodig om uw eigen Azure-compatibele schijven en connectoren aan te schaffen. Azure Data Box verzendt de schijven die zijn gekoppeld aan de geselecteerde [SKU voor gegevens .](https://azure.microsoft.com/services/databox/data/)
* Azure Backup (MARS Agent) kan back-upgegevens rechtstreeks schrijven naar de ondersteunde SKU's van Azure Data Box. Met deze mogelijkheid hoeft u geen tijdelijke locatie in te richten voor uw eerste back-upgegevens. U hebt ook geen hulpprogramma's nodig om die gegevens op te maken en te kopiëren naar de schijven.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure-gegevensvak met de MARS-agent

In dit artikel wordt uitgelegd hoe u Azure Data Box gebruiken om grote initiële back-upgegevens offline te houden van de MARS-agent naar een vault voor herstelservices.

## <a name="supported-platforms"></a>Ondersteunde platforms

Het proces om gegevens van de MARS-agent te zaaien met Azure Data Box wordt ondersteund op de volgende Windows SKU's.

| **Besturingssysteem**                                 | **Sku**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Werkstation**                        |                                                              |
| Windows 10 64-bits                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64-bits                    | Enterprise, Pro                                             |
| Windows 8 64-bits                      | Enterprise, Pro                                             |
| Windows 7 64-bits                      | Ultimate, Enterprise, Professional, Home Premium Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64-bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64-bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64-bits         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64-bits            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 64-bits    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 64-bits | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64-bits    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 64-bits     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64-bits        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Back-upvan gegevens grootte en ondersteunde Data Box SKU's

| Back-upgegevensgrootte (postcompressie door MARS)* per server | Ondersteunde Azure-gegevensvak SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Azure Data Box-schijf](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB en <=80 TB**                                      | [Azure-gegevensvak (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*Typische compressiesnelheden variëren tussen 10% en 20%. <br>
**Als u verwacht meer dan 80 TB aan initiële [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)back-upgegevens voor één MARS-server te hebben, neemt u contact op met .

>[!IMPORTANT]
>Initiële back-upgegevens van één server moeten zich bevinden in één Azure Data Box-exemplaar of Azure Data Box-schijf en kunnen niet worden gedeeld tussen meerdere apparaten van dezelfde of verschillende SKU's. Maar een Azure Data Box-apparaat kan de eerste back-ups van meerdere servers bevatten.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription-and-required-permissions"></a>Azure-abonnement en vereiste machtigingen

* Voor het proces is een Azure-abonnement vereist.
* Het proces vereist dat de gebruiker die is aangewezen om het offline back-upbeleid uit te voeren, eigenaar is van het Azure-abonnement.
* De taak Gegevensbox en de kluis Herstelservices (waaraan de gegevens moeten worden gezaaid) moeten in dezelfde abonnementen zitten.
* We raden u aan dat het doelopslagaccount dat is gekoppeld aan de azure-gegevensvaktaak en de kluis Herstelservices zich in dezelfde regio bevindt. Dit is echter niet nodig.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0

*Dit is de belangrijkste voorwaarde voor het proces*. Voordat u Azure PowerShell installeert, voert versie 3.7.0 de volgende controles uit.

#### <a name="step-1-check-the-powershell-version"></a>Stap 1: Controleer de PowerShell-versie

1. Open Windows PowerShell en voer de volgende opdracht uit:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Als de uitvoer een versie hoger dan 3.7.0 weergeeft, doet u 'Stap 2'. Ga anders naar Stap 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Stap 2: De PowerShell-versie verwijderen

Verwijder de huidige versie van PowerShell.

1. Verwijder de afhankelijke modules door de volgende opdracht in PowerShell uit te voeren:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Voer de volgende opdracht uit om ervoor te zorgen dat alle afhankelijke modules succesvol worden verwijderd:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Stap 3: PowerShell-versie 3.7.0 installeren

Nadat u hebt geverifieerd dat er geen AzureRM-modules aanwezig zijn, installeert u versie 3.7.0 met een van de volgende methoden:

* Gebruik [deze link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)via GitHub.

Of u kunt:

* Voer de volgende opdracht uit in het PowerShell-venster:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell had ook kunnen worden geïnstalleerd met behulp van een msi-bestand. Verwijder het als u het wilt verwijderen door de optie **Programma's verwijderen** in het Configuratiescherm te gebruiken.

### <a name="order-and-receive-the-data-box-device"></a>Bestel en ontvang het Data Box-apparaat

Het offline back-upproces met MARS en Azure Data Box vereist dat de Data Box-apparaten in de status Geleverd zijn voordat u offline back-up activeert met behulp van de MARS-agent. Zie [Back-upgegevensgrootte en ondersteunde Gegevensvak SKU's](#backup-data-size-and-supported-data-box-skus)voor het bestellen van de meest geschikte SKU voor uw behoefte. Volg de stappen in [Zelfstudie: bestel een Azure Data Box-schijf](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) om uw Data Box-apparaten te bestellen en te ontvangen.

> [!IMPORTANT]
> Selecteer *BlobStorage* niet voor de **accountsoort**. De MARS-agent vereist een account dat paginablobs ondersteunt, dat niet wordt ondersteund wanneer *BlobStorage* is geselecteerd. Selecteer **Storage V2 (v2 voor algemeen gebruik)** als **accountsoort** wanneer u het doelopslagaccount voor uw Azure Data Box-taak maakt.

![Accountsoort kiezen in instantiedetails](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>De MARS-agent installeren en instellen

1. Zorg ervoor dat u eerdere installaties van de MARS-agent verwijdert.
1. Download de nieuwste MARS Agent van [deze website.](https://aka.ms/azurebackup_agent)
1. Voer *MARSAgentInstaller.exe uit*en voer *alleen* de stappen uit om de agent te installeren en te [registreren bij](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) de vault van Recovery Services, waar u uw back-ups wilt opslaan.

   > [!NOTE]
   > De kluis Recovery Services moet zich in hetzelfde abonnement bevinden als de taak Azure Data Box.

   Nadat de agent is geregistreerd bij de kluis Van Herstelservices, volgt u de stappen in de volgende secties.

## <a name="set-up-azure-data-box-devices"></a>Azure Data Box-apparaten instellen

Afhankelijk van de Azure Data Box SKU die u hebt besteld, voert u de stappen uit die worden behandeld in de juiste secties die volgen. De stappen laten u zien hoe u de Data Box-apparaten in- en voorbereidt dat de MARS-agent de eerste back-upgegevens identificeert en overdraagt.

### <a name="set-up-azure-data-box-disks"></a>Azure Data Box-schijven instellen

Als u een of meer Azure Data Box-schijven hebt besteld (tot 8 TB per stuk), volgt u de hier genoemde stappen om [uw Data Box-schijf uit te pakken, verbinding te maken en te ontgrendelen.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)

>[!NOTE]
>Het is mogelijk dat de server met de MARS Agent geen USB-poort heeft. In dat geval u uw Azure Data Box-schijf verbinden met een andere server of client en de hoofdmap van het apparaat blootleggen als een netwerkshare.

### <a name="set-up-azure-data-box"></a>Azure-gegevensvak instellen

Als u een Azure Data Box-exemplaar (tot 100 TB) hebt besteld, voert u de stappen hier [uit om uw instantie Gegevensvak in te stellen.](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Uw Azure Data Box-instantie als lokaal systeem monteren

De MARS-agent werkt in de context van lokaal systeem, dus het vereist hetzelfde niveau van bevoegdheden die moeten worden verstrekt aan het bevestigingspad waar de instantie Azure Data Box is verbonden. 

Ga als reactie op het gebruik van het NFS-protocol om ervoor te zorgen dat u uw Data Box-apparaat als lokaal systeem monteren:

1. Schakel de client in voor de NFS-functie op de Windows-server waarop de MARS-agent is geïnstalleerd. Geef de alternatieve bron *WIM:D:\Sources\Install.wim:4*op .
1. Download PSExec <https://download.sysinternals.com/files/PSTools.zip> van naar de server met de MARS Agent geïnstalleerd.
1. Open een opdrachtprompt met verhoogde bevoegdheid en voer de volgende opdracht uit met de map die *PSExec.exe* als de huidige map bevat.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Het opdrachtvenster dat wordt geopend als gevolg van de vorige opdracht bevindt zich in de context van lokaal systeem. Gebruik dit opdrachtvenster om de stappen uit te voeren om de Azure-paginablobshare te monteren als een netwerkstation op uw Windows-server.
1. Volg de stappen in [Connect to Data Box om](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) uw server via NFS met de MARS-agent te verbinden met het Data Box-apparaat. Voer de volgende opdracht uit op de opdrachtprompt Lokaal systeem om de Azure-paginablobs te delen.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Controleer na het monteren van het aandeel of u toegang hebt tot X: vanaf uw server. Ga zo mogelijk verder met het volgende gedeelte van dit artikel.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Eerste back-upgegevens overbrengen naar Azure Data Box-apparaten

1. Open de **Microsoft Azure Backup-toepassing** op uw server.
1. Selecteer **Back-up plannen**in het deelvenster **Handelingen** .

    ![Back-up plannen selecteren](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Volg de stappen in de **wizard Back-up plannen**.

1. Voeg items toe door de knop **Items toevoegen te** selecteren. Houd de totale grootte van de items binnen de [groottelimieten die worden ondersteund door de Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) die u hebt besteld en ontvangen.

    ![Items toevoegen aan back-up](./media/offline-backup-azure-data-box/add-items.png)

1. Selecteer het juiste back-upschema en bewaarbeleid voor **bestanden en mappen** en **systeemstatus**. De systeemstatus is alleen van toepassing op Windows-servers en niet voor Windows-clients.
1. Selecteer op de pagina **Initiële back-uptype (Bestanden en mappen)** van de wizard de optie **Overdracht met Microsoft Azure Data Box-schijven** en selecteer **Volgende**.

    ![Kies het eerste back-uptype](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Meld u aan bij Azure wanneer u daarom wordt gevraagd met behulp van de gebruikersreferenties met eigenaartoegang in het Azure-abonnement. Nadat u erin geslaagd bent dit te doen, ziet u een pagina die lijkt op deze.

    ![Resources maken en vereiste machtigingen toepassen](./media/offline-backup-azure-data-box/creating-resources.png)

   De MARS-agent haalt vervolgens de gegevensvaktaken op in het abonnement dat zich in de status Geleverd begeeft.

    ![Gegevensvaktaken ophalen voor abonnements-id](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecteer de juiste databoxorder waarvoor u uw databoxschijf hebt uitgepakt, aangesloten en ontgrendeld. Selecteer **Volgende**.

    ![Gegevensvakorders selecteren](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecteer **Apparaat detecteren** op de pagina **Apparaatdetectie in gegevensvak.** Met deze actie u de MARS-agent scannen op lokaal gekoppelde Azure Data Box-schijven en deze detecteren.

    ![Apparaatdetectie in gegevensvak](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Als u de instantie Azure Data Box hebt aangesloten als een netwerkshare (vanwege de onbeschikbaarheid van USB-poorten of omdat u het 100 TB-gegevensbox-apparaat hebt besteld en gemonteerd), mislukt de detectie in eerste instantie. U krijgt de optie om het netwerkpad naar het databox-apparaat in te voeren.

    ![Voer het netwerkpad in](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Geef het netwerkpad op naar de hoofdmap van de Azure Data Box-schijf. Deze map moet een map bevatten met de naam *PageBlob*.
    >
    >![Hoofdmap van Azure Data Box-schijf](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Als het pad van de `\\mydomain\myserver\disk1\` schijf bijvoorbeeld is en *disk1* een map bevat met de `\\mydomain\myserver\disk1\`naam *PageBlob,* is het pad dat u invoert op de wizard MARS Agent- pagina .
    >
    >Als u [een Azure Data Box 100 TB-apparaat instelt,](#set-up-azure-data-box-devices)voert u het `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` netwerkpad naar het apparaat in.

1. Selecteer **Volgende**en selecteer **Voltooien** op de volgende pagina om het back-up- en bewaarbeleid op te slaan met de configuratie van offline back-ups met Azure Data Box.

   Op de volgende pagina wordt bevestigd dat het beleid is opgeslagen.

    ![Beleid wordt opgeslagen](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selecteer **Sluiten** op de vorige pagina.

1. Selecteer **Nu back-up** maken in het deelvenster **Handelingen** van de MARS-agent-console. Selecteer **Back-up maken** op de wizardpagina.

    ![Wizard Back-up nu](./media/offline-backup-azure-data-box/backup-now.png)

De MARS-agent maakt een back-up van de gegevens die u hebt geselecteerd op het Azure Data Box-apparaat. Dit proces kan enkele uren tot enkele dagen duren. De hoeveelheid tijd is afhankelijk van het aantal bestanden en de verbindingssnelheid tussen de server met de MARS-agent en de Azure Data Box-schijf.

Nadat de back-up van de gegevens is voltooid, ziet u een pagina op de MARS-agent die lijkt op deze.

![Back-upvoortgang weergegeven](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Stappen na back-up

In dit gedeelte worden de stappen uitgelegd die moeten worden genomen nadat de back-up van de gegevens naar de Azure Data Box Disk is geslaagd.

* Volg de stappen in dit artikel om [de Azure Data Box-schijf naar Azure te verzenden.](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up) Als u een Azure Data Box 100 TB-apparaat hebt gebruikt, voert u de volgende stappen uit om [het Azure Data Box-apparaat naar Azure te verzenden.](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)

* [Controleer de taak Gegevensvak](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) in de Azure-portal. Nadat de Azure Data Box-taak is voltooid, verplaatst de MARS-agent de gegevens automatisch van het opslagaccount naar de kluis Herstelservices op het moment van de volgende geplande back-up. Vervolgens wordt de back-uptaak *als taak voltooid* als een herstelpunt is gemaakt.

    >[!NOTE]
    >De MARS-agent activeert back-ups op de tijden die zijn gepland tijdens het maken van het beleid. Deze taken markeren 'Wachten tot de taak Azure Data Box is voltooid' totdat de taak is voltooid.

* Nadat de MARS-agent een herstelpunt heeft gemaakt dat overeenkomt met de eerste back-up, u het opslagaccount of de specifieke inhoud verwijderen die is gekoppeld aan de taak Azure Data Box.

## <a name="troubleshooting"></a>Problemen oplossen

De MaB-agent (Microsoft Azure Backup) maakt een Azure Active Directory-toepassing (Azure AD) voor u in uw tenant. Deze toepassing vereist een certificaat voor verificatie dat is gemaakt en geüpload wanneer u een offline zaaibeleid configureert. We gebruiken Azure PowerShell om het certificaat te maken en te uploaden naar de Azure AD-toepassing.

### <a name="problem"></a>Probleem

Wanneer u offline back-ups configureert, u een probleem ondervinden vanwege een bug in de Azure PowerShell-cmdlet. Het is mogelijk dat u niet meerdere certificaten toevoegen aan dezelfde Azure AD-toepassing die is gemaakt door de MAB-agent. Dit probleem heeft invloed op u als u een offline zaaibeleid hebt geconfigureerd voor dezelfde of een andere server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Controleren of het probleem wordt veroorzaakt door deze specifieke hoofdoorzaak

Als u wilt zien of uw probleem hetzelfde is als het eerder beschreven probleem, voert u een van de volgende stappen uit.

#### <a name="step-1"></a>Stap 1

Controleer of het volgende foutbericht wordt weergegeven in de MAB-console wanneer u een offline back-up hebt geconfigureerd.

![Kan geen offline back-upbeleid maken voor het huidige Azure-account](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Stap 2

1. Open de map **Temp** in het installatiepad. Het standaardpad voor tijdelijke mappen is *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Zoek naar het *CBUICurr-bestand* en open het bestand.

1. Schuif in het *CBUICurr-bestand* naar de laatste regel en controleer of het probleem `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`hetzelfde is als het probleem in dit foutbericht: .

### <a name="workaround"></a>Tijdelijke oplossing

Als tijdelijke oplossing om dit probleem op te lossen, voert u de volgende stappen uit en probeert u de beleidsconfiguratie opnieuw.

#### <a name="step-1"></a>Stap 1

Meld u aan bij PowerShell die wordt weergegeven op de MAB-gebruikersinterface met behulp van een ander account met beheerderstoegang bij het abonnement waarop de import- of exporttaak wordt gemaakt.

#### <a name="step-2"></a>Stap 2

Als er geen andere server offline seeding heeft geconfigureerd `AzureOfflineBackup_<Azure User Id>` en geen enkele andere server afhankelijk is van de toepassing, verwijdert u deze toepassing. Selecteer **Azure Portal** > **Azure Active Directory** > **App-registraties**.

>[!NOTE]
> Controleer of de `AzureOfflineBackup_<Azure User Id>` toepassing geen andere offline zaaifunctie heeft geconfigureerd en ook of er geen andere server afhankelijk is van deze toepassing. Ga naar > **Instellingentoetsen** onder de sectie **Openbare sleutels.** **Settings** Het zou geen andere openbare sleutels moeten hebben toegevoegd. Zie de volgende screenshot voor referentie.
>
>![Openbare sleutels](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Stap 3

Voer vanaf de server die u probeert te configureren voor offline back-up, de volgende acties uit.

1. Ga naar het tabblad **Persoonlijke aanvraag voor computercertificaten** > **Personal** beheren `CB_AzureADCertforOfflineSeeding_<ResourceId>`en zoek naar het certificaat met de naam .

2. Selecteer het certificaat, klik met de rechtermuisknop op **Alle taken**en selecteer **Exporteren** zonder privésleutel in de .cer-indeling.

3. Ga naar de azure offline back-uptoepassing die in stap 2 wordt genoemd. Selecteer > **Instellingentoetsen** > **Openbare sleutel uploaden**. **Settings** Upload het certificaat dat u in de vorige stap hebt geëxporteerd.

    ![Openbare sleutel uploaden](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Open het register in de server door **regedit** in het run-venster in te voeren.

5. Ga naar het register *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Klik met de rechtermuisknop op **CloudBackupProvider**en `AzureADAppCertThumbprint_<Azure User Id>`voeg een nieuwe tekenreekswaarde toe met de naam .

    >[!NOTE]
    > Voer een van de volgende acties uit om de Azure-gebruikers-id op te halen:
    >
    >* Voer de `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` opdracht uit vanuit de PowerShell met Azure verbonden.
    > * Ga naar het registerpad *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* met de naam *CurrentUserId*.

6. Klik met de rechtermuisknop op de tekenreeks die in de vorige stap is toegevoegd en selecteer **Wijzigen**. Geef in de waarde de duimafdruk op van het certificaat dat u in stap 2 hebt geëxporteerd. Selecteer **OK**.

7. Dubbelklik op het certificaat om de waarde van de duimafdruk te krijgen. Selecteer het tabblad **Details** en schuif omlaag totdat u het veld duimafdruk ziet. Selecteer **Duimafdruk**en kopieer de waarde.

    ![Duimafdrukveld van certificaat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Vragen

Voor vragen of verduidelijkingen over eventuele [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)problemen die u geconfronteerd, contact op .
