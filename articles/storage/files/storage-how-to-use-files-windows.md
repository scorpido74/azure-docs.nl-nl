---
title: Een Azure-bestandsshare gebruiken met Windows | Microsoft Docs
description: Informatie over hoe u een Azure-bestandsshare gebruikt met Windows en Windows Server.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4bd9c64e1b9219f6752172d9dc518af71ad67e70
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268143"
---
# <a name="use-an-azure-file-share-with-windows"></a>Een Azure-bestandsshare gebruiken met Windows
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestandsshares kunnen probleemloos worden gebruikt in Windows en Windows Server. In dit artikel worden de overwegingen besproken voor het gebruik van een Azure-bestandsshare met Windows en Windows Server.

Als u een Azure-bestandsshare wilt gebruiken buiten de Azure-regio waarin deze wordt gehost, bijvoorbeeld on-premises of in een andere Azure-regio, moet het besturingssysteem ondersteuning bieden voor SMB 3.0. 

U kunt Azure-bestandsshares gebruiken in een Windows-installatie die wordt uitgevoerd in een virtuele machine in Azure of on-premises. In de volgende tabel ziet u welke versies van het besturingssysteem de toegang tot bestandsshares ondersteunen en in welke omgeving:

| Windows-versie        | SMB-versie | Koppelbaar in Azure-VM | Koppelbaar on-premises |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Ja | Ja |
| Windows 10<sup>1</sup> | SMB 3.0 | Ja | Ja |
| Windows Server Semi-Annual-kanaal<sup>2</sup> | SMB 3.0 | Ja | Ja |
| Windows Server 2016 | SMB 3.0 | Ja | Ja |
| Windows 8.1 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 R2 | SMB 3.0 | Ja | Ja |
| Windows Server 2012 | SMB 3.0 | Ja | Ja |
| Windows 7<sup>3</sup> | SMB 2.1 | Ja | Nee |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Ja | Nee |

<sup>1</sup> Windows 10, versies 1507, 1607, 1709, 1803, 1809, 1903 en 1909.  
<sup>2</sup> Windows Server, versies 1809, 1903 en 1909.  
<sup>3</sup> Reguliere micro soft-ondersteuning voor Windows 7 en Windows Server 2008 R2 is beëindigd. Het is mogelijk om aanvullende ondersteuning voor beveiligings updates te kopen via het [Extended Security Update (ESU)-programma](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates). We raden u ten zeerste aan de migratie uit te voeren van deze besturings systemen.

> [!Note]  
> We raden altijd aan de meest recente KB voor uw versie van Windows te nemen.

## <a name="prerequisites"></a>Vereisten 
* **Naam van het opslagaccount**: voor het koppelen van een Azure-bestandsshare hebt u de naam van het opslagaccount nodig.

* **Sleutel van het opslagaccount**: voor het koppelen van een Azure-bestandsshare hebt u de primaire (of secundaire) opslagsleutel nodig. SAS-sleutels worden momenteel niet ondersteund voor koppelen.

