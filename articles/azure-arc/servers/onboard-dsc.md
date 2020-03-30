---
title: Verbonden machine-agent installeren met Windows PowerShell DSC
description: In dit artikel leert u hoe u machines met Azure verbinden met Azure Arc voor servers (preview) met Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 1fb64463b0372202adb04c2deb304c389c7773b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79164681"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>De agent Connected Machine installeren met Windows PowerShell DSC

Met Behulp van [Windows PowerShell Desired State Configuration](https://docs.microsoft.com/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) u software-installatie en -configuratie voor een Windows-computer automatiseren. In dit artikel wordt beschreven hoe u DSC gebruiken om de Azure Arc voor servers Connected Machine-agent op hybride Windows-machines te installeren.

## <a name="requirements"></a>Vereisten

- Windows PowerShell-versie 4.0 of hoger

- De [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC-module

- Een serviceprincipal om de machines niet interactief aan te sluiten op Azure Arc voor servers. Volg de stappen onder de sectie [Een serviceprincipal maken voor onboarding op schaal](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) als u nog geen serviceprincipal voor Arc voor servers hebt gemaakt.

## <a name="install-the-connectedmachine-dsc-module"></a>De ConnectedMachine DSC-module installeren

1. Als u de module handmatig wilt installeren, downloadt u de `$env:ProgramFiles\WindowsPowerShell\Modules folder`broncode en ritst u de inhoud van de projectmap terug naar de . Of voer de volgende opdracht uit die u vanuit de PowerShell-galerie wilt installeren met PowerShellGet (in PowerShell 5.0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Als u de installatie wilt bevestigen, voert u de volgende opdracht uit en ziet u de DSC-bronnen van Azure Connected Machine beschikbaar.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   In de uitvoer ziet u iets dat lijkt op het volgende:

   ![Bevestiging van het installatievoorbeeld van connected machine DSC-module](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>De agent installeren en verbinding maken met Azure

De resources in deze module zijn ontworpen om de azure connected machineagent-configuratie te beheren. Ook inbegrepen is een `AzureConnectedMachineAgent.ps1`PowerShell `AzureConnectedMachineDsc\examples` script, gevonden in de map. Het maakt gebruik van communityresources om de download en installatie te automatiseren en een verbinding met Azure Arc tot stand te brengen. Dit script voert vergelijkbare stappen uit die in de hybride machines verbinden met Azure worden beschreven [in het Azure-portalartikel.](onboard-portal.md)

Als de machine via een proxyserver naar de service moet communiceren, moet u na het installeren van de agent een opdracht uitvoeren die [hier](onboard-portal.md#configure-the-agent-proxy-setting)wordt beschreven. Hiermee stelt u de `https_proxy`proxyserversysteemomgevingsvariabele in. In plaats van de opdracht handmatig uit te voeren, u deze stap met DSC uitvoeren met behulp van de [ComputeManagementDsc-module.](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0)

>[!NOTE]
>Om DSC uit te laten voeren, moet Windows zijn geconfigureerd om PowerShell-afstandsbedieningsopdrachten te ontvangen, zelfs wanneer u een localhost-configuratie uitvoert. Om uw omgeving eenvoudig correct `Set-WsManQuickConfig -Force` te configureren, draait u gewoon in een verhoogde PowerShell Terminal.
>

Configuratiedocumenten (MOF-bestanden) kunnen met de `Start-DscConfiguration` cmdlet op de machine worden toegepast.

Hieronder volgen de parameters die u doorgeeft aan het PowerShell-script dat u wilt gebruiken.

- `TenantId`: De unieke id (GUID) die uw specifieke exemplaar van Azure AD vertegenwoordigt.

- `SubscriptionId`: De abonnements-ID (GUID) van uw Azure-abonnement waarin u de machines wilt hebben.

- `ResourceGroup`: De naam van de resourcegroep waartoe u wilt dat uw aangesloten machines behoren.

- `Location`: Bekijk [ondersteunde Azure-regio's](overview.md#supported-regions). Deze locatie kan hetzelfde of anders zijn als de locatie van de resourcegroep.

- `Tags`: Tekenreeksarray met tags die moeten worden toegepast op de gekoppelde machinebron.

- `Credential`: Een PowerShell-referentieobject met de **ApplicationId** en **het wachtwoord** dat wordt gebruikt om machines op schaal te registreren met behulp van een [serviceprincipal.](onboard-service-principal.md) 

1. Navigeer in een PowerShell-console naar de `.ps1` map waar u het bestand hebt opgeslagen.

2. Voer de volgende PowerShell-opdrachten uit om het MOF-document samen te stellen (zie [DSC-configuraties](https://docs.microsoft.com/powershell/scripting/dsc/configurations/configurations?view=powershell-7)voor informatie over het compileren van DSC-configuraties:

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Hiermee wordt `localhost.mof file` een nieuwe map `C:\dsc`gemaakt met de naam .

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc voor servers (voorbeeld), gaat u naar de Azure-portal om te controleren of de server is verbonden. Bekijk uw machines in de [Azure-portal.](https://aka.ms/hybridmachineportal)

## <a name="adding-to-existing-configurations"></a>Toevoegen aan bestaande configuraties

Deze bron kan worden toegevoegd aan bestaande DSC-configuraties om een end-to-end configuratie voor een machine weer te geven. U deze bron bijvoorbeeld toevoegen aan een configuratie die veilige instellingen van het besturingssysteem instelt.

De [CompsiteResource-module](https://www.powershellgallery.com/packages/compositeresource/0.4.0) van de PowerShell-galerie kan worden gebruikt om een [samengestelde bron](https://docs.microsoft.com/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) van de voorbeeldconfiguratie te maken, om het combineren van configuraties verder te vereenvoudigen.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u uw machine beheert met [Azure Policy](../../governance/policy/overview.md), voor zaken als [vm-gastconfiguratie,](../../governance/policy/concepts/guest-configuration.md)het verifiëren van de machine naar de verwachte Log Analytics-werkruimte, het inschakelen van bewaking [met Azure Monitor met VM's](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)en nog veel meer.

- Meer informatie over de [log analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De agent Logboekanalyse voor Windows en Linux is vereist wanneer u het besturingssysteem en de workloads die op de machine worden uitgevoerd proactief wilt controleren, deze wilt beheren met behulp van automatiseringsrunboeken of oplossingen zoals Updatebeheer of andere Azure-services zoals [Azure Security Center](../../security-center/security-center-intro.md)wilt gebruiken.