---
title: 'Snelstartgids: computers verbinden met Azure met behulp van Azure Arc voor servers-Power shell'
description: In deze Quick Start leert u hoe u met behulp van Azure Arc computers verbindt met Azure voor servers met Power shell
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, Power shell, desired state Configuration, update beheer, wijzigingen bijhouden, inventaris, runbooks, Python, grafisch, hybride, onboard
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 814be233c80213f84fb81a62caf152536ef4811f
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834084"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Snelstartgids: computers verbinden met Azure met behulp van Azure Arc voor servers-Power shell

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Bekijk de ondersteunde clients en de vereiste netwerk configuratie in het [overzicht van Azure-Arc voor servers](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Een service-principal maken voor onboarding op schaal

Een Service-Principal is een speciale beperkt beheer identiteit die alleen de mini maal vereiste machtiging krijgt om computers te verbinden met Azure. Dit is veiliger dan het gebruik van een krachtigere account, zoals een Tenant Administrator. De service-principal wordt alleen gebruikt tijdens onboarding. U kunt de Service-Principal veilig verwijderen nadat u de gewenste servers hebt verbonden.

> [!NOTE]
> Deze stap wordt aanbevolen, maar niet vereist.

### <a name="steps-to-create-the-service-principal"></a>Stappen voor het maken van de Service-Principal

In dit voor beeld gebruiken we [Azure PowerShell](/powershell/azure/install-az-ps) om een SPN-naam (Service Principal Name) te maken. U kunt ook de stappen volgen die worden vermeld onder [een service-principal maken met behulp van de Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) voor deze taak.

> [!NOTE]
> Wanneer u de Service-Principal maakt, moet u een eigenaar of beheerder van de gebruikers toegang zijn voor het abonnement dat u wilt gebruiken voor onboarding. Als u niet over de juiste machtigingen beschikt om roltoewijzingen te maken, wordt de Service-Principal mogelijk gemaakt, maar kan de machine niet op de onboarding worden uitgevoerd.

De rol `Azure Connected Machine Onboarding` bevat alleen de machtigingen die nodig zijn voor het onboarden. U kunt de machtiging van een SPN definiëren om het bereik van een resource groep of een abonnement toe te staan.

U moet de uitvoer van de cmdlet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) opslaan of u kunt het wacht woord dat u in een latere stap wilt gebruiken, niet ophalen.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

> [!NOTE] 
> Het kan even duren om uw SPN-machtigingen correct in te vullen. Voer de volgende roltoewijzing uit om de machtigingen veel sneller in te stellen.
> ``` PowerShell
> New-AzRoleAssignment -RoleDefinitionName "Azure Connected Machine Onboarding" -ServicePrincipalName $sp.ApplicationId
> ```

Haal nu het wacht woord op met behulp van Power shell.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Kopieer vanuit de uitvoer het **wacht woord** en de **ApplicationId** (uit de vorige stap) en sla ze later op op een veilige plaats, zoals het geheime Archief voor het hulp programma voor Server configuratie. Als u het SPN-wacht woord vergeet of kwijtraakt, kunt u het opnieuw instellen met behulp van de cmdlet [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) .

In het script voor de installatie van agent voorbereiden:

* De eigenschap **ApplicationId** wordt gebruikt voor de para meter `--service-principal-id` die wordt gebruikt om verbinding te maken met de agent
* De eigenschap **Password** wordt gebruikt voor de para meter `--service-principal-secret` die wordt gebruikt om verbinding te maken met de agent.

> [!NOTE]
> Zorg ervoor dat u de Service Principal **ApplicationId** -eigenschap gebruikt, niet de eigenschap **id** . De **id** werkt niet.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>De agent hand matig installeren en verbinding maken met Azure

In de volgende hand leiding kunt u een machine verbinden met Azure door u aan te melden bij de computer en de stappen uit te voeren. U kunt ook computers met Azure verbinden [vanuit de portal](quickstart-onboard-portal.md).

### <a name="download-and-install-the-agent"></a>De agent downloaden en installeren

Voor het installeren van het agent pakket is toegang tot de hoofdmap of lokale beheerder vereist op de doel server, maar er is geen toegang tot Azure.

#### <a name="linux"></a>Linux

