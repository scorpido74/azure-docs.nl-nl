---
title: Azure-bestandssynchronisatie implementeren | Microsoft Documenten
description: Meer informatie over het implementeren van Azure File Sync, van begin tot eind.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268065"
---
# <a name="deploy-azure-file-sync"></a>Azure Files SYNC implementeren
Gebruik Azure File Sync om de bestandsshares van uw organisatie in Azure-bestanden te centraliseren, terwijl de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver behouden blijven. Door Azure File Sync wordt Windows Server getransformeerd in een snelle cache van uw Azure-bestandsshare. U elk protocol dat beschikbaar is op Windows Server gebruiken om lokaal toegang te krijgen tot uw gegevens, waaronder SMB, NFS en FTPS. Je zoveel caches hebben als je nodig hebt over de hele wereld.

We raden u ten zeerste aan [Planning voor een Azure Files-implementatie](storage-files-planning.md) en Planning voor een Azure File [Sync-implementatie](storage-sync-files-planning.md) te lezen voordat u de in dit artikel beschreven stappen voltooit.

## <a name="prerequisites"></a>Vereisten
* Een Azure-bestandsshare in dezelfde regio die u Azure File Sync wilt implementeren. Zie voor meer informatie:
    - [Beschikbaarheid in regio](storage-sync-files-planning.md#azure-file-sync-region-availability) voor Azure File Sync.
    - [Maak een bestandsshare](storage-how-to-create-file-share.md) voor een stapsgewijze beschrijving van het maken van een bestandsshare.
* Ten minste één ondersteund exemplaar van Windows Server- of Windows Server-cluster om te synchroniseren met Azure File Sync. Zie [Interoperabiliteit met Windows Server](storage-sync-files-planning.md#windows-file-server-considerations)voor meer informatie over ondersteunde versies van Windows Server.
* De Az PowerShell-module kan worden gebruikt met PowerShell 5.1 of PowerShell 6+. U de Az PowerShell-module voor Azure File Sync gebruiken op elk ondersteund systeem, inclusief niet-Windows-systemen, maar de cmdlet voor serverregistratie moet altijd worden uitgevoerd op het Windows Server-exemplaar dat u registreert (dit kan rechtstreeks of via PowerShell worden uitgevoerd remoting). Op Windows Server 2012 R2 u controleren of u ten minste PowerShell 5.1 gebruikt. \* door te kijken naar de waarde van de eigenschap **PSVersion** van het **$PSVersionTable** object:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Als de PSVersion-waarde lager is dan 5.1. \*, zoals het geval zal zijn met de meeste verse installaties van Windows Server 2012 R2, u eenvoudig upgraden door het downloaden en installeren van [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Het geschikte pakket om te downloaden en te installeren voor Windows Server 2012 R2 is **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    PowerShell 6+ kan worden gebruikt met elk ondersteund systeem en kan worden gedownload via de [GitHub-pagina.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Als u van plan bent de gebruikersinterface voor serverregistratie te gebruiken in plaats van u rechtstreeks bij PowerShell te registreren, moet u PowerShell 5.1 gebruiken.

* Als u ervoor hebt gekozen powershell 5.1 te gebruiken, moet u ervoor zorgen dat ten minste .NET 4.7.2 is geïnstalleerd. Meer informatie over [.NET Framework-versies en afhankelijkheden van](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) uw systeem.

    > [!Important]  
    > Als u .NET 4.7.2+ installeert op Windows Server `quiet` Core, moet u installeren met de vlaggen en `norestart` vlaggen of mislukt de installatie. Als u bijvoorbeeld .NET 4.8 installeert, ziet de opdracht er als volgt uit:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* De Az PowerShell-module, die kan worden geïnstalleerd door de instructies hier te volgen: [Azure PowerShell installeren en configureren.](https://docs.microsoft.com/powershell/azure/install-Az-ps)
     
    > [!Note]  
    > De Az.StorageSync module wordt nu automatisch geïnstalleerd wanneer u de Az PowerShell module installeert.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Windows Server voorbereiden voor gebruik met Azure File Sync
Schakel voor elke server die u wilt gebruiken met Azure File Sync, inclusief elk serverknooppunt in een failovercluster, **de verbeterde beveiligingsconfiguratie van Internet Explorer**uit. Dit is alleen vereist voor de eerste serverregistratie. U kunt de optie opnieuw inschakelen nadat de server is geregistreerd.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> U deze stap overslaan als u Azure File Sync implementeert op Windows Server Core.

1. Serverbeheer openen.
2. Klik op **Lokale server:**  
    ![Lokale server aan de linkerkant van de gebruikersinterface van Serverbeheer](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Selecteer in het deelvenster **Eigenschappen** de koppeling naar **Verbeterde beveiliging van Internet Explorer**.  
    ![Het deelvenster Verbeterde beveiliging van Internet Explorer in de gebruikersinterface van Serverbeheer](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Selecteer **Uit** voor **beheerders** en **gebruikers**in het dialoogvenster **Verbeterde beveiligingsconfiguratie van Internet Explorer:**  
    ![Het pop-upvenster Verbeterde beveiliging van Internet Explorer met de optie Uit geselecteerd](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Als u de verbeterde beveiligingsconfiguratie van Internet Explorer wilt uitschakelen, voert u het volgende uit vanuit een verhoogde PowerShell-sessie:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Opslagsynchronisatieservice implementeren 
De implementatie van Azure File Sync begint met het plaatsen van een **Storage Sync Service-bron** in een brongroep van uw geselecteerde abonnement. Wij raden aan om er zo weinig van in te richten als nodig is. U maakt een vertrouwensrelatie tussen uw servers en deze bron en een server kan alleen worden geregistreerd bij één Storage Sync Service. Als gevolg hiervan wordt aanbevolen om zoveel opslagsynchronisatieservices te implementeren als u nodig hebt om groepen servers te scheiden. Houd er rekening mee dat servers van verschillende opslagsynchronisatieservices niet met elkaar kunnen synchroniseren.

> [!Note]
> De Storage Sync Service neemt toegangsmachtigingen over van de abonnements- en resourcegroep waarbinnen is geïmplementeerd. We raden u aan zorgvuldig te controleren wie er toegang toe heeft. Entiteiten met schrijftoegang kunnen beginnen met het synchroniseren van nieuwe sets bestanden van servers die zijn geregistreerd op deze synchronisatieservice voor opslagen en ervoor zorgen dat gegevens worden doorgestroomd naar Azure-opslag die voor hen toegankelijk is.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een opslagsynchronisatieservice wilt implementeren, gaat u naar de [Azure-portal,](https://portal.azure.com/)klikt u op *Een bron maken* en vervolgens zoeken naar Azure File Sync. Selecteer in de zoekresultaten **Azure File Sync**en selecteer Vervolgens **Maken** om het tabblad **Opslagsynchronisatie implementeren** te openen.

Voer de volgende gegevens in in het deelvenster dat verschijnt:

- **Naam**: Een unieke naam (per abonnement) voor de Storage Sync Service.
- **Abonnement:** het abonnement waarin u de Storage Sync Service wilt maken. Afhankelijk van de configuratiestrategie van uw organisatie heeft u mogelijk toegang tot een of meer abonnementen. Een Azure-abonnement is de meest elementaire container voor facturering voor elke cloudservice (zoals Azure Files).
- **Resourcegroep**: Een resourcegroep is een logische groep Azure-bronnen, zoals een opslagaccount of een Opslagsynchronisatieservice. U een nieuwe brongroep maken of een bestaande brongroep gebruiken voor Azure File Sync. (We raden u aan resourcegroepen als containers te gebruiken om resources logisch voor uw organisatie te isoleren, zoals het groeperen van HR-resources of resources voor een specifiek project.)
- **Locatie:** het gebied waarin u Azure File Sync wilt implementeren. Alleen ondersteunde regio's zijn beschikbaar in deze lijst.

Wanneer u klaar bent, selecteert u **Maken** om de opslagsynchronisatieservice te implementeren.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Vervang `<Az_Region>` `<RG_Name>`, `<my_storage_sync_service>` en met uw eigen waarden, gebruik vervolgens de volgende opdrachten om een Storage Sync Service te maken en te implementeren:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Azure File Sync-agent installeren
De Azure File Sync-agent is een downloadbaar pakket waardoor Windows Server met een Azure-bestandsshare kan worden gesynchroniseerd. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
U de agent downloaden vanuit het [Microsoft Download Center.](https://go.microsoft.com/fwlink/?linkid=858257) Wanneer de download is voltooid, dubbelklikt u op het MSI-pakket om de installatie van de Azure File Sync-agent te starten.

> [!Important]  
> Als u Azure File Sync wilt gebruiken met een failovercluster, moet de Azure File Sync-agent op elk knooppunt in het cluster zijn geïnstalleerd. Elk knooppunt in het cluster moet zijn geregistreerd om te werken met Azure File Sync.

Wij raden u aan het volgende te doen:
- Verlaat het standaardinstallatiepad (C:\Program Files\Azure\StorageSyncAgent) om probleemoplossing en serveronderhoud te vereenvoudigen.
- Schakel Microsoft Update in om Azure File Sync up-to-date te houden. Alle updates, naar de Azure File Sync-agent, inclusief functie-updates en hotfixes, vinden plaats vanuit Microsoft Update. We raden u aan de nieuwste update voor Azure File Sync te installeren. Zie [Azure File Sync-updatebeleid](storage-sync-files-planning.md#azure-file-sync-agent-update-policy)voor meer informatie .

Wanneer de installatie van de Azure File Sync-agent is voltooid, wordt de gebruikersinterface voor serverregistratie automatisch geopend. U moet een Storage Sync-service hebben voordat u zich registreert; zie de volgende sectie over het maken van een Storage Sync Service.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Voer de volgende PowerShell-code uit om de juiste versie van de Azure File Sync-agent voor uw besturingssysteem te downloaden en op uw systeem te installeren.

> [!Important]  
> Als u Azure File Sync wilt gebruiken met een failovercluster, moet de Azure File Sync-agent op elk knooppunt in het cluster zijn geïnstalleerd. Elk knooppunt in het cluster moet zijn geregistreerd om te werken met Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Windows Server registreren met opslagsynchronisatieservice
Als u Windows Server registreert voor een opslagsynchronisatieservice, wordt er een vertrouwensrelatie ingesteld tussen uw server (of cluster) en de opslagsynchronisatieservice. Een server kan slechts voor één opslagsynchronisatieservice worden geregistreerd en kan worden gesynchroniseerd met andere servers en Azure-bestandsshares die aan dezelfde opslagsynchronisatieservice zijn gekoppeld.

> [!Note]
> Serverregistratie gebruikt uw Azure-referenties om een vertrouwensrelatie te maken tussen de Storage Sync Service en uw Windows Server, maar vervolgens maakt en gebruikt de server zijn eigen identiteit die geldig is zolang de server geregistreerd blijft en de huidige Shared Access Signature-token (Storage SAS) is geldig. Een nieuw SAS-token kan niet aan de server worden uitgegeven zodra de server is niet geregistreerd, waardoor de server geen toegang meer heeft tot uw Azure-bestandsshares, waardoor synchronisatie wordt gestopt.

# <a name="portal"></a>[Portal](#tab/azure-portal)
De gebruikersinterface voor serverregistratie moet automatisch worden geopend na installatie van de Azure File Sync-agent. Als dat niet gebeurt, kunt u deze handmatig openen vanuit de bestandslocatie: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Wanneer de gebruikersinterface voor serverregistratie wordt geopend, selecteert u **Aanmelden** om te beginnen.

Nadat u zich hebt aangemeld, wordt u om de volgende informatie gevraagd:

![Schermafbeelding van de gebruikersinterface van de serverregistratie](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-abonnement**: het abonnement dat de Storage Sync Service bevat (zie [De Opslagsynchronisatieservice implementeren).](#deploy-the-storage-sync-service) 
- **Resourcegroep:** de resourcegroep die de Storage Sync Service bevat.
- **Storage Sync Service:** de naam van de Storage Sync Service waarmee u zich wilt registreren.

Nadat u de juiste informatie hebt geselecteerd, selecteert u **Registreren** om de serverregistratie te voltooien. Als deel van het registratieproces wordt u gevraagd u nogmaals aan te melden.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Synchronisatiegroep en cloudeindpunt maken
Een synchronisatiegroep definieert de synchronisatietopologie voor een verzameling bestanden. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Een synchronisatiegroep moet één cloudeindpunt bevatten, dat een Azure-bestandsshare en een of meer servereindpunten representeert. Een servereindpunt vertegenwoordigt een pad op een geregistreerde server. Een server kan servereindpunten in meerdere synchronisatiegroepen hebben. U zoveel synchronisatiegroepen maken als u nodig hebt om de gewenste synchronisatietopologie op de juiste manier te beschrijven.

Een eindpunt in de cloud is een aanwijzer voor een Azure-bestandsshare. Alle servereindpunten worden gesynchroniseerd met een eindpunt in de cloud, waardoor het cloudeindpunt de hub wordt. Het opslagaccount voor het Azure-bestandsaandeel moet zich in dezelfde regio bevinden als de Storage Sync Service. Het volledige azure-bestandsaandeel wordt gesynchroniseerd, met één uitzondering: een speciale map, vergelijkbaar met de verborgen map 'Systeemvolumegegevens' op een NTFS-volume, wordt ingericht. Deze directory heet ". SystemShareInformatie". Het bevat belangrijke synchronisatiemetagegevens die niet worden gesynchroniseerd met andere eindpunten. Gebruik of verwijder het niet!

> [!Important]  
> U wijzigingen aanbrengen in elk eindpunt in de cloud of het eindpunt van de server in de synchronisatiegroep en uw bestanden laten synchroniseren met de andere eindpunten in de synchronisatiegroep. Als u het cloudeindpunt (Azure-bestandsshare) rechtstreeks wijzigt, moeten wijzigingen eerst worden gedetecteerd door een azure file sync-wijzigingsdetectietaak. Een wijzigingsdetectietaak wordt slechts eenmaal per 24 uur gestart voor een cloudeindpunt. Zie [Azure Files voor](storage-files-faq.md#afs-change-detection)meer informatie.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een synchronisatiegroep wilt maken, gaat u in de [Azure-portal](https://portal.azure.com/)naar uw Opslagsynchronisatieservice en selecteert u **+ Synchronisatiegroep**:

![Een nieuwe synchronisatiegroep in de Azure-portal maken](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

Voer in het deelvenster dat verschijnt de volgende gegevens in om een synchronisatiegroep met een cloudeindpunt te maken:

- **Groepsnaam synchroniseren:** de naam van de synchronisatiegroep die moet worden gemaakt. Deze naam moet uniek zijn binnen de opslagsynchronisatieservice, maar het mag een willekeurige naam zijn die u makkelijk kunt onthouden.
- **Abonnement:** het abonnement waarbij u de Storage Sync Service hebt geïmplementeerd in [De Opslagsynchronisatieservice implementeren](#deploy-the-storage-sync-service).
- **Opslagaccount:** als u **Opslagaccount selecteren**selecteert, verschijnt er een ander deelvenster waarin u het opslagaccount selecteren met de Azure-bestandsshare waarmee u wilt synchroniseren.
- **Azure-bestandsshare:** de naam van het Azure-bestandsaandeel waarmee u wilt synchroniseren.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Als u de synchronisatiegroep wilt maken, voert u de volgende PowerShell uit. Vergeet niet `<my-sync-group>` om te vervangen door de gewenste naam van de synchronisatiegroep.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Zodra de synchronisatiegroep is gemaakt, u uw eindpunt in de cloud maken. Zorg ervoor `<my-storage-account>` dat `<my-file-share>` u de verwachte waarden vervangt en doordeeren.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Servereindpunt maken
Een servereindpunt representeert een bepaalde locatie op een geregistreerde server, bijvoorbeeld een map op een servervolume. Een servereindpunt moet een pad op een geregistreerde server zijn (in plaats van een gemonteerd aandeel) en om cloudtiering te gebruiken, moet het pad zich op een niet-systeemvolume bevinden. Netwerkopslag (NAS) wordt niet ondersteund.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een servereindpunt wilt toevoegen, gaat u naar de nieuw gemaakte synchronisatiegroep en selecteert u **Servereindpunt toevoegen**.

![Nieuw servereindpunt toevoegen in het deelvenster met de synchronisatiegroep](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

Voer in het deelvenster **Servereindpunt toevoegen** de volgende gegevens in om een servereindpunt te maken:

- **Geregistreerde server:** de naam van de server of het cluster waar u het servereindpunt wilt maken.
- **Pad:** het Windows Server-pad dat moet worden gesynchroniseerd als onderdeel van de synchronisatiegroep.
- **Cloudtiering:** een switch om cloudtiering in of uit te schakelen. Met cloudlagen kunnen zelden gebruikte of geopende bestanden worden gelaagd naar Azure-bestanden.
- **Volumevrije ruimte:** de hoeveelheid vrije ruimte die u reserveren op het volume waarop het servereindpunt zich bevindt. Als bijvoorbeeld volumevrije ruimte is ingesteld op 50% op een volume met één servereindpunt, wordt ongeveer de helft van de hoeveelheid gegevens gelaagd naar Azure-bestanden. Ongeacht of cloudtiering is ingeschakeld, heeft uw Azure-bestandsshare altijd een volledige kopie van de gegevens in de synchronisatiegroep.

Als u het servereindpunt wilt toevoegen, selecteert u **Maken**. Uw bestanden worden nu gesynchroniseerd in uw Azure-bestandsshare en Windows Server. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Voer de volgende PowerShell-opdrachten uit om het servereindpunt `<your-server-endpoint-path>` `<your-volume-free-space>` te maken en zorg ervoor dat u de gewenste waarden vervangt en met de gewenste waarden.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Instellingen voor de firewall en het virtuele netwerk configureren

### <a name="portal"></a>Portal
Als u de synchronisatie van Uw Azure-bestand wilt configureren om te werken met firewall- en virtuele netwerkinstellingen, gaat u als volgt te werk:

1. Navigeer vanuit de Azure-portal naar het opslagaccount dat u wilt beveiligen.
1. Selecteer de knop **Firewalls en virtuele netwerken** in het linkermenu.
1. Selecteer **Geselecteerde netwerken** onder Toegang toestaan **van**.
1. Zorg ervoor dat uw servers IP of virtueel netwerk wordt vermeld onder de juiste sectie.
1. Controleer of **vertrouwde Microsoft-services toegang hebben tot dit opslagaccount** is ingeschakeld.
1. Selecteer **Opslaan** om uw instellingen op te slaan.

![Firewall- en virtuele netwerkinstellingen configureren om te werken met Azure File-synchronisatie](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding met Azure File Sync
De aanbevolen stappen om aan boord te gaan op Azure File Sync voor de eerste met nul downtime met behoud van volledige bestandstrouw en toegangscontrolelijst (ACL) zijn als volgt:
 
1. Een storagesyncservice implementeren.
2. Maak een synchronisatiegroep.
3. Installeer Azure File Sync-agent op de server met de volledige gegevensset.
4. Registreer die server en maak een servereindpunt voor het aandeel. 
5. Laat de volledige upload naar het Azure-bestandsshare (eindpunt voor de cloud) volledig uploaden.  
6. Nadat de eerste upload is voltooid, installeert u Azure File Sync-agent op elk van de resterende servers.
7. Maak nieuwe bestandsshares op elk van de resterende servers.
8. Maak desgewenst servereindpunten op nieuwe bestandsshares met cloudtieringbeleid. (Voor deze stap is extra opslagruimte vereist die beschikbaar is voor de eerste installatie.)
9. Laat azure File Sync-agent de volledige naamruimte snel herstellen zonder de werkelijke gegevensoverdracht. Na de volledige naamruimtesynchronisatie vult de synchronisatieengine de lokale schijfruimte op basis van het cloudtieringsbeleid voor het servereindpunt. 
10. Zorg ervoor dat de synchronisatie is voltooid en test uw topologie naar wens. 
11. Leid gebruikers en toepassingen om naar dit nieuwe aandeel.
12. U optioneel dubbele aandelen op de servers verwijderen.
 
Als u geen extra opslagruimte hebt voor de eerste onboarding en deze wilt koppelen aan de bestaande shares, u de gegevens vooraf indebestanden van Azure-bestanden voorzetten. Deze aanpak wordt voorgesteld, als en alleen als u downtime accepteren en absoluut geen gegevenswijzigingen op de serveraandelen garandeert tijdens het eerste onboardingproces. 
 
1. Zorg ervoor dat gegevens op een van de servers niet kunnen worden gewijzigd tijdens het onboardingproces.
2. Pre-seed Azure-bestand deelt met de servergegevens met behulp van een tool voor gegevensoverdracht via bijvoorbeeld Robocopy, directe SMB-kopie. Aangezien AzCopy geen gegevens uploadt via de SMB, kan deze niet worden gebruikt voor pre-seeding.
3. Maak Azure File Sync-topologie met de gewenste servereindpunten die naar de bestaande shares verwijzen.
4. Laat het afstemmingsproces op alle eindpunten synchroniseren. 
5. Zodra de afstemming is voltooid, u aandelen openen voor wijzigingen.
 
Momenteel heeft pre-seeding aanpak een paar beperkingen - 
- Volledige getrouwheid op bestanden blijft niet behouden. Bestanden verliezen bijvoorbeeld ACL's en tijdstempels.
- Gegevenswijzigingen op de server voordat de synchronisatietopologie volledig is ingeschakeld, kunnen conflicten veroorzaken op de eindpunten van de server.  
- Nadat het cloudeindpunt is gemaakt, voert Azure File Sync een proces uit om de bestanden in de cloud te detecteren voordat de eerste synchronisatie wordt gestart. De tijd die nodig is om dit proces te voltooien, is afhankelijk van de verschillende factoren zoals netwerksnelheid, beschikbare bandbreedte en het aantal bestanden en mappen. Voor de ruwe schatting in de preview-release wordt het detectieproces ongeveer uitgevoerd op 10 bestanden per seconde.  Dus zelfs als pre-zaaien snel verloopt, kan de totale tijd om een volledig lopend systeem te krijgen aanzienlijk langer zijn wanneer gegevens vooraf in de cloud worden gezaaid.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Selfservice herstellen via vorige versies en VSS (Volume Shadow Copy Service)

> [!IMPORTANT]
> De volgende informatie kan alleen worden gebruikt met versie 9 (of hoger) van de opslagsynchronisatieagent. Versies lager dan 9 hebben niet de StorageSyncSelfService-cmdlets.

Vorige versies is een Windows-functie waarmee u VSS-snapshots aan de serverzijde van een volume gebruiken om herstelbare versies van een bestand aan een SMB-client te presenteren.
Dit maakt een krachtig scenario mogelijk, ook wel selfserviceherstel genoemd, rechtstreeks voor informatiewerkers in plaats van afhankelijk van het herstel van een IT-beheerder.

VSS-momentopnamen en vorige versies werken onafhankelijk van Azure File Sync. Cloudtiering moet echter worden ingesteld op een compatibele modus. Veel azure file sync server-eindpunten kunnen op hetzelfde volume bestaan. U moet de volgende PowerShell-aanroep per volume voeren met zelfs één servereindpunt waar u cloudlagen plant of gebruikt.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-momentopnamen worden gemaakt van een volledig volume. Standaard kunnen er maximaal 64 momentopnamen bestaan voor een bepaald volume, verleend is er voldoende ruimte om de snapshots op te slaan. VSS verwerkt dit automatisch. Het standaardmomentopnameschema maakt twee momentopnamen per dag, van maandag tot en met vrijdag. Dat schema is configureerbaar via een Geplande Windows-taak. De bovenstaande PowerShell-cmdlet doet twee dingen:
1. Het configureert Azure File Syncs cloud tiering op het opgegeven volume om compatibel te zijn met eerdere versies en garandeert dat een bestand kan worden hersteld vanaf een vorige versie, zelfs als het is gelaagd naar de cloud op de server. 
2. Hiermee wordt het standaard VSS-schema ingeschakeld. U dan besluiten om het later te wijzigen. 

> [!Note]  
> Er zijn twee belangrijke dingen op te merken:
>- Als u de parameter -Force gebruikt en VSS momenteel is ingeschakeld, overschrijft deze het huidige VSS-momentopnameschema en vervangt deze door het standaardschema. Zorg ervoor dat u uw aangepaste configuratie opslaat voordat u de cmdlet uitvoert.
> - Als u deze cmdlet op een clusterknooppunt gebruikt, moet u deze ook uitvoeren op alle andere knooppunten in het cluster! 

Als u wilt zien of de compatibiliteit voor selfserviceherstel is ingeschakeld, u de volgende cmdlet uitvoeren.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Het zal een lijst van alle volumes op de server, evenals het aantal cloud tiering compatibele dagen voor elk. Dit nummer wordt automatisch berekend op basis van de maximaal mogelijke momentopnamen per volume en het standaard momentopnameschema. Dus standaard kunnen alle vorige versies die aan een informatiemedewerker worden gepresenteerd, worden gebruikt om te herstellen van. Hetzelfde geldt als u het standaardschema wijzigt om meer momentopnamen te maken.
Als u de planning echter wijzigt op een manier die resulteert in een beschikbare momentopname op het volume dat ouder is dan de waarde van compatibele dagen, kunnen gebruikers deze oudere momentopname (vorige versie) niet gebruiken om te herstellen.

> [!Note]
> Het inschakelen van selfserviceherstel kan van invloed zijn op het verbruik en de factuur van uw Azure-opslag. Deze impact is beperkt tot bestanden die momenteel op de server zijn gegelaagd. Als u deze functie inschakelt, zorgt u ervoor dat er een bestandsversie beschikbaar is in de cloud waarnaar kan worden verwezen via een eerdere versie (VSS snapshot)-vermelding.
>
> Als u de functie uitschakelt, neemt het Azure-opslagverbruik langzaam af totdat het compatibele dagenvenster is verstreken. Er is geen manier om dit te versnellen. 

Het standaard maximumaantal VSS-momentopnamen per volume (64) en het standaardschema om ze te gebruiken, resulteren in maximaal 45 dagen van eerdere versies waarvan een informatiewerker kan herstellen, afhankelijk van het aantal VSS-momentopnamen dat u op uw volume opslaan.

Als max. 64 VSS-momentopnamen per volume is niet de juiste instelling voor u, u [die waarde wijzigen via een registersleutel.](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)
Als de nieuwe limiet van kracht wordt, moet u de cmdlet opnieuw uitvoeren om de compatibiliteit van vorige versies in te schakelen op elk volume dat eerder is ingeschakeld, met de vlag -Force om rekening te houden met het nieuwe maximumaantal VSS-momentopnamen per volume. Dit zal resulteren in een nieuw berekend aantal compatibele dagen. Houd er rekening mee dat deze wijziging alleen van kracht wordt op nieuw gelaagde bestanden en eventuele aanpassingen over het VSS-schema dat u mogelijk hebt gemaakt, overschrijft.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Een DFS-replicatieimplementatie (DFS-R) migreren naar Azure File Sync
Een DFS-R-implementatie migreren naar Azure File Sync:

1. Maak een synchronisatiegroep om de DFS-R-topologie weer te geven die u vervangt.
2. Begin op de server met de volledige set gegevens in uw DFS-R-topologie om te migreren. Installeer Azure File Sync op die server.
3. Registreer die server en maak een servereindpunt voor de eerste server die is gemigreerd. Schakel cloudtiering niet in.
4. Laat alle gegevens synchroniseren met uw Azure-bestandsshare (eindpunt in de cloud).
5. Installeer en registreer de Azure File Sync-agent op elk van de resterende DFS-R-servers.
6. DFS-R uitschakelen. 
7. Maak een servereindpunt op elk van de DFS-R-servers. Schakel cloudtiering niet in.
8. Zorg ervoor dat de synchronisatie is voltooid en test uw topologie naar wens.
9. DFS-R met pensioen laten gaan.
10. Cloudtiering kan nu naar wens worden ingeschakeld op elk servereindpunt.

Zie [Azure File Sync interop met Dfs (Distributed File System)](storage-sync-files-planning.md#distributed-file-system-dfs)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- [Een Eindpunt van Azure File Sync Server toevoegen of verwijderen](storage-sync-files-server-endpoint.md)
- [Een server registreren of uitschrijven met Azure File Sync](storage-sync-files-server-registration.md)
- [Azure File Sync bewaken](storage-sync-files-monitoring.md)
