---
title: Verbinding maken tussen hybride computers en Azure via de Azure Portal
description: In dit artikel leert u hoe u de agent kunt installeren en computers kunt verbinden met Azure met behulp van Azure Arc voor servers (preview) van de Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 165ed8d0aad7011f1ea71ff870ee4629f1f12613
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898600"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Verbinding maken tussen hybride computers en Azure via de Azure Portal

U kunt Azure-Arc voor servers (preview) inschakelen voor een of meer Windows-of Linux-computers in uw omgeving door een reeks stappen hand matig uit te voeren of een geautomatiseerde methode te gebruiken door een sjabloon script uit te voeren dat wij bieden. Met dit script wordt het downloaden en installeren van beide agents geautomatiseerd.

Voor deze installatie methode moet u beschikken over beheerders rechten op de computer om de agent te installeren en configureren. Op Linux maakt u gebruik van het hoofd account en in Windows lid van de lokale groep Administrators.

Voordat u aan de slag gaat, moet u de [vereisten](overview.md#prerequisites) controleren en controleren of uw abonnement en resources voldoen aan de vereisten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="generate-install-script-from-the-azure-portal"></a>Installatie script genereren op basis van de Azure Portal

Het script voor het automatiseren van het downloaden, installeren en tot stand brengen van de verbinding met Azure Arc is beschikbaar via de Azure Portal. In de volgende stappen wordt beschreven hoe u dit proces kunt volt ooien.

1. Start [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)vanuit uw browser.

2. Op de pagina **machines-Azure-boog** selecteert u **+ toevoegen** in de linkerbovenhoek of selecteert u de optie **machine-Azure-Arc maken** onder in het middelste deel venster. 

3. Selecteer op de pagina **een methode selecteren** de optie voor het genereren van een script voor het **maken**van **interactieve script** tegels.

4. Selecteer op de pagina **script genereren** het abonnement en de resource groep waar u de machine in azure wilt beheren. Selecteer een Azure-locatie waar de meta gegevens van de machine worden opgeslagen.

    >[!NOTE]
    >Azure Arc voor servers (preview) ondersteunt alleen de volgende regio's:
    >- WestUS2
    >- West-Europa
    >- WestAsia
    >

5. Selecteer op de pagina **script genereren** , onder de vervolg keuzelijst **besturings systeem** , het juiste besturings systeem waarop het script wordt uitgevoerd.

6. Als de computer communiceert via een proxy server om verbinding te maken met internet, selecteert u de optie **volgende: > proxy server**. Geef op het tabblad **proxy server** het IP-adres of de naam en het poort nummer van de proxy server op die door de computer wordt gebruikt om te communiceren met de proxy server. Voer de waarde in die volgt op de notatie `http://<proxyURL>:<proxyport>`. Wanneer dit is voltooid, selecteert u **controleren + genereren**.  Als dat niet het geval is, selecteert u **controleren + genereren** om de stappen te volt ooien.

7. Controleer de overzichts gegevens op het tabblad **controleren en genereren** en selecteer vervolgens **downloaden**. Als u wijzigingen wilt aanbrengen, kunt u het **vorige**selecteren.

## <a name="install-and-validate-the-agent-on-windows"></a>De agent in Windows installeren en valideren

### <a name="install-manually"></a>De installatie handmatig uitvoeren

U kunt de aangesloten machine agent hand matig installeren door het Windows Installer-installatie pakket uit te voeren `AzureConnectedMachineAgent.msi` na het downloaden en het te kopiëren naar een map op de doel server of vanuit een gedeelde netwerkmap. Als u het installatie pakket zonder opties uitvoert, wordt een installatie wizard gestart die u kunt volgen om de agent interactief te installeren.

>[!NOTE]
>*Beheerder* bevoegdheden zijn vereist om te installeren of verwijderen van de agent.

Als de machine moet communiceren via een proxy server naar de service, moet u na de installatie van de agent een opdracht uitvoeren die wordt beschreven in een van de onderstaande secties om de systeem omgevingsvariabele van de proxy server in te stellen `https_proxy`.

De volgende tabel ziet u de parameters die worden ondersteund door het installatieprogramma voor de agent vanaf de opdrachtregel.

| Parameter | Beschrijving |
|:--|:--|
| /? | Retourneert een lijst van de opdrachtregelopties. |
| /S | Voert een installatie op de achtergrond zonder tussenkomst van de gebruiker. |

Voer bijvoorbeeld `msiexec.exe /i AzureConnectedMachineAgent.msi /?`in om het installatie programma uit te voeren met de para meter `/?`.

Bestanden voor de verbonden machine agent worden standaard in *C:\Program Files\AzureConnectedMachineAgent* geïnstalleerd. Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-using-scripted-method"></a>Installeren met behulp van een script methode

1. Meld u aan bij de server.

2. Open een Power shell-opdracht prompt met verhoogde bevoegdheden.

3. Ga naar de map of share waarvan u het script hebt gekopieerd en voer deze uit op de-server door de opdracht `./OnboardingScript.ps1`uit te voeren.

### <a name="configure-agent-proxy-setting"></a>Proxy-instelling voor agent configureren

Voer de volgende opdracht uit om de omgevings variabele proxy server in te stellen.

```powershell
# If a proxy server is needed, execute these commands with proxy URL and port
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

>[!NOTE]
>De agent biedt geen ondersteuning voor het instellen van proxy verificatie in deze preview.
>

### <a name="configure-agent-communication"></a>Agent communicatie configureren

Nadat u de agent hebt geïnstalleerd, moet u de agent configureren om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>De agent op Linux installeren en valideren

De verbonden machine agent voor Linux is opgenomen in de voorkeurs pakket indeling voor de distributie (. RPM of. DEB) gehost op de [pakket opslagplaats](https://packages.microsoft.com/)van micro soft. Een shell script bundel `Install_linux_azcmagent.sh` zich bevindt in [https://aka.ms/azcmagent](https://aka.ms/azcmagent) voert de volgende acties uit:

- Hiermee configureert u de hostmachine voor het downloaden van het agent pakket van packages.microsoft.com.
- Hiermee wordt het Hybrid resource provider-pakket geïnstalleerd.
- U kunt de agent ook configureren met uw proxy gegevens door de para meter `--proxy "{proxy-url}:{proxy-port}"` op te nemen.

Het script bevat ook logica voor het identificeren van ondersteunde en niet-ondersteunde distributies, evenals het controleren van de vereiste machtigingen voor het uitvoeren van de installatie. 

In het volgende voor beeld wordt de agent gedownload en geïnstalleerd, zonder een van de voorwaardelijke controles uit te voeren.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Voer de volgende opdrachten uit om de agent te downloaden en te installeren, met inbegrip van de `--proxy` para meter voor het configureren van de agent om te communiceren via uw proxy server:

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-agent-communication"></a>Agent communicatie configureren

Nadat u de agent hebt geïnstalleerd, moet u de agent configureren om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-connection-with-azure-arc"></a>Verbinding met Azure Arc verifiëren

Nadat u de stappen hebt uitgevoerd om de agent te installeren en te configureren om verbinding te maken met Azure Arc voor servers (preview), gaat u naar de Azure Portal om te controleren of de verbinding met de server is geslaagd. U kunt uw machines weer geven in de Azure Portal door te bezoeken [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Geslaagde onboarding](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Opruimen

Als u de verbinding van een machine met Azure Arc voor servers (preview) wilt verbreken, moet u de volgende stappen uitvoeren.

1. Open Azure-Arc voor servers (preview) door naar [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)te gaan.

2. Selecteer de computer in de lijst, klik op het weglatings teken (`...`) en selecteer **verwijderen**.

3. Als u de Windows-agent van de computer wilt verwijderen, voert u de volgende handelingen uit:

    1. Meld u aan bij de computer met een account met beheerders rechten.

    2. Selecteer in **het configuratie scherm**de optie **Program ma's en onderdelen**.

    3. In **Program ma's en onderdelen**selecteert u **Azure Connected machine agent**, selecteert u **verwijderen**en selecteert u vervolgens **Ja**.

        >[!NOTE]
        >U kunt de wizard Setup van agent ook uitvoeren door te dubbel klikken op **AzureConnectedMachineAgent. msi** Installer-pakket.

    Als u het verwijderen wilt scripteren, kunt u het volgende voor beeld gebruiken, waarmee de product code wordt opgehaald en de agent wordt verwijderd met behulp van de Msiexec. exe-opdracht regel `msiexec /x {Product Code}`. Open de REGI ster-editor en zoek onder de register sleutel `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` en zoek de product code-GUID. U kunt de agent vervolgens verwijderen met behulp van Msiexec.

   In het onderstaande voor beeld ziet u hoe de agent wordt verwijderd.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
   ```

4. Als u de Linux-agent wilt verwijderen, voert u de volgende opdracht uit om de agent te verwijderen.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beleid toewijzen aan verbonden computers](../../governance/policy/assign-policy-portal.md)
