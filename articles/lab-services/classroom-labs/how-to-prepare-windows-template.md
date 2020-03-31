---
title: Handleiding voor het instellen van een Windows-sjabloonmachine | Microsoft Documenten
description: Algemene stappen om een Windows-sjabloonmachine voor te bereiden in Lab Services.  Deze stappen omvatten het instellen van Windows Update-planning, het installeren van OneDrive en het installeren van Office.
services: lab-services
documentationcenter: na
author: EMaher
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 11/21/2019
ms.author: enewman
ms.openlocfilehash: c52a1212d160adce3a0a0638164833bc2907a856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76515000"
---
# <a name="guide-to-setting-up-a-windows-template-machine-in-azure-lab-services"></a>Handleiding voor het instellen van een Windows-sjabloonmachine in Azure Lab Services

Als u een Windows 10-sjabloonmachine instelt voor Azure Lab Services, vindt u hier enkele aanbevolen procedures en tips waarmee u rekening moet houden. De onderstaande configuratiestappen zijn allemaal optioneel.  Deze voorbereidende stappen kunnen uw leerlingen echter productiever maken, onderbrekingen van de lestijd minimaliseren en ervoor zorgen dat ze de nieuwste technologieën gebruiken.

>[!IMPORTANT]
>Dit artikel bevat PowerShell-fragmenten om het wijzigingsproces van de machinesjabloon te stroomlijnen.  Voor alle PowerShell-scripts die worden weergegeven, wilt u ze uitvoeren in Windows PowerShell met beheerdersbevoegdheden. In Windows 10 u dat snel doen door met de rechtermuisknop op het menu Start te klikken en de optie Windows PowerShell (Beheerder) te kiezen.In Windows 10, a quick way to doing that is to click the Right-click the Start Menu and choose the Windows PowerShell (Admin).

## <a name="install-and-configure-onedrive"></a>OneDrive installeren en configureren

Om te voorkomen dat studentengegevens verloren gaan als een virtuele machine wordt gereset, raden we studenten aan hun gegevens terug te zetten naar de cloud.  Microsoft OneDrive kan studenten helpen hun gegevens te beschermen.  

### <a name="install-onedrive"></a>OneDrive installeren

Zie de downloadpagina's van [OneDrive](https://onedrive.live.com/about/download/) of [OneDrive voor Bedrijven](https://onedrive.live.com/about/business/) als u OneDrive handmatig wilt downloaden en installeren.

U ook het volgende PowerShell-script gebruiken.  Het zal automatisch downloaden en installeren van de nieuwste versie van OneDrive.  Zodra de OneDrive-client is geïnstalleerd, voert u het installatieprogramma uit.  In ons voorbeeld gebruiken `/allUsers` we de schakelaar om OneDrive te installeren voor alle gebruikers op de machine. We gebruiken `/silent` de schakelaar ook om OneDrive in stilte te installeren.

```powershell
Write-Host "Downloading OneDrive Client..."
$DownloadPath = "$env:USERPROFILE/Downloads/OneDriveSetup.exe"
if((Test-Path $DownloadPath) -eq $False )
{
    Write-Host "Downloading OneDrive..."
    $web = new-object System.Net.WebClient
    $web.DownloadFile("https://go.microsoft.com/fwlink/p/?LinkId=248256",$DownloadPath)
} else {
    Write-Host "OneDrive installer already exists at " $DownloadPath
}

Write-Host "Installing OneDrive..."
& $env:USERPROFILE/Downloads/OneDriveSetup.exe /allUsers /silent
```

### <a name="onedrive-customizations"></a>OneDrive-aanpassingen

