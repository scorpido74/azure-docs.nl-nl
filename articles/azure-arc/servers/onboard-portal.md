---
title: Verbinding maken tussen hybride computers en Azure via de Azure Portal
description: In dit artikel leert u hoe u de Agent installeert en computers verbindt met Azure met behulp van Azure Arc voor servers (preview) van de Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/23/2020
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bc9bc034abce789046803bbcad5b750984c905cb
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809524"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Verbinding maken tussen hybride computers en Azure via de Azure Portal

U kunt Azure Arc voor servers (preview) inschakelen voor een of meer Windows-of Linux-computers in uw omgeving door een reeks stappen hand matig uit te voeren. U kunt ook een geautomatiseerde methode gebruiken door een sjabloon script uit te voeren dat wij bieden. Met dit script wordt het downloaden en installeren van beide agents geautomatiseerd.

Voor deze methode moet u beheerders rechten op de computer hebben om de agent te installeren en configureren. Op Linux, met behulp van het hoofd account en in Windows, bent u lid van de lokale groep Administrators.

Voordat u aan de slag gaat, moet u de [vereisten](agent-overview.md#prerequisites) controleren en controleren of uw abonnement en resources voldoen aan de vereisten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Het installatie script genereren op basis van de Azure Portal

Het script om het downloaden en installeren te automatiseren en de verbinding met Azure Arc tot stand te brengen, is beschikbaar via de Azure Portal. Ga als volgt te werk om het proces te volt ooien:

1. Ga in uw browser naar de [Azure Portal](https://aka.ms/hybridmachineportal).

1. Op de pagina **machines-Azure-boog** selecteert u **toevoegen**, linksboven of de optie **machine-Azure-boog maken** onder aan het middelste deel venster.

1. Selecteer op de pagina **een methode selecteren** de tegel **computers met interactieve script toevoegen** en selecteer vervolgens **script genereren**.

1. Selecteer op de pagina **script genereren** het abonnement en de resource groep waar u de machine in azure wilt beheren. Selecteer een Azure-locatie waar de meta gegevens van de computer worden opgeslagen.

    >[!NOTE]
    >Azure-Arc voor servers (preview) ondersteunt alleen de volgende regio's:
    >- EastUS
    >- WestUS2
    >- West-Europa
    >- SoutheastAsia
    >
    >Bekijk extra [overwegingen bij het selecteren van een](overview.md#supported-regions) regio in het overzichts artikel.

1. Selecteer op de pagina **script genereren** in de vervolg keuzelijst **besturings systeem** het besturings systeem waarop het script wordt uitgevoerd.

1. Als de computer communiceert via een proxy server om verbinding te maken met internet, selecteert u **volgende: proxy server**.
1. Geef op het tabblad **proxy server** het IP-adres van de proxy server of de naam en het poort nummer op dat door de computer wordt gebruikt om te communiceren met de proxy server. Voer de waarde in de notatie in `http://<proxyURL>:<proxyport>` .
1. Selecteer **controleren + genereren**.

1. Controleer de overzichts gegevens op het tabblad **controleren en genereren** en selecteer vervolgens **downloaden**. Als u nog wijzigingen wilt aanbrengen, selecteert u **vorige**.

## <a name="install-and-validate-the-agent-on-windows"></a>De agent in Windows installeren en valideren

### <a name="install-manually"></a>De installatie handmatig uitvoeren

U kunt de aangesloten machine agent hand matig installeren door de Windows Installer-pakket *AzureConnectedMachineAgent.msi*uit te voeren. U kunt de nieuwste versie van het [Windows agent-Windows Installer pakket](https://aka.ms/AzureConnectedMachineAgent) downloaden van het micro soft Download centrum.

>[!NOTE]
>* Als u de agent wilt installeren of verwijderen, moet u over *beheerders* machtigingen beschikken.
>* U moet eerst het installatie pakket downloaden en kopiëren naar een map op de doel server of vanuit een gedeelde netwerkmap. Als u het installatie pakket zonder opties uitvoert, wordt een installatie wizard gestart die u kunt volgen om de agent interactief te installeren.

Als de machine moet communiceren via een proxy server met de-service, moet u na de installatie van de agent een opdracht uitvoeren die verderop in het artikel wordt beschreven. Hiermee stelt u de systeem omgevingsvariabele van de proxy server in `https_proxy` .

Als u niet bekend bent met de opdracht regel opties voor Windows Installer-pakketten, raadpleegt u [Msiexec Standard-opdracht regel opties](/windows/win32/msi/standard-installer-command-line-options) en [Msiexec-opdracht regel opties](/windows/win32/msi/command-line-options).

Voer bijvoorbeeld het installatie programma uit met de `/?` para meter om de optie Help en snelle referentie te controleren.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

Voer de volgende opdracht uit om de agent op de achtergrond te installeren en een installatie logboek bestand te maken in de `C:\Support\Logs` map die bestaat.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
```

Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-with-the-scripted-method"></a>Installeren met de script methode

1. Meld u aan bij de-server.

1. Open een PowerShell-opdrachtprompt met verhoogde bevoegdheid.

    >[!NOTE]
    >Het script ondersteunt alleen uitvoering vanaf een 64-bits versie van Windows Power shell.
    >

1. Ga naar de map of share waarnaar u het script hebt gekopieerd en voer deze uit op de server door het script uit te voeren `./OnboardingScript.ps1` .

Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="configure-the-agent-proxy-setting"></a>De proxy-instelling voor de agent configureren

Als u de omgevings variabele proxy server wilt instellen, voert u de volgende opdracht uit:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>De agent biedt geen ondersteuning voor het instellen van proxy verificatie in deze preview.
>

### <a name="configure-agent-communication"></a>Agent communicatie configureren

Nadat u de agent hebt geïnstalleerd, moet u de agent configureren om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

`"%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

## <a name="install-and-validate-the-agent-on-linux"></a>De agent op Linux installeren en valideren

De verbonden machine agent voor Linux is opgenomen in de voorkeurs pakket indeling voor de distributie (. RPM of. DEB) die wordt gehost in de micro soft [package-opslag plaats](https://packages.microsoft.com/). De [shell-script bundel `Install_linux_azcmagent.sh` ](https://aka.ms/azcmagent) voert de volgende acties uit:

- Hiermee configureert u de hostmachine voor het downloaden van het agent pakket van packages.microsoft.com.
- Hiermee wordt het Hybrid resource provider-pakket geïnstalleerd.

Desgewenst kunt u de agent met uw proxy gegevens configureren door de para meter op te nemen `--proxy "{proxy-url}:{proxy-port}"` .

Het script bevat ook logica voor het identificeren van ondersteunde en niet-ondersteunde distributies en controleert de machtigingen die nodig zijn om de installatie uit te voeren.

In het volgende voor beeld wordt de agent gedownload en geïnstalleerd:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

Voer de volgende opdrachten uit om de agent te downloaden en te installeren, met inbegrip `--proxy` van de para meter voor het configureren van de agent om te communiceren via uw proxy server:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>De communicatie van de agent configureren

Nadat u de agent hebt geïnstalleerd, configureert u deze om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

`azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"`

>[!NOTE]
>U moet toegangs machtigingen voor het *hoofd* hebben op Linux-machines om **azcmagent**uit te voeren.

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc controleren

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc voor servers (preview), gaat u naar de Azure Portal om te controleren of de server met succes is verbonden. Bekijk uw computers in [Azure Portal](https://aka.ms/hybridmachineportal).

![Een geslaagde server verbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/insights/vminsights-enable-policy.md)en nog veel meer.

- Meer informatie over de [log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u het besturingssysteem en workloads op de machine proactief wilt monitoren, deze wilt beheren met Automation-runbooks of oplossingen zoals Updatebeheer, of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md) wilt gebruiken.