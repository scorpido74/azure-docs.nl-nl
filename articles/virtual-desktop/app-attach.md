---
title: Windows Virtual Desktop MSIX-app koppelen-Azure
description: MSIX-app-koppeling instellen voor het virtuele bureau blad van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: helohr
ms.openlocfilehash: c5d6c671890f5e036d3f4cce6e880230c01048ed
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483828"
---
# <a name="set-up-msix-app-attach"></a>MSIX-app-koppeling instellen

> [!IMPORTANT]
> MSIX app attach is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit onderwerp vindt u instructies voor het instellen van een MSIX-app-koppeling in een virtueel-bureaublad omgeving van Windows.

## <a name="requirements"></a>Vereisten

Voordat u aan de slag gaat, hebt u het volgende nodig om de MSIX-app-koppeling te configureren:

- Toegang tot de Windows Insider-portal voor het verkrijgen van de versie van Windows 10 met ondersteuning voor de MSIX app attach-Api's.
- Een werkende implementatie van virtueel bureau blad in Windows. Zie [een Tenant maken in virtueel bureau blad van Windows](tenant-setup-azure-active-directory.md)voor meer informatie.
- Het MSIX-verpakkings programma
- Een netwerk share in uw Windows-implementatie voor virtueel bureau blad waar het MSIX-pakket wordt opgeslagen

## <a name="get-the-os-image"></a>De installatie kopie van het besturings systeem ophalen

Eerst moet u de installatie kopie van het besturings systeem ophalen die u voor de MSIX-app gaat gebruiken. De installatie kopie van het besturings systeem ophalen:

