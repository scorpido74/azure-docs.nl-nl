---
title: Offline back-up met Azure Data Box
description: Meer informatie over hoe u Azure Data Box kunt gebruiken om grote initiële back-upgegevens offline te brengen van de MARS-agent naar een Azure Recovery Services-kluis.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027028"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Offline back-ups Azure Backup met Azure Data Box

U kunt de [Azure data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -service gebruiken om uw grote eerste Mars-back-ups offline te brengen (zonder gebruik van het netwerk) naar een Azure Recovery Services-kluis.  Dit bespaart tijd en netwerk bandbreedte, waardoor er anders een grote hoeveelheid back-upgegevens online wordt verplaatst via een netwerk met een hoge latentie. Deze uitbrei ding is momenteel beschikbaar als preview-versie. Azure Data Box op basis van offline back-ups biedt twee verschillende voor delen ten opzichte [van de Azure import/export-service voor offline back-ups](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. U hoeft uw eigen Azure-compatibele schijven en connectors niet aan te schaffen. Azure Data Box-Service levert de schijven die zijn gekoppeld aan de geselecteerde [Data Box SKU](https://azure.microsoft.com/services/databox/data/)

2. Azure Backup (MARS-agent) kunnen back-upgegevens rechtstreeks naar de ondersteunde Sku's van Azure Data Box schrijven. Dit elimineert de nood zaak voor het inrichten van een faserings locatie voor uw eerste back-upgegevens en de nood zaak van hulpprogram ma's voor het format teren en kopiëren van die gegevens op de schijven.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box met MARS-agent

In dit artikel wordt uitgelegd hoe u Azure Data Box kunt gebruiken om grote initiële back-upgegevens offline te brengen van de MARS-agent naar een Azure Recovery Services-kluis. Het artikel is onderverdeeld in de volgende onderdelen:

* Ondersteunde platforms
* Ondersteunde Data Box Sku's
* Vereisten
* MARS-agent instellen
* Azure Data Box instellen
* Back-upgegevens overdragen naar Azure Data Box
* Stappen na de back-up

## <a name="supported-platforms"></a>Ondersteunde platforms

Het proces voor het seeden van gegevens van de MARS-agent met behulp van Azure Data Box wordt ondersteund op de volgende Windows Sku's:

| **Besturingssysteem**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Werk station**                        |                                                              |
| Windows 10 64-bits                     | Enter prise, Pro, Home                                       |
| Windows 8,1 64 bits                    | Enter prise, Pro                                             |
| Windows 8 64-bits                      | Enter prise, Pro                                             |
| Windows 7 64-bits                      | Ultimate, Enter prise, Professional, Home Premium, Home Basic, starter |
| **Server**                             |                                                              |
| Windows Server 2019 64-bits            | Standard, Data Center, Essentials                            |
| Windows Server 2016 64-bits            | Standard, Data Center, Essentials                            |
| Windows Server 2012 R2 64 bits         | Standard, Data Center, Foundation                            |
| Windows Server 2012 64-bits            | Data Center, Foundation, Standard                            |
| Windows Storage Server 2016 64 bits    | Standaard, werk groep                                         |
| Windows Storage Server 2012 R2 64 bits | Standaard, werk groep, essentieel                              |
| Windows Storage Server 2012 64 bits    | Standaard, werk groep                                         |
| Windows Server 2008 R2 SP1 64 bits     | Standard, Enter prise, Data Center, Foundation                |
| Windows Server 2008 SP2 64 bits        | Standard, Enter prise, Data Center                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Grootte van back-upgegevens en ondersteunde Data Box Sku's

| Grootte van back-upgegevens (post compressie per MARS) * per server | Ondersteund Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB en < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* Typische compressie tarieven variëren tussen 10-20% <br>
\* * Neem contact op met [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) als u verwacht dat u meer dan 80 TB aan initiële back-upgegevens voor één MARS-server hebt.

>[!IMPORTANT]
>De eerste back-upgegevens van één server moeten zich in één Azure Data Box of Azure Data Box Disk bevinden en kunnen niet worden gedeeld tussen meerdere apparaten van dezelfde of andere Sku's. Een Azure Data Box apparaat kan echter eerste back-ups van meerdere servers bevatten.

## <a name="pre-requisites"></a>Vereisten

### <a name="azure-subscription-and-required-permissions"></a>Azure-abonnement en de vereiste machtigingen

* Voor het proces is een Azure-abonnement vereist
* Het proces vereist dat de gebruiker die het offline back-upbeleid heeft opgegeven, een ' eigenaar ' van het Azure-abonnement is
* De Data Box-taak en de Recovery Services kluis (waartoe de gegevens moeten worden geseedd) moeten zich in dezelfde abonnementen bevinden.
* Het is raadzaam om het doel-opslag account dat is gekoppeld aan de Azure Data Box-taak en de Recovery Services kluis zich in dezelfde regio te bevinden. Dit is echter niet nodig.

### <a name="get-azure-powershell-370"></a>Azure PowerShell 3.7.0 ophalen

**Dit is de belangrijkste vereiste voor het proces**. Voordat u Azure PowerShell installeert (ver. 3.7.0), voert u de volgende controles uit * *

#### <a name="step-1-check-powershell-version"></a>Stap 1: Power shell-versie controleren

* Open Windows PowerShell en voer de volgende opdracht uit:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Als de uitvoer een hogere versie dan **3.7.0**bevat, voert u stap 2 hieronder uit. Zo niet, ga dan direct naar stap 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Stap 2: de Power shell-versie verwijderen

Verwijder de huidige versie van Power shell door de volgende acties uit te voeren:

* Verwijder de afhankelijke modules door de volgende opdracht uit te voeren in Power shell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Voer de volgende opdracht uit om ervoor te zorgen dat alle afhankelijke modules goed worden verwijderd:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Stap 3: Power shell-versie 3.7.0 installeren

Wanneer u hebt gecontroleerd of er geen AzureRM-modules aanwezig zijn, installeert u versie 3.7.0 met een van de volgende methoden:

* Via GitHub, [link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

OF

* Voer de volgende opdracht uit in het Power shell-venster:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell kan ook zijn geïnstalleerd met behulp van een MSI-bestand. Als u deze wilt verwijderen, verwijdert u deze met behulp van de optie Program ma's verwijderen in het configuratie scherm.

### <a name="order-and-receive-the-data-box-device"></a>Bestel en ontvang het Data Box apparaat

Het offline back-upproces met MARS en Azure Data Box vereist dat de vereiste Data Box apparaten de status ' bezorgd ' hebben voordat offline back-up wordt geactiveerd met de MARS-agent. Raadpleeg de grootte van de [back-upgegevens en ondersteunde data Box sku's](#backup-data-size-and-supported-data-box-skus) om de meest geschikte SKU voor uw vereiste te best Ellen. Volg de stappen in [dit artikel](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) om uw data Box-apparaten te best Ellen en te ontvangen.

>[!IMPORTANT]
>Selecteer niet BlobStorage voor het soort account. De MARS-agent vereist een account dat ondersteuning biedt voor pagina-blobs die niet worden ondersteund wanneer BlobStorage is geselecteerd. U wordt sterk aangeraden *opslag v2* (*algemeen gebruik v2*) te selecteren als het soort account bij het maken van het doel opslag account voor uw Azure data Box-taak.

![Account type kiezen in details van exemplaar](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>De MARS-agent installeren en instellen

* Zorg ervoor dat u eerdere installaties van de MARS-agent verwijdert.

* Down load [hier](https://aka.ms/azurebackup_agent)de nieuwste Mars-agent.

* Voer *MARSAgentInstaller. exe* uit en voer ***alleen** de stappen uit om [de agent te installeren en registreren](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) bij de Recovery Services kluis waarnaar u uw back-ups wilt opslaan.

  >[!NOTE]
  > De Recovery Services kluis moet zich in hetzelfde abonnement benemen als de Azure Data Box taak.

* Nadat de agent is geregistreerd bij de Recovery Services kluis, volgt u de stappen in de onderstaande secties.

## <a name="setup-azure-data-box-devices"></a>Setup Azure Data Box apparaat (en)

Afhankelijk van de Azure Data Box SKU die u hebt besteld, voert u de stappen uit die worden beschreven in de onderstaande secties om de Data Box-apparaten in te stellen en voor te bereiden voor de MARS-agent om de eerste back-upgegevens te identificeren en over te dragen.

### <a name="setup-azure-data-box-disk"></a>Azure Data Box Disk instellen

Als u een of meer Azure Data Box schijven hebt besteld (Maxi maal 8 TB elk), volgt u de stappen die hier worden beschreven om [uw data Box disk uit te pakken, te verbinden en te ontgrendelen](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Het is mogelijk dat de server met de MARS-agent geen USB-poort heeft. In dat geval kunt u uw Azure Data Box Disk verbinden met een andere server/client en de hoofdmap van het apparaat beschikbaar maken als een netwerk share.

### <a name="setup-azure-data-box"></a>Azure Data Box instellen

Als u een Azure Data Box (tot 100 TB) hebt besteld, volgt u de stappen [die hier worden beschreven om uw data box in te](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)stellen.

#### <a name="mount-your-azure-data-box-as-local-system"></a>Uw Azure Data Box als lokaal systeem koppelen

De MARS-agent werkt in de context van het lokale systeem en daarom moet hetzelfde bevoegdheids niveau worden gegeven aan het koppelingspad waarmee de Azure Data Box is verbonden. Volg de onderstaande stappen om ervoor te zorgen dat u uw Data Box-apparaat als lokaal systeem kunt koppelen met behulp van het NFS-protocol:

* Schakel de functie client voor NFS in op de Windows-Server (waarop de MARS-agent is geïnstalleerd).<br>
  Geef een alternatieve bron op: *Wim: D: \Sources\Install.Wim: 4*

* Down load PSExec van <https://download.sysinternals.com/files/PSTools.zip> naar de server waarop de MARS-agent is geïnstalleerd.

* Open een opdracht prompt met verhoogde bevoegdheid en voer de volgende opdracht uit met de map met PSExec. exe als de huidige map:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* Het opdracht venster dat wordt geopend als gevolg van de bovenstaande opdracht bevindt zich in de context van het lokale systeem. Gebruik dit opdracht venster om de stappen uit te voeren om de Azure page BLOB-share te koppelen als een netwerk station op uw Windows-Server.

* Volg de onderstaande [stappen om uw server te verbinden](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) met de Mars-agent met het data Box-apparaat via NFS en de volgende opdracht uit te voeren op de opdracht prompt van het lokale systeem om de share Azure pagina-blobs te koppelen:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Nadat de verbinding is gekoppeld, controleert u of u toegang hebt tot X: vanaf uw server. Zo ja, ga dan verder met de volgende sectie van dit artikel.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Initiële back-upgegevens overdragen naar Azure Data Box apparaat (en)

* Open de **Microsoft Azure backup** -toepassing op uw server.

* Klik in het deel venster **acties** op **back-up plannen** .

    ![Klik op back-up plannen](./media/offline-backup-azure-data-box/schedule-backup.png)

* Volg de stappen in de **wizard Back-up plannen**

* **Voeg items toe** zodat de totale grootte van de items binnen de [grootte limieten valt die wordt ondersteund door de Azure data Box SKU](#backup-data-size-and-supported-data-box-skus) die u hebt besteld en ontvangen.

    ![Items toevoegen aan back-up](./media/offline-backup-azure-data-box/add-items.png)

* Selecteer het juiste back-upschema en het Bewaar beleid voor bestanden en mappen en de systeem status (systeem status is alleen van toepassing op Windows-servers en niet voor Windows-clients)

* Kies in het scherm **eerste back-uptype (bestanden en mappen)** van de wizard de optie **overdracht met Microsoft Azure data Box schijven** en klik op **volgende**

    ![Eerste back-uptype kiezen](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Meld u aan bij Azure wanneer u wordt gevraagd de gebruikers referenties met de eigenaar van het Azure-abonnement te gebruiken. Nadat u dit hebt gedaan, wordt er een scherm weer gegeven dat er ongeveer als volgt uitziet:

    ![Resources maken en vereiste machtigingen Toep assen](./media/offline-backup-azure-data-box/creating-resources.png)

* De MARS-agent haalt vervolgens de Data Box-taken op in het abonnement die de status ' bezorgd ' hebben.

    ![Databox-taken voor abonnements-ID ophalen](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Selecteer de juiste volg orde voor data vakken waarvoor u de Data Box schijf hebt uitgepakt, verbonden en ontgrendeld. Klik op **Volgende**.

    ![Data Box order (s) selecteren](./media/offline-backup-azure-data-box/select-databox-order.png)

* Klik op **apparaat detecteren** in het scherm **detectie van data Box apparaat** . Hierdoor wordt de MARS-agent gescand op lokaal gekoppelde Azure Data Box schijven en worden deze gedetecteerd, zoals hieronder wordt weer gegeven:

    ![Detectie van Data Box apparaat](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Als u de Azure Data Box als een netwerk share hebt verbonden (vanwege een niet-beschik baarheid van USB-poorten of omdat u het apparaat 100 TB Data Box hebt gebesteld en gekoppeld), zal de detectie eerst mislukken, maar krijgt u de mogelijkheid om het netwerkpad naar de Data Box apparaat in te voeren als s hown hieronder:

    ![Voer het netwerkpad in](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Geef het netwerkpad naar de hoofdmap van de Azure Data Box schijf op. Deze map moet een map met de naam *PageBlob* bevatten, zoals hieronder wordt weer gegeven:
    >
    >![Hoofdmap van Azure Data Box schijf](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Als het pad van de schijf bijvoorbeeld `\\mydomain\myserver\disk1\` is en *Disk1* een map bevat met de naam *PageBlob*, wordt het pad dat wordt vermeld in de wizard Mars agent `\\mydomain\myserver\disk1\`
    >
    >Als u [een Azure Data Box 100 TB-apparaat hebt ingesteld](#setup-azure-data-box), geeft u het volgende op als het netwerkpad naar het apparaat `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Klik op **volgende** en klik op **volt ooien** in het volgende scherm om het back-up-en bewaar beleid met de configuratie van offline back-up op te slaan met Azure data box.

* In het volgende scherm wordt bevestigd dat het beleid is opgeslagen:

    ![Het beleid is opgeslagen](./media/offline-backup-azure-data-box/policy-saved.png)

* Klik op het bovenstaande scherm op **sluiten** .

* Klik op ***Nu back** -up maken in het deel venster **acties** van de Mars agent-console en klik op **back-up** in het scherm van de wizard, zoals hieronder wordt weer gegeven:

    ![Wizard nu back-up maken](./media/offline-backup-azure-data-box/backup-now.png)

* De MARS-agent begint met het maken van een back-up van de gegevens die u hebt geselecteerd voor het Azure Data Box apparaat. Dit kan enkele uren tot enkele dagen duren, afhankelijk van het aantal bestanden en de verbindings snelheid tussen de server en de MARS-agent en de Azure Data Box Disk.

* Wanneer de back-up van de gegevens is voltooid, ziet u een scherm op de MARS-agent die er ongeveer als volgt uitziet:

    ![Voortgang van back-up weer gegeven](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Stappen na de back-up

In deze sectie worden de stappen beschreven die u moet uitvoeren wanneer het maken van een back-up van de gegevens naar de Azure Data Box Disk is voltooid.

* Volg de stappen in dit artikel om [de Azure data Box schijf naar Azure te verzenden](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Als u een apparaat met Azure Data Box 100-TB hebt gebruikt, volgt u deze stappen om [de Azure data box naar Azure te verzenden](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Controleer de data Box taak](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) in de Azure Portal. Zodra de Azure Data Box taak ' voltooid ' is, worden de gegevens van het opslag account automatisch door de MARS-agent naar de Recovery Services kluis verplaatst op het moment van de volgende geplande back-up. Als een herstel punt is gemaakt, wordt de back-uptaak als voltooid gemarkeerd.

    >[!NOTE]
    >De MARS-agent triggert back-ups op de tijden die zijn gepland tijdens het maken van het beleid. Met deze taken wordt echter ' wachten tot Azure Data Box taak is voltooid ' gemarkeerd tot het moment waarop de taak is voltooid.

* Nadat de MARS-agent een herstel punt heeft gemaakt dat overeenkomt met de eerste back-up, kunt u het opslag account (of de specifieke inhoud) verwijderen die aan de Azure Data Box-taak is gekoppeld.

## <a name="troubleshooting"></a>Problemen oplossen

De Microsoft Azure Backup-Agent (MAB) maakt een Azure AD-toepassing voor u in uw Tenant. Voor deze toepassing is een certificaat vereist voor verificatie dat is gemaakt en geüpload bij het configureren van het offline seeding-beleid. We gebruiken Azure PowerShell om het certificaat te maken en te uploaden naar de Azure AD-toepassing.

### <a name="issue"></a>Probleem

Op het moment van het configureren van de offline back-up is het mogelijk dat u een probleem ondervindt, waarbij een fout in de Azure PowerShell-cmdlet niet kan worden toegevoegd aan dezelfde Azure AD-toepassing die door de MAB-agent is gemaakt. Dit heeft gevolgen voor u als u offline seeding-beleid voor dezelfde of een andere server hebt geconfigureerd.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Controleren of het probleem wordt veroorzaakt door deze specifieke hoofd oorzaak

Voer een van de volgende stappen uit om ervoor te zorgen dat de fout wordt veroorzaakt door het bovenstaande probleem:

#### <a name="step-1"></a>Stap 1

Controleer of het volgende fout bericht wordt weer gegeven in de MAB-console op het moment dat u offline back-up configureert:

![Kan geen offline back-upbeleid maken voor het huidige Azure-account](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Stap 2

* Open de map **temp** in het installatiepad (standaardpad voor tijdelijke mappen is *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Zoek naar het **CBUICurr** -bestand en open het bestand.

* Schuif in het **CBUICurr** -bestand naar de laatste regel en controleer of de fout wordt veroorzaakt door `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Tijdelijke oplossing

Als tijdelijke oplossing: Voer de volgende stappen uit om dit probleem op te lossen en voer de beleids configuratie opnieuw uit.

#### <a name="first-step"></a>Eerste stap

Meld u aan bij Power shell die wordt weer gegeven in de MAB-gebruikers interface met een ander account met beheerders toegang voor het abonnement waarin de export taak voor importeren wordt gemaakt.

#### <a name="second-step"></a>Tweede stap

Als voor geen enkele andere server offline seeding is geconfigureerd en er geen andere server afhankelijk is van de `AzureOfflineBackup_<Azure User Id>` toepassing, verwijdert u deze toepassing uit **Azure Portal** > **Azure Active Directory** > **app-registraties.**

>[!NOTE]
> Controleer of voor de toepassings `AzureOfflineBackup_<Azure User Id>` geen andere offline-seeding is geconfigureerd en of er geen andere server afhankelijk is van deze toepassing. Ga naar **instellingen** > **sleutels** onder de sectie **open bare sleutels** er mogen geen andere open bare sleutels zijn toegevoegd. Raadpleeg de volgende scherm afbeelding voor referentie:
>
>![Open bare sleutels](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Derde stap

Voer de volgende acties uit vanaf de-server die u wilt configureren voor offline back-up:

1. Open het tabblad **computer certificaat toepassing beheren** > **persoonlijk** en zoek naar het certificaat met de naam `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Selecteer het certificaat dat hierboven wordt vermeld, klik met de rechter muisknop op **alle taken** en **Exporteer** zonder persoonlijke sleutel in de. CER-indeling.

3. Ga naar de Azure offline back-uptoepassing die wordt vermeld in **punt 2**. In de **instellingen** > **sleutels** > **open bare sleutel uploaden** uploadt u het certificaat dat u in de bovenstaande stap hebt geëxporteerd.

    ![Open bare sleutel uploaden](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Open het REGI ster in de-server door **regedit** te typen in het venster uitvoeren.

5. Ga naar de register *computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Klik met de rechter muisknop op **CloudBackupProvider** en voeg een nieuwe teken reeks waarde toe met de naam `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Als u de Azure-gebruikers-id wilt ophalen, voert u een van de volgende acties uit:
    >
    >1. Voer de `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”` opdracht uit van de Azure Connected Power shell.
    > 2. Ga naar het registerpad: *computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Naam: *CurrentUserId*

6. Klik met de rechter muisknop op de teken reeks die u in de bovenstaande stap hebt toegevoegd en selecteer **wijzigen**. Geef in de waarde de vinger afdruk op van het certificaat dat u hebt geëxporteerd in **punt 2** en klik op **OK**.

7. Als u de waarde van de vinger afdruk wilt ophalen, dubbelklikt u op het certificaat, selecteert u het tabblad **Details** en schuift u omlaag totdat u het veld vinger afdruk ziet. Klik op **vinger afdruk** en kopieer de waarde.

    ![Vingerafdruk veld van certificaat](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Vragen

Voor alle vragen of uitleg over problemen die zich voordoen, neem dan contact op met [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