Er zijn veel [aanpassingen die kunnen worden gedaan om OneDrive](https://docs.microsoft.com/onedrive/use-group-policy). Laten we een aantal van de meest voorkomende aanpassingen.

#### <a name="silently-move-windows-known-folders-to-onedrive"></a>Windows-bekende mappen stilletjes verplaatsen naar OneDrive

Mappen zoals documenten, downloads en afbeeldingen worden vaak gebruikt om studentenbestanden op te slaan. Om ervoor te zorgen dat er een back-up van deze mappen wordt gemaakt in OneDrive, raden we u aan deze mappen naar OneDrive te verplaatsen.

Als u zich op een machine bevindt die Active Directory niet gebruikt, kunnen gebruikers deze mappen handmatig verplaatsen naar OneDrive zodra ze zijn geverifieerd naar OneDrive.

1. Verkenner openen
2. Klik met de rechtermuisknop op de map Documenten, Downloads of Afbeeldingen.
3. Ga naar Eigenschappen > locatie.  Verplaats de map naar een nieuwe map in de OneDrive-map.

Als uw virtuele machine is verbonden met Active Directory, u instellen dat de sjabloonmachine uw leerlingen automatisch vraagt om de bekende mappen naar OneDrive te verplaatsen.  

U moet eerst uw Office-tenant-id ophalen.  Zie [uw Office 365-tenant-id zoeken](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)voor meer instructies.  U de Office 365 Tenant ID ook krijgen met behulp van de volgende PowerShell.

```powershell
Install-Module MSOnline -Confirm
Connect-MsolService
$officeTenantID = Get-MSOLCompanyInformation |
    Select-Object -expand objectID |
    Select-Object -expand Guid
```

Zodra u uw Office 365-tenant-id hebt, stelt u OneDrive in om bekende mappen naar OneDrive te verplaatsen met de volgende PowerShell.

```powershell
if ($officeTenantID -eq $null)
{
        Write-Error "Variable `$officeTenantId must be set to your Office Tenant Id before continuing."
}
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "KFMSilentOptIn" -Value $officeTenantID -PropertyType STRING
```

### <a name="use-onedrive-files-on-demand"></a>On-demand OneDrive-bestanden gebruiken

Studenten kunnen veel bestanden in hun OneDrive-accounts hebben. Om ruimte op de machine te besparen en de downloadtijd te verkorten, raden we u aan om alle bestanden die zijn opgeslagen in het OneDrive-account van de student on-demand te maken.  On-demandbestanden worden pas gedownload als een gebruiker toegang heeft tot het bestand.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "FilesOnDemandEnabled" -Value "00000001" -PropertyType DWORD
```

### <a name="silently-sign-in-users-to-onedrive"></a>Gebruikers in stilte aanmelden bij OneDrive

OneDrive kan worden ingesteld om automatisch in te loggen met de Windows-referenties van de aangemelde gebruiker.  Automatisch aanmelden is handig voor klassen waarbij de student zich aanmeldt met hun Office 365-schoolgegevens.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "SilentAccountConfig" -Value "00000001" -PropertyType DWORD
```

### <a name="disable-the-tutorial-that-appears-at-the-end-of-onedrive-setup"></a>De zelfstudie uitschakelen die wordt weergegeven aan het einde van de OneDrive-installatie

Met deze instelling u voorkomen dat de zelfstudie wordt gestart in een webbrowser aan het einde van OneDrive Setup.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive" -Force
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
    -Name "DisableTutorial" -Value "00000001" -PropertyType DWORD -Force
```

### <a name="set-the-maximum-size-of-a-file-that-to-be-download-automatically"></a>De maximale grootte instellen van een bestand dat automatisch moet worden gedownload

Deze instelling wordt gebruikt in combinatie met het stilletjes aanmelden van gebruikers bij de OneDrive-synchronisatieclient met hun Windows-referenties op apparaten waarop OneDrive Files On-Demand niet is ingeschakeld. Elke gebruiker die een OneDrive heeft die groter is dan de opgegeven drempelwaarde (in MB) wordt gevraagd de mappen te kiezen die ze willen synchroniseren voordat de OneDrive-synchronisatieclient (OneDrive.exe) de bestanden downloadt.  In ons voorbeeld is "1111-2222-3333-4444" de Office 365 tenant ID en 0005000 stelt een drempel van 5 GB in.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive"
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\OneDrive\DiskSpaceCheckThresholdMB"
    -Name "1111-2222-3333-4444" -Value "0005000" -PropertyType DWORD
