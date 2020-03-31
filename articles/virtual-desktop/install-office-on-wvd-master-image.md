---
title: Office installeren op een master VHD-afbeelding - Azure
description: Office installeren en aanpassen op een Windows Virtual Desktop-hoofdafbeelding in Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b93f26a6799a50868feb1f3350a3dc4a73a0b2e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127851"
---
# <a name="install-office-on-a-master-vhd-image"></a>Office installeren op een master-VHD-installatiekopie

In dit artikel vindt u hoe u Office 365 ProPlus, OneDrive en andere veelvoorkomende toepassingen installeren op een vhd-afbeelding (Master Virtual Hard Disk) voor uploaden naar Azure. Als uw gebruikers toegang nodig hebben tot bepaalde LOB-toepassingen (Line of Business), raden we u aan deze te installeren nadat u de instructies in dit artikel hebt voltooid.

In dit artikel wordt ervan uitgegaan dat u al een virtuele machine (VM) hebt gemaakt. Zo niet, zie [Een hoofdVHD-afbeelding voorbereiden en aanpassen](set-up-customize-master-image.md#create-a-vm)

In dit artikel wordt ook ervan uitgegaan dat u verhoogde toegang hebt op de VM, of deze nu is ingericht in Azure of Hyper-V Manager. Zie Access [verheffen om alle Azure-abonnements- en beheergroepen te beheren.](../role-based-access-control/elevate-access-global-admin.md)

>[!NOTE]
>Deze instructies zijn bedoeld voor een Windows Virtual Desktop-specifieke configuratie die kan worden gebruikt met de bestaande processen van uw organisatie.

## <a name="install-office-in-shared-computer-activation-mode"></a>Office installeren in de activeringsmodus voor gedeelde computers

Met de activering van gedeelde computers u Office 365 ProPlus implementeren op een computer in uw organisatie die door meerdere gebruikers wordt geopend. Zie [Overzicht van de activering van gedeelde computers voor Office 365 ProPlus voor](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/)meer informatie over de activering van gedeelde computers.

Gebruik het [Office Deployment Tool](https://www.microsoft.com/download/details.aspx?id=49117) om Office te installeren. Windows 10 Enterprise-multisessie ondersteunt alleen de volgende versies van Office:
- Office 365 ProPlus
- Office 365 Business dat wordt geleverd met een Microsoft 365 Business-abonnement

Het Office Deployment Tool vereist een configuratie-XML-bestand. Zie de [configuratieopties voor het Office Deployment Tool](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/)om het volgende voorbeeld aan te passen.

Deze voorbeeldconfiguratie XML die we hebben geleverd, doet de volgende dingen:

- Installeer Office vanaf het maandelijkse kanaal en lever updates van het maandelijkse kanaal wanneer deze worden uitgevoerd.
- Gebruik de x64-architectuur.
- Automatische updates uitschakelen.
- Verwijder bestaande installaties van Office en migreer de instellingen ervan.
- Gedeelde computeractivering inschakelen.

>[!NOTE]
>De zoekfunctie voor stencils in Visio werkt mogelijk niet zoals verwacht in Windows Virtual Desktop.

Dit is wat deze voorbeeldconfiguratie XML niet zal doen:

- Skype voor Bedrijven installeren
- Installeer OneDrive in de modus per gebruiker. Zie [OneDrive installeren in de modus Per machine](#install-onedrive-in-per-machine-mode)voor meer informatie .

>[!NOTE]
>Gedeelde computeractivering kan worden ingesteld via Groepsbeleidsobjecten (GPO's) of registerinstellingen. De GPO bevindt zich in **de\\beheerinstellingen\\\\voor computerconfiguratiebeleid Microsoft Office 2016 (Machine)\\**

Het Office Deployment Tool bevat setup.exe. Als u Office wilt installeren, voert u de volgende opdracht uit in een opdrachtregel:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Voorbeeldconfiguratie.xml

In het volgende XML-voorbeeld wordt de maandelijkse release geïnstalleerd.

```xml
<Configuration>
  <Add OfficeClientEdition="64" Channel="Monthly">
    <Product ID="O365ProPlusRetail">
      <Language ID="en-US" />
      <Language ID="MatchOS" />
      <ExcludeApp ID="Groove" />
      <ExcludeApp ID="Lync" />
      <ExcludeApp ID="OneDrive" />
      <ExcludeApp ID="Teams" />
    </Product>
  </Add>
  <RemoveMSI/>
  <Updates Enabled="FALSE"/>
  <Display Level="None" AcceptEULA="TRUE" />
  <Logging Level=" Standard" Path="%temp%\WVDOfficeInstall" />
  <Property Name="FORCEAPPSHUTDOWN" Value="TRUE"/>
  <Property Name="SharedComputerLicensing" Value="1"/>
</Configuration>
```

>[!NOTE]
>Het Office-team raadt aan om 64-bits installatie te gebruiken voor de parameter **OfficeClientEdition.**

Na het installeren van Office u het standaardOffice-gedrag bijwerken. Voer de volgende opdrachten afzonderlijk of in een batchbestand uit om het gedrag bij te werken.

```batch
rem Mount the default user registry hive
reg load HKU\TempDefault C:\Users\Default\NTUSER.DAT
rem Must be executed with default registry hive mounted.
reg add HKU\TempDefault\SOFTWARE\Policies\Microsoft\office\16.0\common /v InsiderSlabBehavior /t REG_DWORD /d 2 /f
rem Set Outlook's Cached Exchange Mode behavior
rem Must be executed with default registry hive mounted.
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v enable /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v syncwindowsetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSetting /t REG_DWORD /d 1 /f
reg add "HKU\TempDefault\software\policies\microsoft\office\16.0\outlook\cached mode" /v CalendarSyncWindowSettingMonths  /t REG_DWORD /d 1 /f
rem Unmount the default user registry hive
reg unload HKU\TempDefault

rem Set the Office Update UI behavior.
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideupdatenotifications /t REG_DWORD /d 1 /f
reg add HKLM\SOFTWARE\Policies\Microsoft\office\16.0\common\officeupdate /v hideenabledisableupdates /t REG_DWORD /d 1 /f
```

## <a name="install-onedrive-in-per-machine-mode"></a>OneDrive installeren in de modus per machine

OneDrive wordt normaal gesproken per gebruiker geïnstalleerd. In deze omgeving moet het per machine worden geïnstalleerd.

U OneDrive als u onedrive in de modus per machine installeren:

1. Maak eerst een locatie om het OneDrive-installatieprogramma te fasen. Een lokale schijfmap\\\\of [unc] (file://unc) locatie is prima.

2. Download OneDriveSetup.exe naar uw geënsceneerde locatie met deze link:<https://aka.ms/OneDriveWVD-Installer>

3. Als u Office met OneDrive hebt geïnstalleerd door ** \<ExcludeApp\>ID="OneDrive" /** weg te laten, verwijdert u bestaande Installaties van OneDrive per gebruiker uit een opdracht met verhoogde bevoegdheid door de volgende opdracht uit te voeren:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Voer deze opdracht uit vanuit een opdrachtprompt met verhoogde bevoegdheid om de registerwaarde **AllUsersInstalleren** in te stellen:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Voer deze opdracht uit om OneDrive in de modus per machine te installeren:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Voer deze opdracht uit om OneDrive te configureren om te beginnen bij aanmelden voor alle gebruikers:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Schakel **in het gebruikersaccount in stilte configureren** door de volgende opdracht uit te voeren.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. De bekende mappen van Windows omleiden en verplaatsen naar OneDrive door de volgende opdracht uit te voeren.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams en Skype

Windows Virtual Desktop biedt geen ondersteuning voor Skype voor Bedrijven en Teams.

## <a name="next-steps"></a>Volgende stappen

Nu u Office aan de afbeelding hebt toegevoegd, u uw hoofdVHD-afbeelding blijven aanpassen. Zie [Een master VHD-afbeelding voorbereiden en aanpassen](set-up-customize-master-image.md).
