---
title: Windows Virtual Desktop MSIX-app koppelen - Azure
description: MSIX-app koppelen instellen voor Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 20a82cbd7de4b5678648bac19ab9b59bf557b0ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128314"
---
# <a name="set-up-msix-app-attach"></a>MSIX-app-koppeling instellen

> [!IMPORTANT]
> MSIX app attach is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

In dit onderwerp u meelopen hoe u MSIX-app-bijlage in een Windows Virtual Desktop-omgeving instellen.

## <a name="requirements"></a>Vereisten

Voordat u aan de slag gaat, moet u het koppelen van de MSIX-app configureren:

- Toegang tot de Windows Insider-portal om de versie van Windows 10 te verkrijgen met ondersteuning voor de MSIX-app bevestig-API's.
- Een goed werkende Windows Virtual Desktop-implementatie. Zie Een [tenant maken in Windows Virtueel bureaublad](tenant-setup-azure-active-directory.md)voor meer informatie.
- Het MSIX verpakkingsgereedschap
- Een netwerkaandeel in uw Windows Virtual Desktop-implementatie waarbij het MSIX-pakket wordt opgeslagen

## <a name="get-the-os-image"></a>De afbeelding van het besturingssysteem weergeven

Eerst moet je de OS-afbeelding krijgen die je voor de MSIX-app gebruikt. Ga als u de os-afbeelding bekijken:

1. Open de [Windows Insider-portal](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) en meld u aan.

     >[!NOTE]
     >U moet lid zijn van het Windows Insider-programma om toegang te krijgen tot de Windows Insider-portal. Bekijk onze [Windows Insider-documentatie](/windows-insider/at-home/)voor meer informatie over het Windows Insider-programma.

2. Blader omlaag naar de sectie **Editie selecteren** en selecteer **Windows 10 Insider Preview Enterprise (FAST) – Build 19035** of hoger.

3. Selecteer **Bevestigen**, selecteer vervolgens de taal die u wilt gebruiken en selecteer **Opnieuw bevestigen.**
    
     >[!NOTE]
     >Op dit moment is Engels de enige taal die is getest met de functie. U andere talen selecteren, maar deze worden mogelijk niet weergegeven zoals bedoeld.
    
4. Wanneer de downloadkoppeling wordt gegenereerd, selecteert u de **64-bits download** en slaat u deze op uw lokale harde schijf op.

## <a name="prepare-the-vhd-image-for-azure"></a>De VHD-afbeelding voorbereiden op Azure 

Voordat u aan de slag gaat, moet u een master VHD-afbeelding maken. Als u uw master VHD-afbeelding nog niet hebt gemaakt, gaat u naar [Een master VHD-afbeelding voorbereiden en aanpassen](set-up-customize-master-image.md) en de instructies daar volgen. 

Nadat u uw hoofdVHD-afbeelding hebt gemaakt, moet u automatische updates voor Toepassingen voor het koppelen van MSIX-apps uitschakelen. Als u automatische updates wilt uitschakelen, moet u de volgende opdrachten uitvoeren in een opdrachtprompt met verhoogde bevoegdheid:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Bereid vervolgens de VM VHD voor Azure voor en upload de resulterende VHD-schijf naar Azure. Zie [Een hoofd VHD-afbeelding voorbereiden en aanpassen](set-up-customize-master-image.md)voor meer informatie.

Zodra u de VHD naar Azure hebt geüpload, maakt u een hostgroep die is gebaseerd op deze nieuwe afbeelding door de instructies in de [groep Een hostgroep maken](create-host-pools-azure-marketplace.md) te volgen met behulp van de zelfstudie Azure Marketplace.

## <a name="prepare-the-application-for-msix-app-attach"></a>De toepassing voorbereiden voor het koppelen van de MSIX-app 