```

## <a name="install-and-configure-microsoft-office-365"></a>Microsoft Office 365 installeren en configureren

### <a name="install-microsoft-office-365"></a>Microsoft Office 365 installeren

Als uw sjabloonmachine Office nodig heeft, raden we u aan Office te installeren via de [ODT (Office Deployment Tool).](https://www.microsoft.com/download/details.aspx?id=49117 ) U moet een herbruikbaar configuratiebestand maken met de [Office 365 Client Configuration Service](https://config.office.com/) om te kiezen welke architectuur, welke functies u van Office nodig hebt en hoe vaak deze worden bijgewerkt.

1. Ga naar [Office 365 Client Configuration Service](https://config.office.com/) en download uw eigen configuratiebestand.
2. Office [Deployment Tool downloaden](https://www.microsoft.com/download/details.aspx?id=49117).  Gedownload bestand zal `setup.exe`zijn .
3. Voer `setup.exe /download configuration.xml` uit om Office-onderdelen te downloaden.
4. Voer `setup.exe /configure configuration.xml` uit om Office-onderdelen te installeren.

### <a name="change-the-microsoft-office-365-update-channel"></a>Het updatekanaal van Microsoft Office 365 wijzigen

Met het Office Configuration Tool u instellen hoe vaak Office updates ontvangt. Als u echter moet wijzigen hoe vaak Office updates ontvangt na de installatie, u de URL van het updatekanaal wijzigen. URL-adressen van het updatekanaal zijn te vinden op [Het updatekanaal van Office 365 ProPlus wijzigen voor apparaten in uw organisatie.](https://docs.microsoft.com/deployoffice/change-update-channels) In het onderstaande voorbeeld ziet u hoe u Office 365 instelt op het kanaal voor maandelijkse updates.

```powershell
# Update to the Office 365 Monthly Channel
Set-ItemProperty
    -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration\CDNBaseUrl"
    -Name "CDNBaseUrl"
    -Value "http://officecdn.microsoft.com/pr/492350f6-3a01-4f97-b9c0-c7c6ddf67d60"
```

## <a name="install-and-configure-updates"></a>Updates installeren en configureren

### <a name="install-the-latest-windows-updates"></a>De nieuwste Windows-updates installeren

We raden u aan de nieuwste Microsoft-updates op de sjabloonmachine te installeren voor beveiligingsdoeleinden voordat u de sjabloon-VM publiceert.  Het voorkomt ook dat studenten worden verstoord in hun werk wanneer updates op onverwachte momenten worden uitgevoerd.

1. **Startinstellingen** in het menu Start
2. Klik op **&-beveiliging** bijwerken
3. Klik **op Controleren op updates**
4. Updates worden gedownload en geïnstalleerd.

U PowerShell ook gebruiken om de sjabloonmachine bij te werken.

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Confirm
Install-Module PSWindowsUpdate -Confirm
Install-WindowsUpdate -MicrosoftUpdate
Set-ExecutionPolicy default -Force
```

>[!NOTE]
>Voor sommige updates moet de machine mogelijk opnieuw worden opgestart.  U wordt gevraagd of een herstart vereist is.

### <a name="install-the-latest-updates-for-microsoft-store-apps"></a>De nieuwste updates voor Microsoft Store-apps installeren

We raden u aan dat alle Microsoft Store-apps worden bijgewerkt naar de nieuwste versies.  Hier vindt u instructies voor het handmatig bijwerken van toepassingen vanuit de Microsoft Store.  

1. **Microsoft Store-toepassing** starten.
2. Klik op de ellips (...) naast uw gebruikersfoto in de bovenhoek van de toepassing.
3. Selecteer **Downloaden** en updates in het vervolgkeuzemenu.
4. Klik **op Update downloaden.**