1. Open de [Windows Insider-Portal](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) en meld u aan.

     >[!NOTE]
     >U moet lid zijn van het Windows Insider-programma om toegang te krijgen tot de Windows Insider-Portal. Raadpleeg onze [Windows Insider-documentatie](https://docs.microsoft.com/windows-insider/at-home/)voor meer informatie over het Windows Insider-programma.

2. Schuif omlaag naar de sectie **Select Edition** en selecteer **Windows 10 Insider preview Enter prise (Fast) – Build xxxxx**.

3. Selecteer **bevestigen**, selecteer de taal die u wilt gebruiken en selecteer vervolgens opnieuw **bevestigen** .
    
     >[!NOTE]
     >Op het moment is Engels de enige taal die met de functie is getest. U kunt andere talen selecteren, maar deze kunnen niet worden weer gegeven zoals bedoeld.
    
4. Wanneer de download koppeling is gegenereerd, selecteert u de **64-bits downloaden** en slaat u deze op de lokale harde schijf op.

## <a name="prepare-the-vhd-image-for-azure"></a>De VHD-installatie kopie voorbereiden voor Azure 

Voordat u aan de slag gaat, moet u een installatie kopie van een hoofd-VHD maken. Als u nog geen installatie kopie van de hoofd-VHD hebt gemaakt, gaat u naar voor [bereiding en past u een installatie kopie van een virtuele harde schijf](set-up-customize-master-image.md) aan en volgt u de instructies. 

Nadat u de installatie kopie van de hoofd-VHD hebt gemaakt, moet u automatische updates voor MSIX app attach-toepassingen uitschakelen. Als u automatische updates wilt uitschakelen, moet u de volgende opdrachten uitvoeren in een opdracht prompt met verhoogde bevoegdheid:

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

Bereid vervolgens de VM-VHD voor Azure voor en upload de resulterende VHD-schijf naar Azure. Zie [een VHD-master installatie kopie voorbereiden en aanpassen](set-up-customize-master-image.md)voor meer informatie.

Nadat u de VHD naar Azure hebt geüpload, maakt u een hostgroep die is gebaseerd op deze nieuwe installatie kopie door de instructies in de [groep een host maken te volgen met behulp van de zelf studie voor Azure Marketplace](create-host-pools-azure-marketplace.md) .

## <a name="prepare-the-application-for-msix-app-attach"></a>De toepassing voorbereiden voor de MSIX-app attach 

Als u al een MSIX-pakket hebt, gaat u verder met het configureren van een [virtuele bureau blad-infra structuur voor Windows](#configure-windows-virtual-desktop-infrastructure). Als u oudere toepassingen wilt testen, volgt u de instructies in een [MSIX-pakket maken op basis van een installatie programma van een desktop computer op een virtuele machine](https://docs.microsoft.com/windows/msix/packaging-tool/create-app-package-msi-vm) om de verouderde toepassing te converteren naar een MSIX-pakket.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Een VHD-of VHDX-pakket voor MSIX genereren

Pakketten bevinden zich in VHD-of VHDX-indeling om de prestaties te optimaliseren. MSIX vereist dat de VHD-of VHDX-pakketten goed werken.

Een VHD-of VHDX-pakket genereren voor MSIX:

1. [Down load het msixmgr-hulp programma](https://aka.ms/msixmgr) en sla de zip-map op in een map in een sessie-host-VM.

2. Pak de map msixmgr. zip.

3. Plaats het bron MSIX-pakket in dezelfde map waarin u het msixmgr-hulp programma hebt uitgepakt.

4. Voer de volgende cmdlet uit in Power shell om een VHD te maken:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Zorg ervoor dat de grootte van de VHD groot genoeg is voor de uitgevouwen MSIX. *

5. Voer de volgende cmdlet uit om de zojuist gemaakte VHD te koppelen:

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

8. Voer deze cmdlet uit om de partitie te Format teren:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Maak een bovenliggende map op de gekoppelde VHD. Deze stap is verplicht omdat voor het koppelen van de MSIX-app een bovenliggende map is vereist. U kunt de naam van de bovenliggende map, ongeacht u dat wilt.

### <a name="expand-msix"></a>MSIX uitvouwen

Daarna moet u de MSIX-installatie kopie uitbreiden door deze uit te pakken. De MSIX-installatie kopie uitpakken:

1. Open een opdracht prompt als beheerder en navigeer naar de map waar u het hulp programma msixmgr hebt gedownload en uitgepakt.

2. Voer de volgende cmdlet uit om de MSIX uit te pakken naar de VHD die u in de vorige sectie hebt gemaakt en gekoppeld.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Het volgende bericht moet worden weer gegeven wanneer het uitpakken is voltooid:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Als u pakketten gebruikt van de Microsoft Store voor bedrijven (of onderwijs) in uw netwerk of op apparaten die niet zijn verbonden met internet, moet u de pakket licenties verkrijgen uit de Store en deze installeren om de app uit te voeren. Zie [pakketten offline gebruiken](#use-packages-offline).

3. Ga naar de gekoppelde VHD en open de map app en bevestig dat de inhoud van het pakket aanwezig is.

4. Ontkoppel de VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>De Windows-infra structuur voor virtueel bureau blad configureren

Standaard kan één uitgevouwen MSIX-pakket (de VHD die u hebt gemaakt in de vorige sectie) worden gedeeld tussen meerdere host-Vm's voor hosts wanneer de virtuele harde schijven zijn gekoppeld in de modus alleen-lezen.

Voordat u begint, moet u ervoor zorgen dat uw netwerk share aan de volgende vereisten voldoet:

- De share is met SMB compatibel.
- De virtuele machines die deel uitmaken van de Session Host-groep hebben NTFS-machtigingen voor de share.

### <a name="set-up-an-msix-app-attach-share"></a>Een koppelings share voor een MSIX-app instellen 

Maak in uw Windows Virtual Desktop-omgeving een netwerk share en verplaats het pakket daar.

>[!NOTE]
> De best practice voor het maken van MSIX-netwerk shares is het instellen van de netwerk share met NTFS-machtigingen voor alleen-lezen.

## <a name="install-certificates"></a>Certificaten installeren

Als uw app gebruikmaakt van een certificaat dat niet openbaar of zelfondertekend is, kunt u dit als volgt installeren:

1. Klik met de rechter muisknop op het pakket en selecteer **Eigenschappen**.
2. In het venster dat wordt weer gegeven, selecteert u het tabblad **digitale hand tekeningen** . Er mag slechts één item in de lijst staan op het tabblad, zoals wordt weer gegeven in de volgende afbeelding. Selecteer het item dat u wilt markeren en selecteer vervolgens * * de
3. Wanneer het venster Details van digitale signalen wordt weer gegeven, selecteert u het tabblad **Algemeen** en selecteert u **certificaat installeren**.
4. Wanneer het installatie programma wordt geopend, selecteert u **lokale computer** als uw opslag locatie en selecteert u vervolgens **volgende**.
5. Als het installatie programma u vraagt of u wilt toestaan dat de app wijzigingen in uw apparaat aanbrengt, selecteert u **Ja**.
6. Selecteer **alle certificaten in het onderstaande archief opslaan**en selecteer vervolgens **Bladeren**.
7. Wanneer het venster certificaat archief selecteren wordt weer gegeven, selecteert u **vertrouwde personen**en selecteert u **OK**.
8. Selecteer **Voltooien**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Power shell-scripts voorbereiden voor MSIX-app attach

Het koppelen van de MSIX-app heeft vier afzonderlijke fasen die in de volgende volg orde moeten worden uitgevoerd:

1. Fase
2. Registreren
3. Registratie
4. Destage

Elke fase maakt een Power shell-script. Voorbeeld scripts voor elke fase zijn [hier](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)beschikbaar.

### <a name="stage-the-powershell-script"></a>Het Power shell-script faseren

Voordat u de Power shell-scripts bijwerkt, moet u ervoor zorgen dat u de volume-GUID van het volume in de VHD hebt. De volume-GUID ophalen:

1.  Open de netwerk share waar de VHD zich bevindt in de virtuele machine waarop u het script wilt uitvoeren.

2.  Klik met de rechter muisknop op de VHD en selecteer **koppelen**. Hiermee wordt de VHD gekoppeld aan een stationsletter.

3.  Nadat u de VHD hebt gekoppeld, wordt het venster **bestanden Verkenner** geopend. De bovenliggende map vastleggen en de variabele **\$parentFolder** bijwerken

    >[!NOTE]
    >Als u geen bovenliggende map ziet, betekent dit dat de MSIX niet goed is uitgevouwen. Voer de vorige sectie opnieuw uit en probeer het opnieuw.

4.  Open de bovenliggende map. Als het pakket correct is uitgevouwen, ziet u een map met dezelfde naam als het Pack. Werk de variabele van de **\$pakket** naam bij zodat deze overeenkomt met die van deze map.

    Bijvoorbeeld `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Open een opdracht prompt en voer **mountvol**in. Met deze opdracht wordt een lijst met volumes en de bijbehorende GUID'S weer gegeven. Kopieer de GUID van het volume waar de stationsletter overeenkomt met het station waarnaar u uw VHD hebt gekoppeld in stap 2.

    Bijvoorbeeld, in dit voor beeld van de uitvoer van de opdracht mountvol als u uw VHD hebt gekoppeld aan station C, wilt u de bovenstaande waarde kopiëren `C:\`:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Werk de **\$volumeGuid** -variabele met de volume-GUID die u zojuist hebt gekopieerd.

7. Open een Power shell-prompt voor beheerders en werk het volgende Power shell-script bij met de variabelen die van toepassing zijn op uw omgeving.

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

    Mount-Diskimage -ImagePath \$vhdSrc -NoDriveLetter -Access ReadOnly

    Write-Host ("Mounting of " + \$vhdSrc + " was completed!") -BackgroundColor Green

    }

    catch

    {

    Write-Host ("Mounting of " + \$vhdSrc + " has failed!") -BackgroundColor Red

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
    $_.ToString() -eq 'System.Threading.Tasks.Task\`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress\`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the
    pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Power shell-script registreren

Als u het REGI ster wilt uitvoeren, voert u de volgende Power shell-cmdlets uit met de waarden voor de tijdelijke aanduiding die zijn vervangen door instellingen die van toepassing zijn op uw omgeving.

```powershell
#MSIX app attach registration sample

#region variables

$packageName = "<package name>"

$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"

#endregion

#region register

Add-AppxPackage -Path \$path -DisableDevelopmentMode -Register

#endregion
```

### <a name="deregister-powershell-script"></a>Het Power shell-script deregistreren

Voor dit script vervangt u de tijdelijke aanduiding voor **\$pakketmap** door de naam van het pakket dat u wilt testen.

```powershell
#MSIX app attach deregistration sample

#region variables

$packageName = "<package name>"

#endregion

#region deregister

Remove-AppxPackage -PreserveRoamableApplicationData $packageName

#endregion
```

### <a name="destage-powershell-script"></a>Power shell-script destage

Voor dit script vervangt u de tijdelijke aanduiding voor **\$pakketmap** door de naam van het pakket dat u wilt testen.

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

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Simulatie scripts instellen voor de MSIX-agent voor het koppelen van apps

Nadat u de scripts hebt gemaakt, kunnen gebruikers deze hand matig uitvoeren of instellen dat ze automatisch worden uitgevoerd als opstart-, aanmeldings-, afmeldings-en afsluit scripts. Zie [opstart-, afsluit-, aanmeldings-en afmeldings scripts gebruiken in Groepsbeleid](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11))voor meer informatie over deze soorten scripts.

Met elk van deze automatische scripts wordt één fase van de app gekoppelde scripts uitgevoerd:

- Het opstart script voert het fase script uit.
- Het aanmeldings script voert het registratie script uit.
- Het afmeldings script voert het registratie script uit.
- Het afsluit script voert het destage-script uit.

## <a name="use-packages-offline"></a>Pakketten offline gebruiken

Als u pakketten gebruikt van de [Microsoft Store voor bedrijven](https://businessstore.microsoft.com/) of de [Microsoft Store voor onderwijs](https://educationstore.microsoft.com/) binnen uw netwerk of op apparaten die niet zijn verbonden met internet, moet u de pakket licenties van de Microsoft Store ophalen en op uw apparaat installeren om de app te kunnen uitvoeren. Als uw apparaat online is en verbinding kan maken met de Microsoft Store voor bedrijven, moeten de vereiste licenties automatisch worden gedownload, maar als u offline bent, moet u de licenties hand matig instellen. 

Als u de licentie bestanden wilt installeren, moet u een Power shell-script gebruiken dat de MDM_EnterpriseModernAppManagement_StoreLicenses02_01-klasse aanroept in de WMI Bridge-provider.  

U kunt als volgt de licenties instellen voor offline gebruik: 

1. Down load het app-pakket, de licenties en de vereiste Frameworks van de Microsoft Store voor bedrijven. U hebt zowel de versleutelde als niet-versleutelde licentie bestanden nodig. Gedetailleerde Download instructies vindt u [hier](https://docs.microsoft.com/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Werk de volgende variabelen bij in het script voor stap 3:
      1. `$contentID` is de ContentID-waarde van het niet-versleutelde licentie bestand (. XML). U kunt het licentie bestand openen in een tekst editor naar keuze.
      2. `$licenseBlob` is de volledige teken reeks voor de licentie-Blob in het gecodeerde licentie bestand (. bin). U kunt het versleutelde licentie bestand openen in een tekst editor naar keuze. 
3. Voer het volgende script uit vanuit een Power shell-prompt voor beheerders. Er is een goede plaats om de licentie-installatie uit te voeren aan het einde van het [faserings script](#stage-the-powershell-script) dat ook moet worden uitgevoerd vanaf een beheerders prompt.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{‘ContentID’_in_unencoded_license_file}"

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

Deze functie wordt momenteel niet ondersteund, maar u kunt vragen stellen aan de community op het [virtuele bureau blad van Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

U kunt ook feedback geven voor het virtuele bureau blad van Windows op de [Windows Virtual Desktop-feedback-hub](https://aka.ms/MRSFeedbackHub), of feedback geven voor de MSIX-app en het hulp programma voor het inpakken van de [MSIX-app](https://aka.ms/msixappattachfeedback) en de [MSIX](https://aka.ms/msixtoolfeedback).
