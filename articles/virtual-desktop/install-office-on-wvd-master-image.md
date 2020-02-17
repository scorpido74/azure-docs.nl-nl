---
title: Office installeren op een hoofd-VHD-installatie kopie-Azure
description: Office installeren en aanpassen op basis van een installatie kopie van een virtueel bureau blad van Windows in Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: helohr
ms.openlocfilehash: 1637cb7621d6071bbce2af66f3a106770139ad4e
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368817"
---
# <a name="install-office-on-a-master-vhd-image"></a>Office installeren op een master-VHD-installatiekopie

In dit artikel leest u hoe u Office 365 ProPlus, OneDrive en andere algemene toepassingen op een master-image van een virtuele harde schijf (VHD) installeert voor het uploaden naar Azure. Als uw gebruikers toegang moeten hebben tot bepaalde LOB-toepassingen (line-of-Business), raden wij u aan deze te installeren nadat u de instructies in dit artikel hebt voltooid.

In dit artikel wordt ervan uitgegaan dat u al een virtuele machine (VM) hebt gemaakt. Als dat niet het geval is, raadpleegt u [een VHD-master installatie kopie voorbereiden en aanpassen](set-up-customize-master-image.md#create-a-vm)

In dit artikel wordt ervan uitgegaan dat u toegang hebt tot verhoogde bevoegdheden op de virtuele machine, ongeacht of deze is ingericht in azure of Hyper-V-beheer. Als dat niet het geval is, raadpleegt [u toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren](../role-based-access-control/elevate-access-global-admin.md).

>[!NOTE]
>Deze instructies gelden voor een Windows-specifieke configuratie voor virtueel bureau blad die kan worden gebruikt met de bestaande processen van uw organisatie.

## <a name="install-office-in-shared-computer-activation-mode"></a>Office installeren in de activerings modus gedeelde computer

Met activering via een gedeelde computer kunt u Office 365 ProPlus implementeren op een computer in uw organisatie die toegankelijk is voor meerdere gebruikers. Zie [overzicht van gedeelde computer activering voor Office 365 ProPlus](/deployoffice/overview-of-shared-computer-activation-for-office-365-proplus/)voor meer informatie over het activeren van gedeelde computers.

Gebruik het [hulp programma voor implementatie van Office](https://www.microsoft.com/download/details.aspx?id=49117) om Office te installeren. Windows 10 Enter prise multi-session ondersteunt alleen de volgende versies van Office:
- Office 365 ProPlus
- Office 365-bedrijf dat wordt geleverd met een Microsoft 365 Business-abonnement

Voor het Office Deployment Tool is een XML-configuratie bestand vereist. Als u het volgende voor beeld wilt aanpassen, raadpleegt u de [configuratie opties voor het hulp programma Office-implementatie](/deployoffice/configuration-options-for-the-office-2016-deployment-tool/).

Deze XML-voorbeeld configuratie heeft de volgende kenmerken:

- Installeer Office vanuit het maandelijkse kanaal en lever updates van het maandelijkse kanaal wanneer ze worden uitgevoerd.
- Gebruik de x64-architectuur.
- Automatische updates uitschakelen.
- Verwijder alle bestaande installaties van Office en migreer de instellingen.
- Activering van gedeelde computer inschakelen.

>[!NOTE]
>De functie voor het zoeken naar het stencil van Visio werkt mogelijk niet zoals verwacht in Windows virtueel bureau blad.

Dit is de XML-voorbeeld configuratie:

- Skype voor bedrijven installeren
- Installeer OneDrive in de modus per gebruiker. Zie voor meer informatie [OneDrive installeren in de modus per computer](#install-onedrive-in-per-machine-mode).

>[!NOTE]
>Activering van gedeelde computers kan worden ingesteld via groepsbeleid objecten (Gpo's) of register instellingen. Het groeps beleidsobject bevindt zich in **computer configuratie\\beleid\\Beheersjablonen\\Microsoft Office 2016 (computer)\\licentie-instellingen**

Het Office Deployment Tool bevat Setup. exe. Als u Office wilt installeren, voert u de volgende opdracht uit op de opdracht regel:

```batch
Setup.exe /configure configuration.xml
```

#### <a name="sample-configurationxml"></a>Voor beeld van configuratie. XML

In het volgende XML-voor beeld wordt de maandelijkse release geïnstalleerd.

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
>Het Office-team raadt aan om de 64-bits installatie te gebruiken voor de para meter **OfficeClientEdition** .

Na de installatie van Office kunt u het standaard gedrag van Office bijwerken. Voer de volgende opdrachten afzonderlijk of in een batch-bestand uit om het gedrag bij te werken.

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

## <a name="install-onedrive-in-per-machine-mode"></a>OneDrive installeren in de modus per computer

OneDrive wordt normaal gesp roken per gebruiker geïnstalleerd. In deze omgeving moet deze per computer worden geïnstalleerd.

U kunt als volgt OneDrive installeren in de modus per computer:

1. Maak eerst een locatie voor het faseren van het OneDrive-installatie programma. Een lokale schijf map of de locatie van de\\\\UNC] (file://unc) is nauw keurig.

2. Down load OneDriveSetup. exe naar uw gefaseerde locatie met deze koppeling: <https://aka.ms/OneDriveWVD-Installer>

3. Als u Office met OneDrive hebt geïnstalleerd door **\<EXCLUDEAPP id = "OneDrive"/\>** te verwijderen, verwijdert u alle bestaande installaties per gebruiker van onedrive van een opdracht prompt met verhoogde bevoegdheid door de volgende opdracht uit te voeren:
    
    ```batch
    "[staged location]\OneDriveSetup.exe" /uninstall
    ```

4. Voer deze opdracht uit vanaf een opdracht prompt met verhoogde bevoegdheid om de register waarde **AllUsersInstall** in te stellen:

    ```batch
    REG ADD "HKLM\Software\Microsoft\OneDrive" /v "AllUsersInstall" /t REG_DWORD /d 1 /reg:64
    ```

5. Voer deze opdracht uit om OneDrive te installeren in de modus per computer:

    ```batch
    Run "[staged location]\OneDriveSetup.exe" /allusers
    ```

6. Voer deze opdracht uit om OneDrive te configureren om te beginnen bij het aanmelden voor alle gebruikers:

    ```batch
    REG ADD "HKLM\Software\Microsoft\Windows\CurrentVersion\Run" /v OneDrive /t REG_SZ /d "C:\Program Files (x86)\Microsoft OneDrive\OneDrive.exe /background" /f
    ```

7. Schakel **gebruikers account op de achtergrond configureren** uit door de volgende opdracht uit te voeren.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "SilentAccountConfig" /t REG_DWORD /d 1 /f
    ```

8. Omleiden en verplaatsen van Windows bekende mappen naar OneDrive door de volgende opdracht uit te voeren.

    ```batch
    REG ADD "HKLM\SOFTWARE\Policies\Microsoft\OneDrive" /v "KFMSilentOptIn" /t REG_SZ /d "<your-AzureAdTenantId>" /f
    ```

## <a name="teams-and-skype"></a>Teams en Skype

Virtueel bureau blad van Windows biedt geen ondersteuning voor Skype voor bedrijven en teams.

## <a name="next-steps"></a>Volgende stappen

Nu u Office aan de installatie kopie hebt toegevoegd, kunt u de installatie kopie van uw hoofd-VHD blijven aanpassen. Zie [een VHD-master installatie kopie voorbereiden en aanpassen](set-up-customize-master-image.md).