U Powershell ook gebruiken om Microsoft Store-toepassingen bij te werken die al zijn geïnstalleerd.

```powershell
(Get-WmiObject -Namespace "root\cimv2\mdm\dmmap" -Class "MDM_EnterpriseModernAppManagement_AppManagement01").UpdateScanMethod()
```

### <a name="stop-automatic-windows-updates"></a>Automatische Windows-updates stoppen

Nadat u Windows hebt bijgewerkt naar de nieuwste versie, u overwegen Windows-updates te stoppen.  Automatische updates kunnen mogelijk de geplande lestijd verstoren.  Als uw cursus langer wordt uitgevoerd, u studenten vragen om handmatig te controleren op updates of automatische updates in te stellen voor een tijd buiten de geplande lesuren.  Zie de [extra Windows Update-instellingen beheren](https://docs.microsoft.com/windows/deployment/update/waas-wu-settings)voor meer informatie over aanpassingsopties voor Windows Update.

Automatische Windows-updates kunnen worden gestopt met het volgende PowerShell-script.

```powershell
New-Item -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
New-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\AU"
    -Name "NoAutoUpdate" -Value "1" -PropertyType DWORD
```

## <a name="install-foreign-language-packs"></a>Pakketten voor vreemde talen installeren

Als u extra talen op de virtuele machine wilt installeren, u deze toevoegen via de Microsoft Store.

1. Microsoft Store starten
2. Zoek naar "taalpakket"
3. Taal kiezen om te installeren

Als u al bent aangemeld bij de sjabloon-VM, gebruikt u [de snelkoppeling 'Taalpakket installeren'](ms-settings:regionlanguage?activationSource=SMC-IA-4027670) om rechtstreeks naar de juiste instellingenpagina te gaan.

## <a name="remove-unneeded-built-in-apps"></a>Onnodige ingebouwde apps verwijderen

Windows 10 wordt geleverd met veel ingebouwde toepassingen die mogelijk niet nodig zijn voor uw specifieke klasse. Als u de machineafbeelding voor studenten wilt vereenvoudigen, u bepaalde toepassingen van uw sjabloonmachine verwijderen.  Als u een lijst met geïnstalleerde `Get-AppxPackage` toepassingen wilt bekijken, gebruikt u de PowerShell-cmdlet.  In het onderstaande voorbeeld worden alle geïnstalleerde toepassingen weergegeven die kunnen worden verwijderd.

```powershell
Get-AppxPackage | Where {$_.NonRemovable -eq $false} | select Name
```

Als u een toepassing wilt verwijderen, gebruikt u de cmdlet Remove-Appx.  In het onderstaande voorbeeld ziet u hoe u alles met XBox-gerelateerd verwijderen.

```powershell
Get-AppxPackage -Name *xbox* | foreach { if (-not $_.NonRemovable) { Remove-AppxPackage $_} }
```

## <a name="install-common-teaching-related-applications"></a>Algemene onderwijsgerelateerde toepassingen installeren

Installeer andere apps die vaak worden gebruikt voor lesgeven via de Windows Store-app. Suggesties omvatten toepassingen zoals [Microsoft Whiteboard-app,](https://www.microsoft.com/store/productId/9MSPC6MP8FM4) [Microsoft Teams](https://www.microsoft.com/store/productId/9MSPC6MP8FM4)en Minecraft [Education Edition](https://education.minecraft.net/). Deze toepassingen moeten handmatig worden geïnstalleerd via de Windows Store of via hun respectievelijke websites op de sjabloon-VM.

## <a name="conclusion"></a>Conclusie

In dit artikel ziet u optionele stappen om uw Windows-sjabloon-VM voor te bereiden op een effectieve klasse.  Stappen zijn het installeren van OneDrive en het installeren van Office 365, het installeren van de updates voor Windows en het installeren van updates voor Microsoft Store-apps.  We hebben ook besproken hoe u updates instellen voor een schema dat het beste werkt voor uw klas.  