Als u al een MSIX-pakket hebt, gaat u verder [met Windows Virtual Desktop-infrastructuur configureren.](#configure-windows-virtual-desktop-infrastructure) Als u oudere toepassingen wilt testen, volgt u de instructies in [Een MSIX-pakket maken van een desktopinstallatieprogramma op een VM](/windows/msix/packaging-tool/create-app-package-msi-vm/) om de verouderde toepassing om te zetten in een MSIX-pakket.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Een VHD- of VHDX-pakket genereren voor MSIX

Pakketten zijn in VHD- of VHDX-formaat om de prestaties te optimaliseren. MSIX vereist dat VHD- of VHDX-pakketten goed werken.

Ga als lid van MSIX naar een VHD- of VHDX-pakket:

1. [Download het msixmgr-gereedschap](https://aka.ms/msixmgr) en sla de .zip-map op in een map binnen een vm van sessiehost.

2. Rits de msixmgr-map .zip uit.

3. Zet de bron MSIX pakket in dezelfde map waar je de msixmgr tool uitgepakt.

4. Voer de volgende cmdlet uit in PowerShell om een VHD te maken:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Zorg ervoor dat de grootte van VHD groot genoeg is om de uitgebreide MSIX vast te houden.*

5. Voer de volgende cmdlet uit om de nieuw gecreëerde VHD te monteren:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Voer deze cmdlet uit om de VHD te initialiseren:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Voer deze cmdlet uit om een nieuwe partitie te maken:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Voer deze cmdlet uit om de partitie op te maken:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Maak een bovenliggende map op de gemonteerde VHD. Deze stap is verplicht omdat de MSIX-app-bijlage een bovenliggende map vereist. U de bovenliggende map een naam geven wat u maar wilt.

### <a name="expand-msix"></a>MSIX uitbreiden

Daarna moet je de MSIX-afbeelding "uitbreiden" door het uit te pakken. Ga als het gaat om het uitpakken van de MSIX-afbeelding:

1. Open een opdrachtprompt als administrator en navigeer naar de map waar u het msixmgr-gereedschap hebt gedownload en uitgepakt.

2. Voer de volgende cmdlet uit om de MSIX uit te pakken in de VHD die u in de vorige sectie hebt gemaakt en gemonteerd.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Het volgende bericht moet worden weergegeven nadat het uitpakken is gedaan:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Als u pakketten uit de Microsoft Store for Business (of Onderwijs) gebruikt binnen uw netwerk of op apparaten die niet met internet zijn verbonden, moet u de pakketlicenties uit de Store verkrijgen en installeren om de app met succes uit te voeren. Zie [Pakketten offline gebruiken](#use-packages-offline).

3. Navigeer naar de gemonteerde VHD en open de app-map en bevestig dat de inhoud van het pakket aanwezig is.

4. Ontkoppel de VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Windows Virtual Desktop-infrastructuur configureren

Door het ontwerp kan één MSIX-uitgebreid pakket (de VHD die u in de vorige sectie hebt gemaakt) worden gedeeld tussen meerdere vm's van sessiehost, omdat de VHD's in de alleen-lezenmodus zijn gekoppeld.

Controleer voordat u begint of uw netwerkaandeel aan deze vereisten voldoet:

- Het aandeel is compatibel met het MKB.
- De VM's die deel uitmaken van de sessiehostgroep hebben NTFS-machtigingen voor het aandeel.

### <a name="set-up-an-msix-app-attach-share"></a>Een MSIX-app koppelen instellen 

Maak in uw Windows Virtual Desktop-omgeving een netwerkshare en verplaats het pakket daar.

>[!NOTE]
> De beste praktijk voor het maken van MSIX-netwerkshares is het instellen van het netwerkaandeel met NTFS-alleen-lezen machtigingen.

## <a name="install-certificates"></a>Certificaten installeren

Als uw app een certificaat gebruikt dat niet vertrouwd is of zelf is ondertekend, u het als geen ander installeren:

1. Klik met de rechtermuisknop op het pakket en selecteer **Eigenschappen**.
2. Selecteer in het venster dat wordt weergegeven het tabblad **Digitale handtekeningen.** Er mag slechts één item in de lijst op het tabblad staan, zoals in de volgende afbeelding wordt weergegeven. Selecteer dat item om het item te markeren en selecteer **Details**.
3. Wanneer het venster met gegevens voor digitale handtekeningen wordt weergegeven, selecteert u het tabblad **Algemeen** en selecteert u **Certificaat installeren**.
4. Wanneer het installatieprogramma wordt geopend, selecteert u **de lokale machine** als opslaglocatie en selecteert u **Volgende**.
5. Als het installatieprogramma u vraagt of u de app wilt toestaan wijzigingen aan te brengen in uw apparaat, selecteert u **Ja**.
6. Selecteer **Alle certificaten plaatsen in het volgende archief**en selecteer **Bladeren**.
7. Wanneer het venster voor het geselecteerde certificaatopslag wordt weergegeven, selecteert u **Vertrouwde personen**en selecteert u **OK**.
8. Selecteer **Finish**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>PowerShell-scripts voorbereiden voor het koppelen van de MSIX-app

MSIX app attach heeft vier verschillende fasen die in de volgende volgorde moeten worden uitgevoerd:

1. Fase
2. Registreren
3. Uitschrijven
4. Destage

Elke fase maakt een PowerShell-script. Voorbeeldscripts voor elke fase zijn [hier](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)beschikbaar.

### <a name="stage-the-powershell-script"></a>Het PowerShell-script fase

Voordat u de PowerShell-scripts bijwerkt, moet u ervoor zorgen dat u de volume-GUID van het volume in de VHD hebt. Ga als u op het volume GUID:

1.  Open het netwerkdeel waar de VHD zich in de VM bevindt, waar u het script uitvoert.

2.  Klik met de rechtermuisknop op de VHD en selecteer **Monteren**. Dit zal de VHD monteren op een drive letter.

3.  Nadat u de VHD hebt gemonteerd, wordt het venster **Verkenner** geopend. De bovenliggende map vastleggen en de **$parentFolder** variabele bijwerken

    >[!NOTE]
    >Als u geen bovenliggende map ziet, betekent dit dat de MSIX niet goed is uitgebreid. Doe de vorige sectie opnieuw en probeer het opnieuw.

4.  Open de bovenliggende map. Als deze correct is uitgevouwen, ziet u een map met dezelfde naam als het pakket. Werk de **$packageName** variabele bij om de naam van deze map te evenaren.

    Bijvoorbeeld `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Open een opdrachtprompt en voer **mountvol in**. Met deze opdracht wordt een lijst met volumes en hun GUID's weergegeven. Kopieer de GUID van het volume waar de drive letter overeenkomt met het station waarop u uw VHD hebt gemonteerd in stap 2.

    In dit voorbeeld uitvoer bijvoorbeeld voor de opdracht mountvol, als u uw VHD op Station `C:\`C hebt gemonteerd, wilt u de bovenstaande waarde kopiëren:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Werk de **$volumeGuid** variabele bij met de volume-GUID die u zojuist hebt gekopieerd.

7. Open een PowerShell-prompt voor beheerders en werk het volgende PowerShell-script bij met de variabelen die van toepassing zijn op uw omgeving.

    ```powershell
    #MSIX app attach staging sample

    #region variables

    $vhdSrc="<path to vhd>"

    $packageName = "<package name>"

    $parentFolder = "<package parent folder>"

    $parentFolder = "\" + $parentFolder + "\"

    $volumeGuid = "<vol guid>"

    $msixJunction = "C:\temp\AppAttach\"

    #endregion

    #region mountvhd

    try

    {

    Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red

    }

    #endregion

    #region makelink

    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"

    if (!(Test-Path $msixJunction))

    {

    md $msixJunction

    }

    $msixJunction = $msixJunction + $packageName

    cmd.exe /c mklink /j $msixJunction $msixDest

    #endregion

    #region stage

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>PowerShell-script registreren

Als u het registerscript wilt uitvoeren, voert u de volgende PowerShell-cmdlets uit met de tijdelijke aanduidingswaarden die zijn vervangen door waarden die van toepassing zijn op uw omgeving.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path $path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>PowerShell-script uitschrijven

Vervang voor dit script de tijdelijke aanduiding voor **$packageName** door de naam van het pakket dat u test.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>PowerShell-script destage

Vervang voor dit script de tijdelijke aanduiding voor **$packageName** door de naam van het pakket dat u test.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Simulatiescripts instellen voor de MSIX-app attach agent

Nadat u de scripts hebt gemaakt, kunnen gebruikers ze handmatig uitvoeren of instellen dat ze automatisch worden uitgevoerd als scripts opstarten, aanmelden, afmelden en afsluiten. Zie [Opstart-, afsluit-, aanmeldings- en afmeldscripts gebruiken in Groepsbeleid](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)voor meer informatie over dit soort scripts .

Elk van deze automatische scripts voert een fase van de app attach scripts:

- In het opstartscript wordt het werkscript uitgevoerd.
- In het aanmeldingsscript wordt het registerscript uitgevoerd.
- In het afmeldscript wordt het uitschrijfscript uitgevoerd.
- In het afsluitscript wordt het script van destage uitgevoerd.

## <a name="use-packages-offline"></a>Pakketten offline gebruiken

Als u pakketten uit de [Microsoft Store voor Bedrijven](https://businessstore.microsoft.com/) of de Microsoft Store for [Education](https://educationstore.microsoft.com/) binnen uw netwerk of op apparaten die niet met internet zijn verbonden, gebruikt, moet u de pakketlicenties uit de Microsoft Store ophalen en installeren op uw apparaat om de app uit te voeren. Als uw apparaat online is en verbinding kan maken met de Microsoft Store voor Bedrijven, moeten de vereiste licenties automatisch worden gedownload, maar als u offline bent, moet u de licenties handmatig instellen. 

Als u de licentiebestanden wilt installeren, moet u een PowerShell-script gebruiken dat de MDM_EnterpriseModernAppManagement_StoreLicenses02_01 klasse aanroept in de WMI Bridge Provider.  

U de licenties als een van de redenen instellen voor offline gebruik: 

1. Download het app-pakket, licenties en vereiste frameworks uit de Microsoft Store voor Bedrijven. Je hebt zowel de gecodeerde als ongecodeerde licentiebestanden nodig. Gedetailleerde download instructies zijn [hier](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)te vinden.
2. Werk de volgende variabelen in het script bij voor stap 3:
      1. `$contentID`is de ContentID-waarde van het ongecodeerde licentiebestand (.xml). U het licentiebestand openen in een teksteditor naar keuze.
      2. `$licenseBlob`is de volledige tekenreeks voor de licentieblob in het gecodeerde licentiebestand (.bin). U het gecodeerde licentiebestand openen in een teksteditor naar keuze. 
3. Voer het volgende script uit vanuit een PowerShell-prompt voor beheerders. Een goede plek om licentie-installatie uit te voeren is aan het einde van de [faseringsscript](#stage-the-powershell-script) dat ook moet worden uitgevoerd vanaf een admin prompt.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession 

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />' 

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param) 


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}  
```

## <a name="next-steps"></a>Volgende stappen

Deze functie wordt momenteel niet ondersteund, maar u vragen stellen aan de community op de [Windows Virtual Desktop TechCommunity.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

U ook feedback geven voor Windows Virtual Desktop op de [Feedbackhub van Windows Virtual Desktop](https://aka.ms/MRSFeedbackHub)of feedback geven voor de MSIX-app en -verpakkingstool op de Feedbackhub van de [MSIX-app en](https://aka.ms/msixappattachfeedback) de [feedbackhub voor msix-verpakkingen.](https://aka.ms/msixtoolfeedback)
