---
title: De Azure Arc voor servers (preview)-agent beheren
description: In dit artikel worden de verschillende beheertaken beschreven die u doorgaans uitvoert tijdens de levenscyclus van de Azure Arc voor servers Connected Machine-agent.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308962"
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

> [!NOTE]
> Als u de agent wilt upgraden, moet u *roottoegangsmachtigingen* hebben of met een account met verhoogde rechten met Sudo.

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

## <a name="about-the-azcmagent-tool"></a>Over de azcmagent tool

De azcmagent-tool (Azcmagent.exe) wordt gebruikt om de Azure Arc voor servers (preview) Connected Machine-agent tijdens de installatie te configureren of om de oorspronkelijke configuratie van de agent na de installatie te wijzigen. Azcmagent.exe biedt opdrachtregelparameters om de agent aan te passen en de status ervan te bekijken:

* **Verbinding maken** - De machine verbinden met Azure Arc

* **Loskoppelen** - De machine loskoppelen van Azure Arc

* **Opnieuw verbinding maken** - Een losgekoppelde machine opnieuw verbinden met Azure Arc

* **Weergeven** - De status van de agent weergeven en de configuratie-eigenschappen (naam van de brongroep, abonnements-id, versie, enz.), die u kunnen helpen bij het oplossen van een probleem met de agent.

* **-h of --help** - Toont beschikbare opdrachtregelparameters

    Als u bijvoorbeeld gedetailleerde hulp wilt zien `azcmagent reconnect -h`voor de parameter **Opnieuw verbinden,** typt u . 

* **-v of --verbose** - Verbose logging inschakelen

U een **Verbinding**maken, **Verbreken**en opnieuw handmatig **verbinden** terwijl u interactief bent aangemeld, of automatiseren met dezelfde serviceprincipal die u hebt gebruikt om meerdere agents aan boord te nemen of met een [microsoft-identiteitsplatformtoegangstoken](../../active-directory/develop/access-tokens.md). Als u geen serviceprincipal hebt gebruikt om de machine te registreren bij Azure Arc voor servers (voorbeeld), raadpleegt u het volgende [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) om een serviceprincipal te maken.

### <a name="connect"></a>Verbinding maken

Met deze parameter wordt een resource in Azure Resource Manager die de machine vertegenwoordigt, gemaakt in Azure. De bron bevindt zich in de opgegeven abonnementsgroep en resourcegroep en `--location` gegevens over de machine worden opgeslagen in het Azure-gebied dat door de instelling is opgegeven. De standaardresourcenaam is de hostnaam van deze machine als deze niet is opgegeven.

Een certificaat dat overeenkomt met de door het systeem toegewezen identiteit van de machine wordt vervolgens lokaal gedownload en opgeslagen. Zodra deze stap is voltooid, beginnen de Azure Connected Machine Metadata Service en Guest Configuration Agent te synchroniseren met Azure Arc voor servers (preview).

Voer de volgende opdracht uit om verbinding te maken met een serviceprincipal:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Voer de volgende opdracht uit om verbinding te maken met een toegangstoken:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Voer de volgende opdracht uit om verbinding te maken met uw verhoogde ingelogde referenties (interactief):

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Verbinding verbreken

Met deze parameter wordt een resource in Azure Resource Manager die de machine vertegenwoordigt, verwijderd in Azure. Het verwijdert de agent niet uit de machine, dit moet als een afzonderlijke stap worden gedaan. Als de machine is losgekoppeld, als u deze opnieuw wilt registreren `azcmagent connect` bij Azure Arc voor servers (voorbeeld), gebruikt u zo dat er een nieuwe bron voor wordt gemaakt in Azure.

Voer de volgende opdracht uit om de verbinding met een serviceprincipal te verbreken:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Voer de volgende opdracht uit om de verbinding met een toegangstoken te verbreken:

`azcmagent disconnect --access-token <accessToken>`

Voer de volgende opdracht uit om verbinding te maken met uw verhoogde ingelogde referenties (interactief):

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Opnieuw verbinden

Deze parameter verbindt de reeds geregistreerde of verbonden machine opnieuw met Azure Arc voor servers (voorbeeld). Dit kan nodig zijn als de machine ten minste 45 dagen is uitgeschakeld om het certificaat te laten verlopen. Deze parameter gebruikt de meegeleverde verificatieopties om nieuwe referenties op te halen die overeenkomen met de Azure Resource Manager-bron die deze machine vertegenwoordigt.

Deze opdracht vereist hogere bevoegdheden dan de Azure [Connected Machine Onboarding-rol.](overview.md#required-permissions)

Voer de volgende opdracht uit om opnieuw verbinding te maken met een serviceprincipal:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Voer de volgende opdracht uit om opnieuw verbinding te maken met een access token:

`azcmagent reconnect --access-token <accessToken>`

Voer de volgende opdracht uit om opnieuw verbinding te maken met uw verhoogde ingelogde referenties (interactief):

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>De agent verwijderen

Voer een van de volgende methoden uit om de Windows- of Linux Connected Machine-agent van de machine te verwijderen. Het verwijderen van de agent maakt de machine niet uit met Arc voor servers (voorbeeld), dit is een apart proces dat u uitvoert wanneer u de machine niet meer in Azure hoeft te beheren.

### <a name="windows-agent"></a>Windows-agent

Beide methoden verwijderen de agent, maar verwijderen de map *C:\Program Files\AzureConnectedMachineAgent* niet op de machine.

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

> [!NOTE]
> Als u de agent wilt verwijderen, moet u *roottoegangsmachtigingen* hebben of met een account met verhoogde rechten met Sudo.

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

## <a name="unregister-machine"></a>Kassamachine

Als u van plan bent de machine niet meer te beheren met ondersteunende services in Azure, voert u de volgende stappen uit om de registratie van de machine bij Arc voor servers uit te zeggen (voorbeeld). U deze stappen uitvoeren voor of nadat u het agent Connected Machine uit de machine hebt verwijderd.

1. Open Azure Arc voor servers (preview) door naar de [Azure-portal](https://aka.ms/hybridmachineportal)te gaan.

2. Selecteer de machine in de lijst, selecteer de ellips (**...**) en selecteer **Verwijderen**.