* **Zorg ervoor dat poort 445 open is**: het SMB-protocol vereist dat TCP-poort 445 open is; verbindingen mislukken als poort 445 is geblokkeerd. Met de cmdlet `Test-NetConnection` kunt u controleren of uw firewall poort 445 blokkeert. Hier vindt u meer informatie over de verschillende manieren waarop u de [tijdelijke oplossing poort 445 kunt blok keren](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked).

    In de volgende Power shell-code wordt ervan uitgegaan dat de module Azure PowerShell is geïnstalleerd, raadpleegt u [Azure PowerShell module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps) voor meer informatie. Vergeet niet om `<your-storage-account-name>` en `<your-resource-group-name>` te vervangen door de betreffende namen van uw opslagaccount.

    ```powershell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Als de verbinding is geslaagd, hoort u de volgende uitvoer te zien:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > De bovenstaande opdracht retourneert het huidige IP-adres van het opslagaccount. Dit IP-adres blijft niet noodzakelijkerwijs hetzelfde en kan op elk moment veranderen. Neem dit IP-adres niet op in scripts of in de firewallconfiguratie. 

## <a name="using-an-azure-file-share-with-windows"></a>Een Azure-bestandsshare gebruiken met Windows
Als u een Azure-bestandsshare met Windows wilt gebruiken, moet u deze koppelen, wat betekent dat u er een stationsletter of koppelingspunt aan moet toewijzen. U kunt ook toegang krijgen tot de share via het [UNC-pad](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

In tegenstelling tot andere SMB-shares waarmee u mogelijk te maken hebt gehad, zoals die worden gehost op een Windows Server, Linux Samba-server of NAS-apparaat, ondersteunt Azure-bestandsshares momenteel geen Kerberos-verificatie met uw Active Directory- (AD) of Azure Active Directory-identiteit (AAD). Er wordt momenteel aan deze functie [gewerkt](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles). In plaats daarvan krijgt u toegang tot uw Azure-bestandsshare met de opslagaccountsleutel van het opslagaccount dat uw Azure-bestandsshare bevat. Een sleutel voor een opslag account is een beheerders sleutel voor een opslag account, met inbegrip van beheerders machtigingen voor alle bestanden en mappen in de bestands share waartoe u toegang hebt, en voor alle bestands shares en andere opslag resources (blobs, wacht rijen, tabellen, enzovoort) binnen uw opslag account. Als dit niet voldoende is voor uw werkbelasting, is [Azure File Sync](storage-sync-files-planning.md) in de tussentijd mogelijk de oplossing voor het gebrek aan Kerberos-verificatie en ACL-ondersteuning, tot Kerberos-verificatie op basis van AAD- en ACL-ondersteuning openbaar beschikbaar is.

Een algemeen patroon om Line-Of-Business-toepassingen die een SMB-bestandsshare van Azure verwachten te verplaatsen, is om een Azure-bestandsshare te gebruiken als alternatief voor het uitvoeren van een toegewezen Windows-bestandsserver in een virtuele Azure-machine. Als u een Line-Of-Business-toepassing succesvol wilt migreren zodat deze een Azure-bestandsshare gebruikt, is een belangrijk aandachtspunt dat veel Line-of-Business-toepassingen worden uitgevoerd in de context van een toegewezen serviceaccount met beperkte systeemmachtigingen, in plaats van het beheerdersaccount van de virtuele machine. U moet er daarom voor zorgen dat u de referenties voor de Azure-bestandsshare koppelt/opslaat in de context van een serviceaccount in plaats van uw beheerdersaccount.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Permanente referenties voor een Azure-bestandsshare in Windows  
Met het hulpprogramma [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) kunt u de referenties van uw opslagaccount bewaren in Windows. Dit betekent dat wanneer u toegang wilt krijgen tot een Azure-bestandsshare via het UNC-pad of als u de share wilt koppelen, u uw referenties niet hoeft op te geven. Als u de referenties van uw opslagaccount wilt bewaren, voert u de volgende PowerShell-opdrachten uit, waarbij u `<your-storage-account-name>` en `<your-resource-group-name>` vervangt waar dat nodig is.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

U kunt controleren of het hulpprogramma cmdkey uw referenties voor het opslagaccount heeft opgeslagen met de lijstparameter:

```powershell
cmdkey /list
```

Als de referenties voor uw Azure-bestandsshare succesvol zijn opgeslagen, hoort de verwachte uitvoer er als volgt uit te zien (er kunnen meerdere sleutels worden bewaard in de lijst):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

U zou nu toegang moeten hebben tot de share of de share moeten kunnen koppelen zonder extra referenties op te geven.

#### <a name="advanced-cmdkey-scenarios"></a>Geavanceerde cmdkey-scenario's
Er kunnen nog twee cmdkey-scenario's worden overwogen: referenties voor een andere gebruiker op de machine opslaan, zoals een serviceaccount, en referenties opslaan op een externe machine met externe toegang via PowerShell.

De referenties voor een andere gebruiker op de machine opslaan is erg eenvoudig: wanneer u zich bij uw account aanmeldt, voert u de volgende PowerShell-opdracht uit:

```powershell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Hiermee wordt een nieuw PowerShell-venster in de gebruikerscontext van uw serviceaccount (of gebruikersaccount) geopend. U kunt vervolgens het cmdkey-hulpprogramma gebruiken zoals [hierboven](#persisting-azure-file-share-credentials-in-windows) is beschreven.

De referenties op een externe machine opslaan via externe toegang via Powershell is echter niet mogelijk, omdat cmdkey geen toegang tot de referentieopslag toestaat wanneer de gebruiker zich heeft aangemeld via externe toegang via PowerShell, zelfs niet voor aanvullingen. We raden u aan om u bij de machine aan te melden met [Extern bureaublad](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>De Azure-bestandsshare koppelen met PowerShell
Voer de volgende opdrachten uit via een standaard PowerShell-sessie (dus zonder verhoogde bevoegdheden) om de Azure-bestandsshare te koppelen. Vergeet niet om `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` en `<desired-drive-letter>` te vervangen door de juiste informatie.

```powershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> Als u de optie `-Persist` in de cmdlet `New-PSDrive` gebruikt, zorgt dat er alleen voor dat de bestandsshare bij opstarten kan worden ontkoppeld als de referenties zijn bewaard. U kunt de referenties bewaren met de cmdkey, zoals [eerder is beschreven](#persisting-azure-file-share-credentials-in-windows). 

Indien gewenst, kunt u de Azure-bestandsshare ontkoppelen met de volgende PowerShell-cmdlet.

```powershell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>De Azure-bestandsshare koppelen met de Verkenner
> [!Note]  
> Houd er rekening mee dat de volgende instructies worden weergegeven in Windows 10 en enigszins kunnen verschillen in oudere versies. 

1. Open Verkenner. Dit kan worden gedaan door deze te openen vanuit het menu Start of door op de snelkoppeling Win + E te drukken.

2. Ga naar het item **Deze pc** aan de linkerkant van het venster. Hiermee wijzigt u de menu's die beschikbaar zijn in het lint. Selecteer in het menu Computer **Netwerkverbinding maken**.
    
    ![Een schermafbeelding van de vervolgkeuzelijst 'Netwerkverbinding maken'](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Kopieer het UNC-pad in het deelvenster **Verbinding maken** in Azure Portal. 

    ![Het UNC-pad in het deelvenster Verbinding maken van Azure Files](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Selecteer de stationsletter en voer het UNC-pad in. 
    
    ![Een schermafbeelding van het dialoogvenster 'Netwerkverbinding maken'](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Gebruik de opslagaccountnaam voorafgegaan door `AZURE\` als de gebruikersnaam en een toegangssleutel als het wachtwoord.
    
    ![Een schermafbeelding van het dialoogvenster voor netwerkreferenties](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Gebruik de Azure-bestandsshare naar wens.
    
    ![De Azure-bestandsshare is nu gekoppeld](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Wanneer u klaar bent om de Azure-bestandsshare te ontkoppelen, kunt u dit doen door met de rechtermuisknop in de Verkenner op de vermelding voor de share onder **Netwerklocaties** te klikken en **Verbinding verbreken** te selecteren.

### <a name="accessing-share-snapshots-from-windows"></a>Toegang tot momentopnamen van Windows-shares
Als u een momentopname van een share hebt gemaakt, ofwel handmatig ofwel automatisch met behulp van een script of een service zoals Azure Backup, kunt u eerdere versies van een share, een map of een bepaald bestand op een bestandsshare van Windows bekijken. U kunt een moment opname van een share maken vanuit de [Azure Portal](storage-how-to-use-files-portal.md), [Azure POWERSHELL](storage-how-to-use-files-powershell.md)en [Azure cli](storage-how-to-use-files-cli.md).

#### <a name="list-previous-versions"></a>Vorige versies weergeven
Blader naar het item of het bovenliggende item dat moet worden teruggezet. Dubbelklik om naar de gewenste map te gaan. Klik er met de rechtermuisknop op en selecteer **Eigenschappen** in het menu.

![Snelmenu voor een geselecteerde map](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Selecteer **Vorige versies** om de lijst met momentopnamen van shares voor deze map weer te geven. Het kan enkele seconden duren voordat deze lijst is geladen. Dit hangt af van de netwerksnelheid en het aantal momentopnamen van shares in de map.

![Tabblad Vorige versies](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

U kunt **Openen** selecteren om een bepaalde momentopname te openen. 

![Geopende momentopname](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Terugzetten op basis van een vorige versie
Selecteer **Terugzetten** om de inhoud van de gehele map op de aanmaaktijd van de momentopname van de share recursief naar de oorspronkelijke locatie te kopiëren.
 ![Knop Terugzetten in waarschuwingsbericht](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Windows/Windows Server beveiligen
Als u een Azure-bestandsshare in Windows wilt koppelen, moet poort 445 toegankelijk zijn. Veel organisaties blokkeren poort 445 vanwege de beveiligingsrisico's die bij SMB 1 horen. SMB 1, ook wel bekend als CIFS (Common Internet File System), is een oud bestandssysteemprotocol in Windows en Windows Server. SMB 1 is een verouderd, inefficiënt en bovenal onveilig protocol. Het goede nieuws is dat Azure Files SMB 1 niet ondersteunt en dat alle ondersteunde versies van Windows en Windows Server het mogelijk maken om SMB 1 te verwijderen of uit te schakelen. We raden altijd [ten zeerste](https://aka.ms/stopusingsmb1) aan om de SMB 1-client en -server in Windows te verwijderen of uit te schakelen voordat u Azure-bestandsshares in een productieomgeving gebruikt.

In de volgende tabel staat gedetailleerde informatie over de status van SMB 1 in elke versie van Windows:

| Windows-versie                           | Standaardstatus van SMB 1 | Uitschakel-/verwijdermethode       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019                       | Uitgeschakeld             | Verwijderen met Windows-functie |
| Windows Server-versie 1709 en hoger            | Uitgeschakeld             | Verwijderen met Windows-functie |
| Windows 10-versie 1709 en hoger                | Uitgeschakeld             | Verwijderen met Windows-functie |
| Windows Server 2016                       | Ingeschakeld              | Verwijderen met Windows-functie |
| Windows 10-versies 1507, 1607 en 1703 | Ingeschakeld              | Verwijderen met Windows-functie |
| Windows Server 2012 R2                    | Ingeschakeld              | Verwijderen met Windows-functie | 
| Windows 8.1                               | Ingeschakeld              | Verwijderen met Windows-functie | 
| Windows Server 2012                       | Ingeschakeld              | Uitschakelen met Register       | 
| Windows Server 2008 R2                    | Ingeschakeld              | Uitschakelen met Register       |
| Windows 7                                 | Ingeschakeld              | Uitschakelen met Register       | 

### <a name="auditing-smb-1-usage"></a>SMB 1-gebruik controleren
> Van toepassing op Windows Server 2019, Windows Server Semi-Annual-kanaal (versie 1709 en 1803), Windows Server 2016, Windows 10 (versies 1507, 1607, 1703, 1709 en 1803), Windows Server 2012 R2 en Windows 8,1

Voordat u SMB 1 uit uw omgeving verwijdert, wilt u mogelijk het gebruik van SMB 1 controleren om na te gaan of er geen clients beschadigd raken door de wijziging. Als er verzoeken worden gedaan aan SMB-shares met SMB 1, wordt er in het gebeurtenissenlogboek onder `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit` een controlegebeurtenis geregistreerd. 

> [!Note]  
> Als u ondersteuning voor controle wilt inschakelen op Windows Server 2012 R2 en Windows 8.1, installeert u ten minste [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Als u controle wilt inschakelen, voert u de volgende cmdlet uit in een PowerShell-sessie met verhoogde bevoegdheden:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>SMB 1 verwijderen van Windows Server
> Van toepassing op Windows Server 2019, Windows Server Semi-Annual-kanaal (versie 1709 en 1803), Windows Server 2016, Windows Server 2012 R2

Als u SMB 1 van een Windows Server-exemplaar wilt verwijderen, voert u de volgende cmdlet uit in een PowerShell-sessie met verhoogde bevoegdheden:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Start uw server opnieuw op om het verwijderingsproces te voltooien. 

> [!Note]  
> Vanaf Windows 10 en Windows Server-versie 1709 is SMB 1 niet meer standaard geïnstalleerd en heeft SMB 1 afzonderlijke Windows-functies voor de SMB 1-client en SMB 1-server. We raden altijd aan om zowel de SMB 1-server (`FS-SMB1-SERVER`) als de SMB 1-client (`FS-SMB1-CLIENT`) niet te installeren.

### <a name="removing-smb-1-from-windows-client"></a>SMB 1 verwijderen van een Windows-client
> Van toepassing op Windows 10 (versies 1507, 1607, 1703, 1709 en 1803) en Windows 8.1

Als u SMB 1 wilt verwijderen van uw Windows-client, voert u de volgende cmdlet uit in een PowerShell-sessie met verhoogde bevoegdheden:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Start uw pc opnieuw op om het verwijderingsproces te voltooien.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>SMB 1 uitschakelen op oudere versies van Windows/Windows Server
> Van toepassing op Windows Server 2012, Windows Server 2008 R2 en Windows 7

SMB 1 kan niet volledig worden verwijderd van ouders versies van Windows/Windows Server, maar kan worden uitgeschakeld via het register. Als u SMB 1 wilt uitschakelen, maakt u een nieuw registersleutel `SMB1` van type `DWORD` met een waarde van `0` onder `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Dit kunt u ook eenvoudig doen met de volgende PowerShell-cmdlet:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Nadat u deze registersleutel hebt gemaakt, moet u uw server opnieuw opstarten om SMB 1 uit te schakelen.

### <a name="smb-resources"></a>SMB-resources
- [Stoppen met SMB 1 gebruiken](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [SMB 1 detecteren in uw omgeving met DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [SMB 1 uitschakelen via groepsbeleid](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende koppelingen voor meer informatie over Azure Files:
- [Implementatie van Azure Files plannen](storage-files-planning.md)
- [Veelgestelde vragen](../storage-files-faq.md)
- [Problemen oplossen in Windows](storage-troubleshoot-windows-file-connection-problems.md)      
