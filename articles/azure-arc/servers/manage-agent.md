---
title: De Azure Arc voor servers (preview)-agent beheren
description: In dit artikel worden de verschillende beheertaken beschreven die u doorgaans uitvoert tijdens de levenscyclus van de Azure Arc voor servers Connected Machine-agent.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 758e6123fd09df1e3f8b2e883a729b9fec4328d1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367289"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Het beheren en onderhouden van de Connected Machine-agent

Na de eerste implementatie van de Azure Arc voor servers (preview) Connected Machine-agent voor Windows of Linux, moet u de agent mogelijk opnieuw configureren, upgraden of van de computer verwijderen als deze de pensioenfase in de levenscyclus heeft bereikt. U deze routineonderhoudstaken eenvoudig handmatig of via automatisering beheren, wat zowel operationele fouten als kosten vermindert.

## <a name="upgrading-agent"></a>Upgradeagent

De Azure Connected Machine-agent voor Windows en Linux kan handmatig of automatisch worden geüpgraded naar de nieuwste release, afhankelijk van uw vereisten. In de volgende tabel worden de methoden beschreven die worden ondersteund om de agentupgrade uit te voeren.

| Besturingssysteem | Upgrademethode |
|------------------|----------------|
| Windows | Handmatig<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows-agent

Als u de agent op een Windows-machine wilt bijwerken naar de nieuwste versie, is de agent beschikbaar bij Microsoft Update en kan deze worden geïmplementeerd met uw bestaande software-updatebeheerproces. Het kan ook handmatig worden uitgevoerd vanuit de opdrachtprompt, vanuit een script of andere `AzureConnectedMachine.msi`automatiseringsoplossing of vanuit de wizard UI door het uitvoeren van . 

> [!NOTE]
> * Als u de agent wilt upgraden, moet u *beheerdersmachtigingen* hebben.
> * Als u handmatig wilt upgraden, moet u eerst het Installatiepakket downloaden en kopiëren naar een map op de doelserver of vanuit een gedeelde netwerkmap. 

Als u niet bekend bent met de opdrachtregelopties voor Windows Installer-pakketten, controleert u [msiexec-standaard opdrachtregelopties](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) en [msiexec-opdrachtregelopties](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Upgraden met de wizard Setup

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. **AzureConnectedMachineAgent.msi** uitvoeren om de wizard Setup te starten.

De wizard Setup detecteert of er een vorige versie bestaat en voert vervolgens automatisch een upgrade van de agent uit. Wanneer de upgrade is voltooid, wordt de wizard Setup automatisch gesloten.

#### <a name="to-upgrade-from-the-command-line"></a>Upgraden vanaf de opdrachtregel

1. Meld u aan bij de computer met een account met beheerdersrechten.

2. Voer de volgende opdracht uit om de `C:\Support\Logs` agent in stilte te upgraden en een installatielogboekbestand in de map te maken.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-agent

Om de agent op een Linux-machine bij te werken naar de nieuwste versie, gaat het om twee opdrachten. Eén opdracht om de lokale pakketindex bij te werken met de lijst met de laatst beschikbare pakketten uit de repositories en één opdracht om het lokale pakket te upgraden. 

#### <a name="upgrade-ubuntu"></a>Ubuntu upgraden

1. Voer de volgende opdracht uit om de lokale pakketindex bij te werken met de laatste wijzigingen in de repositories:

    ```bash
    apt update
    ```

2. Voer de volgende opdracht uit om uw systeem te upgraden:

    ```bash
    apt upgrade
    ```

Acties van de [apt-opdracht,](https://help.ubuntu.com/lts/serverguide/apt.html) zoals het installeren en `/var/log/dpkg.log` verwijderen van pakketten, worden geregistreerd in het logboekbestand.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat/CentOS/Amazon Linux

1. Voer de volgende opdracht uit om de lokale pakketindex bij te werken met de laatste wijzigingen in de repositories:

    ```bash
    yum check-update
    ```

2. Voer de volgende opdracht uit om uw systeem te upgraden:

    ```bash
    yum update
    ```

Acties van de [opdracht yum,](https://access.redhat.com/articles/yum-cheat-sheet) zoals installatie en verwijdering `/var/log/yum.log` van pakketten, worden geregistreerd in het logboekbestand. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade SUSE Linux Enterprise

1. Voer de volgende opdracht uit om de lokale pakketindex bij te werken met de laatste wijzigingen in de repositories:

    ```bash
    zypper refresh
    ```

2. Voer de volgende opdracht uit om uw systeem te upgraden:

    ```bash
    zypper update
    ```

Acties van de [zypper](https://en.opensuse.org/Portal:Zypper) opdracht, zoals de installatie en `/var/log/zypper.log` verwijdering van pakketten, worden geregistreerd in het logbestand. 

## <a name="remove-the-agent"></a>De agent verwijderen

Gebruik een van de volgende procedures om de Windows- of Linux-agent te verwijderen met behulp van de opdrachtregel of de installatiewizard die in deze sectie wordt beschreven. Voordat u de installatie van de agent ongedaan maakt, koppelt u de machine eerst los van Azure Arc voor servers (voorbeeld) door de volgende stappen uit te voeren: 

1. Open Azure Arc voor servers (preview) door naar de [Azure-portal](https://aka.ms/hybridmachineportal)te gaan.

2. Selecteer de machine in de lijst, selecteer de ellips (**...**) en selecteer **Verwijderen**.

### <a name="windows-agent"></a>Windows-agent

#### <a name="uninstall-from-control-panel"></a>Verwijderen uit Configuratiescherm

1. Ga als volgt te werk om de Windows-agent van de machine te verwijderen:

    a. Meld u aan bij de computer met een account met beheerdersmachtigingen.  
    b. Selecteer **programma's en onderdelen**in **het Configuratiescherm**.  
    c. Selecteer in **Programma's en onderdelen** **Azure Connected Machine Agent**, selecteer **Verwijderen**en selecteer **Vervolgens Ja**.  

    >[!NOTE]
    > U de wizard agentsetup ook uitvoeren door te dubbelklikken op het **installatiepakket AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Verwijderen van de opdrachtregel

Als u de agent handmatig wilt verwijderen uit de opdrachtprompt of een geautomatiseerde methode wilt gebruiken, zoals een script, u het volgende voorbeeld gebruiken. Eerst moet u de productcode, een GUID die de hoofdidentificatie van het toepassingspakket is, ophalen uit het besturingssysteem. De verwijderen wordt uitgevoerd met behulp van de `msiexec /x {Product Code}`opdrachtregel Msiexec.exe - .
    
1. Open de registereditor.

2. Zoek en `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`kopieer de productcode GUID onder registersleutel.

3. U de agent vervolgens verwijderen met behulp van Msiexec aan de hand van de volgende voorbeelden:

   * Vanuit het type opdrachtregel:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * U dezelfde stappen uitvoeren met PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-agent

Om de Linux-agent te verwijderen, is de opdracht om te gebruiken afhankelijk van het Linux-besturingssysteem.

- Voer voor Ubuntu de volgende opdracht uit:

    ```bash
    sudo apt purge azcmagent
    ```

- Voer voor RHEL, CentOS en Amazon Linux de volgende opdracht uit:

    ```bash
    sudo yum remove azcmagent
    ```

- Voer voor SLES de volgende opdracht uit:

    ```bash
    sudo zypper remove azcmagent
    ```
