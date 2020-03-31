---
title: Hybride machines verbinden met Azure vanuit de Azure-portal
description: In dit artikel leert u hoe u de agent installeert en machines met Azure verbindt met Azure met behulp van Azure Arc voor servers (preview) vanuit de Azure-portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.openlocfilehash: 40885e1de4ff4c16d2a50399c654d8596396ab53
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366369"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Hybride machines verbinden met Azure vanuit de Azure-portal

U Azure Arc inschakelen voor servers (preview) voor een of een klein aantal Windows- of Linux-machines in uw omgeving door handmatig een reeks stappen uit te voeren. U ook een geautomatiseerde methode gebruiken door een sjabloonscript uit te voeren dat wij leveren. Dit script automatiseert het downloaden en installeren van beide agents.

Deze methode vereist dat u beheerdersmachtigingen op de machine hebt om de agent te installeren en te configureren. Op Linux, met behulp van het hoofdaccount en op Windows bent u lid van de groep Lokale beheerders.

Voordat u aan de slag gaat, moet u de [vereisten](overview.md#prerequisites) bekijken en controleren of uw abonnement en resources aan de vereisten voldoen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Het installatiescript genereren vanuit de Azure-portal

Het script om de download en installatie te automatiseren en de verbinding met Azure Arc tot stand te brengen, is beschikbaar via de Azure-portal. Ga als volgt te werk om het proces te voltooien:

1. Ga vanuit uw browser naar de [Azure-portal.](https://aka.ms/hybridmachineportal)

1. Selecteer op de pagina **Machines - Azure Arc** **linksboven toevoegen**of de optie Machine maken - Azure **Arc** onder aan het middelste deelvenster. 

1. Selecteer **op** de pagina Een methode selecteren de **machines toevoegen met interactieve scripttegel** en selecteer **vervolgens Script genereren**.

1. Selecteer **op** de pagina Script genereren de abonnements- en resourcegroep waar u de machine binnen Azure wilt beheren. Selecteer een Azure-locatie waar de metagegevens van de machine worden opgeslagen.

    >[!NOTE]
    >Azure Arc voor servers (preview) ondersteunt alleen de volgende regio's:
    >- WestUS2 WestUS2
    >- West-Europa
    >- WestAzië
    >
    >Bekijk aanvullende overwegingen bij het selecteren van een regio [hier](overview.md#supported-regions) in het artikel Overzicht.

1. Selecteer op de pagina **Script genereren** in de vervolgkeuzelijst **Besturingssysteem** het besturingssysteem waarop het script wordt uitgevoerd.

1. Als de machine communiceert via een proxyserver om verbinding te maken met internet, selecteert u **Volgende: Proxyserver**. 
1. Geef op het tabblad **Proxyserver** het IP-adres van de proxyserver of het naam- en poortnummer op dat de machine zal gebruiken om met de proxyserver te communiceren. Voer de waarde `http://<proxyURL>:<proxyport>`in de notatie in . 
1. Selecteer **Controleren + genereren**.

1. Bekijk op het tabblad **Controleren + genereren** de overzichtsgegevens en selecteer **Downloaden**. Als u nog wijzigingen moet aanbrengen, selecteert u **Vorige**.

## <a name="install-and-validate-the-agent-on-windows"></a>De agent installeren en valideren in Windows

### <a name="install-manually"></a>De installatie handmatig uitvoeren

U de agent Verbonden machine handmatig installeren door het Windows Installer-pakket *AzureConnectedMachineAgent.msi*uit te voeren. 

> [!NOTE]
> * Als u de agent wilt installeren of verwijderen, moet u *beheerdersmachtigingen* hebben.
> * U moet het Installatiepakket eerst downloaden en kopiëren naar een map op de doelserver of vanuit een gedeelde netwerkmap. Als u het Installatiepakket zonder opties uitvoert, wordt een installatiewizard gestart die u volgen om de agent interactief te installeren.

Als de machine via een proxyserver naar de service moet communiceren, moet u na het installeren van de agent een opdracht uitvoeren die later in het artikel wordt beschreven. Hiermee stelt u de `https_proxy`proxyserversysteemomgevingsvariabele in.

Als u niet bekend bent met de opdrachtregelopties voor Windows Installer-pakketten, controleert u [msiexec-standaard opdrachtregelopties](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) en [msiexec-opdrachtregelopties](https://docs.microsoft.com/windows/win32/msi/command-line-options).

Voer bijvoorbeeld het installatieprogramma `/?` uit met de parameter om de help- en snelreferentieoptie te bekijken. 

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Voer de volgende opdracht uit om de `C:\Support\Logs` agent in stilte te installeren en een installatielogboekbestand in de map te maken.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Bestanden voor de agent Verbonden machine worden standaard geïnstalleerd in *C:\Program Files\AzureConnectedMachineAgent*. Als de agent niet start nadat de installatie is voltooid, controleert u de logboeken op gedetailleerde foutgegevens. De logboekmap is *%Programfiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Installeren met de gescripte methode

1. Log in op de server.

1. Open een PowerShell-opdrachtprompt met verhoogde bevoegdheid.

1. Wijzig de map of deel het script naar het script en voer `./OnboardingScript.ps1` het uit op de server door het script uit te voeren.

### <a name="configure-the-agent-proxy-setting"></a>De proxy-instelling voor agenten configureren

Voer de volgende opdracht uit om de omgevingsvariabele proxyserver in te stellen:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>De agent ondersteunt geen proxyverificatie in dit voorbeeld.
>

### <a name="configure-agent-communication"></a>Agentcommunicatie configureren

Nadat u de agent hebt geïnstalleerd, moet u de agent configureren om met de Azure Arc-service te communiceren door de volgende opdracht uit te voeren:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>De agent installeren en valideren op Linux

De Connected Machine agent voor Linux wordt geleverd in de gewenste pakketindeling voor de distributie (. RPM of . DEB) die wordt gehost in de [Microsoft-pakketrepository.](https://packages.microsoft.com/) De [shell `Install_linux_azcmagent.sh` script bundel](https://aka.ms/azcmagent) voert de volgende acties uit:

- Hiermee configureert u de hostmachine om het agentpakket van packages.microsoft.com te downloaden.
- Installeert het pakket Hybrid Resource Provider.

Optioneel u de agent configureren met uw `--proxy "{proxy-url}:{proxy-port}"` proxy-informatie door de parameter op te nemen.

Het script bevat ook logica om de ondersteunde en niet-ondersteunde distributies te identificeren en controleert de machtigingen die nodig zijn om de installatie uit te voeren. 

In het volgende voorbeeld wordt de agent gedownload en geïnstalleerd:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Als u de agent wilt `--proxy` downloaden en installeren, inclusief de parameter voor het configureren van de agent om te communiceren via uw proxyserver, voert u de volgende opdrachten uit:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>De agentcommunicatie configureren

Nadat u de agent hebt geïnstalleerd, configureert u deze om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc verifiëren

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc voor servers (voorbeeld), gaat u naar de Azure-portal om te controleren of de server is verbonden. Bekijk uw machines in de [Azure-portal.](https://aka.ms/hybridmachineportal)

![Een geslaagde serververbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u uw machine beheert met [Azure Policy](../../governance/policy/overview.md), voor zaken als [vm-gastconfiguratie,](../../governance/policy/concepts/guest-configuration.md)het verifiëren van de machine naar de verwachte Log Analytics-werkruimte, het inschakelen van bewaking [met Azure Monitor met VM's](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)en nog veel meer.

- Meer informatie over de [log analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De agent Logboekanalyse voor Windows en Linux is vereist wanneer u het besturingssysteem en de workloads die op de machine worden uitgevoerd proactief wilt controleren, deze wilt beheren met behulp van automatiseringsrunboeken of oplossingen zoals Updatebeheer of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md)wilt gebruiken.