Voor **Linux** -servers wordt de agent gedistribueerd via de [pakket opslagplaats van micro soft](https://packages.microsoft.com) met de voorkeurs indeling van het pakket voor de distributie (. RPM of. DEB).

> [!NOTE]
> Tijdens de open bare preview is slechts één pakket vrijgegeven, wat geschikt is voor Ubuntu 16,04 of 18,04.

De eenvoudigste optie is het registreren van de pakket opslagplaats en vervolgens het pakket installeren met behulp van Package Manager van de distributie.
Het bash-script bevindt zich op [https://aka.ms/azcmagent](https://aka.ms/azcmagent) voert de volgende acties uit:

1. Hiermee configureert u de hostmachine die moet worden gedownload van `packages.microsoft.com`.
2. Hiermee wordt het Hybrid resource provider-pakket geïnstalleerd.
3. U kunt de agent ook configureren voor de proxy bewerking als u `--proxy`opgeeft.

Het script bevat ook controles op ondersteunde en niet-ondersteunde distributies en detecteert de vereiste machtigingen voor installatie.

In het onderstaande voor beeld wordt de agent gedownload en geïnstalleerd, zonder een van de voorwaardelijke controles.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Als u liever niet verwijst naar de micro soft-bibliotheek van het pakket, kunt u het pakket bestand van daaruit naar uw interne opslag plaats kopiëren.

#### <a name="windows"></a>Windows

Voor **Windows**is de agent verpakt in een Windows Installer-bestand (`.MSI`) en kan worden gedownload van [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent), dat wordt gehost op [https://download.microsoft.com](https://download.microsoft.com).

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> In Linux wordt het installatie script opnieuw uitgevoerd naar de nieuwste versie. In Windows moet u de ' Azure Connected machine agent ' verwijderen voordat u het installatie programma opnieuw uitvoert om een upgrade uit te voeren.

### <a name="connecting-to-azure"></a>Verbinding maken met Azure

Na de installatie kunt u de agent beheren en configureren met behulp van een opdracht regel programma genaamd `azcmagent.exe`. De agent bevindt zich onder `/opt/azcmagent/bin` op Linux en `$env:programfiles\AzureConnectedMachineAgent` in Windows.

Open in Windows Power shell als Administrator op een doel knooppunt en voer het volgende uit:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{desired-location}" `
  --subscription-id "{your-subscription-id}"
```

Open in Linux een shell en voer uit

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Parameters:

* `tenant-id`: de GUID van de Tenant. U kunt deze in Azure Portal vinden door **Azure Active Directory** - -> **Eigenschappen** -> **Directory-id**te selecteren.
* `subscription-id`: de GUID van het abonnement, in azure, waar u de computer wilt verbinden.
* `resource-group`: de resource groep waar u de computer met verbinding wilt.
* `location`: Zie [Azure-regio's en-locaties](https://azure.microsoft.com/global-infrastructure/regions/). Deze locatie kan hetzelfde zijn als de locatie van de resource groep. Voor de open bare Preview wordt de service ondersteund in **WestUS2**, **Zuidoost-Azië**en **Europa-West**.
* `resource-name`: (*optioneel*) gebruikt voor de Azure resource-representatie van uw on-premises machine. Als u deze waarde niet opgeeft, wordt de hostnaam van de computer gebruikt.

U vindt meer informatie over het hulp programma ' azcmagent ' in [Azcmagent Reference](azcmagent-reference.md).
<!-- Isn't this still needed to view machines? -->

Wanneer de uitvoering is voltooid, is de computer verbonden met Azure. U kunt uw machine weer geven in de Azure Portal door te bezoeken [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Geslaagde onboarding](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Configuratie van proxy server

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

Als op de server een proxy server is vereist, kunt u voor **Linux**het volgende doen:

* Voer het `install_linux_hybrid_agent.sh`-script uit in het gedeelte [de bovenstaande agent installeren](#download-and-install-the-agent) met `--proxy`.
* Als u de agent al hebt geïnstalleerd, voert u de opdracht uit `/opt/azcmagent/bin/hybridrp_proxy add http://{proxy-url}:{proxy-port}`, waarmee de proxy wordt geconfigureerd en de agent opnieuw wordt gestart.

#### <a name="windows"></a>Windows

Voor **Windows**, als de server proxy server vereist voor toegang tot Internet bronnen, moet u de onderstaande opdracht uitvoeren om de proxy server-omgevings variabele in te stellen. Hierdoor kan de agent proxy server gebruiken voor Internet toegang.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Geverifieerde proxy's worden niet ondersteund voor open bare preview-versie.

## <a name="clean-up"></a>Opruimen

Als u een computer wilt loskoppelen van Azure Arc voor servers, moet u twee stappen uitvoeren.

1. Selecteer de machine in de [Portal](https://aka.ms/hybridmachineportal), klik op het weglatings teken (`...`) en selecteer **verwijderen**.
1. Verwijder de agent van de computer.

   In Windows kunt u het configuratie scherm ' apps &-onderdelen ' gebruiken om de agent te verwijderen.
  
  ![& Functies voor apps](./media/quickstart-onboard/apps-and-features.png)

   Als u het verwijderen wilt scripteren, kunt u het volgende voor beeld gebruiken waarmee de **PackageId** wordt opgehaald en de agent wordt verwijderd met behulp van `msiexec /X`.

   Zoek naar de register sleutel `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` en zoek de **PackageId**. U kunt de agent vervolgens verwijderen met behulp van `msiexec`.

   In het onderstaande voor beeld ziet u hoe de agent wordt verwijderd.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Voer op Linux de volgende opdracht uit om de agent te verwijderen.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een beleid toewijzen aan verbonden computers](../../governance/policy/assign-policy-portal.md)
