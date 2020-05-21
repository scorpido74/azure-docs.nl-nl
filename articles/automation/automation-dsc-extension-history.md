---
title: Versie geschiedenis van de desired state Configuration-extensie voor Azure gebruiken
description: In dit artikel leest u hoe u kunt werken met de versie geschiedenis van de uitbrei ding desired state Configuration (DSC) in Azure.
ms.date: 06/21/2018
keywords: DSC, Power shell, azure, uitbrei ding
author: mgoedtel
ms.author: magoedte
services: automation
ms.service: automation
ms.subservice: dsc
ms.topic: conceptual
ms.openlocfilehash: 4578e3bff7951293c5ec2f1992e597face84b716
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83713196"
---
# <a name="work-with-azure-desired-state-configuration-extension-version-history"></a>Versie geschiedenis van de desired state Configuration-extensie voor Azure gebruiken

De Azure desired state Configuration (DSC) VM-extensie wordt bijgewerkt als nodig ter ondersteuning van verbeteringen en nieuwe mogelijkheden die worden geleverd door Azure, Windows Server en het Windows Management Framework (WMF) dat Windows Power shell bevat.

Dit artikel bevat informatie over elke versie van de Azure DSC VM-extensie, de omgevingen waarin deze worden ondersteund en opmerkingen en opmerkingen over nieuwe functies of wijzigingen.

## <a name="latest-version"></a>Nieuwste versie

### <a name="version-276"></a>Versie 2,76

- **Release datum:**
  - 9 mei 2018 (Azure) | 21 juni 2018 (Azure China ViaNet 21, Azure Government)
- **Ondersteuning voor besturings systeem:**
  - Windows Server 2016
  - Windows Server 2012 R2
  - Windows Server 2012
  - Windows Server 2008 R2 SP1
  - Windows-client 7/8.1/10
  - Nano Server
- **WMF-ondersteuning:**
  - WMF 5.1
  - WMF 5,0 RTM
  - WMF 4,0-update
  - WMF 4.0
- **Variabelen**
  - Azure
  - Azure China ViaNet 21
  - Azure Government
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Verbetering van de uitbrei ding van meta gegevens voor Substatussen en andere kleine oplossingen.

## <a name="supported-versions"></a>Ondersteunde versies

> [!WARNING]
> Versie 2,4 t/m 2,13 gebruik WMF 5,0 open bare preview waarvan de handtekening certificaten zijn verlopen in augustus
> 2016. Zie [blog post](https://devblogs.microsoft.com/powershell/azure-dsc-extension-versions-2-4-up-to-2-13-will-retire-in-august/)voor meer informatie over dit probleem.

### <a name="version-275"></a>Versie 2,75

- **Release datum:** 5 maart 2018
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Nadat GitHub recent is verplaatst naar TLS 1,2, kunt u geen virtuele machine onboarden naar Azure Automation DSC met behulp van zelf-Resource Manager-sjablonen die beschikbaar zijn op Azure Marketplace of gebruikmaken van de DSC-extensie om een configuratie op te halen die wordt gehost op GitHub. Er wordt een fout weer gegeven die vergelijkbaar is met het volgende tijdens het implementeren van de extensie:

    ```json
    {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
        "details": [{
            "code": "Conflict",
            "message": "{
                \"status\": \"Failed\",
                \"error\": {
                    \"code\": \"ResourceDeploymentFailure\",
                    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",
                    \"details\": [ {
                        \"code\": \"VMExtensionProvisioningError\",
                        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'.
                        Error message: \\\"The DSC Extension failed to execute: Error downloading
                        https://github.com/Azure/azure-quickstart-templates/raw/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip
                        after 29 attempts: The request was aborted: Could not create SSL/TLS secure channel..\\nMore information about the failure can
                        be found in the logs located under 'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.74.0.0' on the VM.\\\".\"
                    } ]
                }
            }"
        }]
    }
    ```

  - In de nieuwe extensie versie wordt TLS 1,2 nu afgedwongen. Bij het implementeren van de uitbrei ding als u al de AutoUpgradeMinorVersion = True had in de Resource Manager-sjabloon, krijgt de uitbrei ding automatisch een upgrade naar 2,75. Geef voor hand matige updates `TypeHandlerVersion = 2.75` in uw Resource Manager-sjabloon op.

### <a name="version-270---272"></a>Versie 2,70-2,72

- **Release datum:** 13 november 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Met bug worden & verbeteringen opgelost waarmee het gebruik van DSC Azure Automation via de portal-gebruikers interface en de Resource Manager-sjabloon wordt vereenvoudigd. Zie het [standaard configuratie script](/azure/virtual-machines/extensions/dsc-overview) in de DSC-extensie documentatie voor meer informatie.

### <a name="version-226"></a>Versie 2,26

- **Release datum:** 9 juni 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Verbeteringen voor telemetrie.

### <a name="version-225"></a>Versie 2,25

- **Release datum:** 2 juni 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows-client 7/8.1/10, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Er zijn verschillende fout oplossingen en andere kleine verbeteringen toegevoegd.

### <a name="version-224"></a>Versie 2,24

- **Release datum:** 13 april 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Beschrijft de VM-UUID & DSC-agent-ID als extensie-meta gegevens. Er zijn andere kleine verbeteringen toegevoegd.

### <a name="version-223"></a>Versie 2,23

- **Release datum:** 15 maart 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - Er zijn veel fout oplossingen en andere verbeteringen toegevoegd.

### <a name="version-222"></a>Versie 2,22

- **Release datum:** 8 februari 2017
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen wordt het [Windows Management Framework 5,1](https://devblogs.microsoft.com/powershell/wmf-5-1-releasing-january-2017/) (voor de installatie van WMF moet opnieuw worden opgestart) geïnstalleerd. Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - De DSC-extensie biedt nu ondersteuning voor WMF 5,1.
  - Er zijn kleine andere verbeteringen toegevoegd.

### <a name="version-221"></a>Versie 2,21

- **Release datum:** 2 december 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1, nano server
- **WMF-ondersteuning:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016. voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart). Voor nano server is de DSC-rol geïnstalleerd op de virtuele machine.
- **Nieuwe functies:**
  - De DSC-uitbrei ding is nu beschikbaar op nano server. Deze versie bevat voornamelijk code wijzigingen voor het uitvoeren van de uitbrei ding op nano server.
  - Er zijn kleine andere verbeteringen toegevoegd.

### <a name="version-220"></a>Versie 2,20

- **Release datum:** 2 augustus 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,1 Preview, WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Ondersteuning voor de preview-versie van WMF 5,1. Bij de eerste publicatie was deze versie een optionele upgrade en moest u Wmfversion = ' 5.1 PP ' opgeven in Resource Manager-sjablonen om de WMF 5,1-Preview te installeren.
    Wmfversion = ' laatste ' installeert nog steeds de [WMF 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/).
    Zie [deze blog](https://devblogs.microsoft.com/powershell/announcing-windows-management-framework-wmf-5-1-preview/)voor meer informatie over de preview-versie van WMF 5,1.
  - Er zijn secundaire andere oplossingen en verbeteringen toegevoegd.

### <a name="version--219"></a>Versie 2,19

- **Release datum:** 3 juni 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure, Azure China ViaNet 21, Azure Government
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - De DSC-uitbrei ding is nu onboarding naar Azure China ViaNet 21. Deze versie bevat voornamelijk oplossingen voor het uitvoeren van de uitbrei ding op Azure China ViaNet 21.

### <a name="version-218"></a>Versie 2,18

- **Release datum:** 3 juni 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Maak telemetrie niet-blokkerend wanneer er een fout optreedt tijdens het downloaden van de telemetrie-hotfix (bekend Azure DNS probleem) of tijdens de installatie.
  - Oplossing voor het tijdelijke probleem waarbij de uitbrei ding de verwerking van de configuratie stopt nadat de computer opnieuw is opgestart.
    Hierdoor blijft de DSC-extensie in de status ' overgangen ' staan.
  - Er zijn secundaire andere oplossingen en verbeteringen toegevoegd.

### <a name="version-217"></a>Versie 2,17

- **Release datum:** 26 april 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0 update, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Ondersteuning voor WMF 4,0-update. Zie [deze blog](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-4-0-update-now-available-for-windows-server-2012-windows-server-2008-r2-sp1-and-windows-7-sp1/)voor meer informatie over de WMF 4,0-update.
  - Voer de logica opnieuw uit op fouten die zich voordoen tijdens de installatie van de DSC-uitbrei ding of tijdens het Toep assen van een DSC-configuratie post uitbreiding Als onderdeel van deze wijziging voert de uitbrei ding de installatie opnieuw uit als een eerdere installatie is mislukt of een DSC-configuratie opnieuw heeft ingesteld die eerder is mislukt, gedurende een maximum periode van drie keer tot de voltooiings status (geslaagd/fout) is bereikt of als er een nieuwe aanvraag is ontvangen. Als de extensie mislukt als gevolg van ongeldige gebruikers instellingen/gebruikers invoer, wordt er geen nieuwe poging gedaan. In dit geval moet de uitbrei ding opnieuw worden aangeroepen met een nieuwe aanvraag en de juiste gebruikers instellingen. Opmerking: de DSC-extensie is afhankelijk van de Azure VM-agent voor de nieuwe pogingen. De Azure VM-agent roept de uitbrei ding aan met de laatste mislukte aanvraag totdat de status is geslaagd of mislukt.

### <a name="version-216"></a>Versie 2,16

- **Release datum:** 21 april 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Verbetering van het afhandelen van fouten en andere kleine oplossingen.
  - Nieuwe eigenschap in de DSC-extensie-instellingen. ' ForcePullAndApply ' in AdvancedOptions is toegevoegd om de DSC-uitbrei ding DSC-configuraties in te scha kelen wanneer de vernieuwings modus pull is (in plaats van de standaard push modus). Raadpleeg [deze blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/) voor meer informatie over de instellingen van de DSC-extensie.

### <a name="version-215"></a>Versie 2,15

- **Release datum:** 14 maart 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - In extensie versie 2,14 zijn wijzigingen in de installatie van WMF RTM opgenomen. Tijdens de upgrade van extensie versie 2.13.2.0 naar 2.14.0.0, zult u merken dat sommige DSC-cmdlets mislukken of uw configuratie mislukt met een fout: ' er is geen exemplaar gevonden met de opgegeven eigenschaps waarden '. Zie de opmerkingen bij de [release van DSC](/powershell/scripting/wmf/known-issues/known-issues-dsc)voor meer informatie. De oplossingen voor deze problemen zijn toegevoegd in 2,15-versie.
  - Als u versie 2,14 al hebt geïnstalleerd en een van de bovenstaande twee problemen hebt, moet u deze stappen hand matig uitvoeren. In een Power shell-sessie met verhoogde bevoegdheden:
    - `Remove-Item -Path $env:SystemRoot\system32\Configuration\DSCEngineCache.mof`
    - `mofcomp $env:windir\system32\wbem\DscCoreConfProv.mof`

### <a name="version-214"></a>Versie 2,14

- **Release datum:** 25 februari 2016
- **Ondersteuning voor besturings systeem:** Windows Server 2016 Technical Preview, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 SP1
- **WMF-ondersteuning:** WMF 5,0 RTM, WMF 4,0
- **Omgeving:** Azure
- **Opmerkingen:** Deze versie maakt gebruik van DSC zoals opgenomen in Windows Server 2016 Technical Preview; voor andere Windows-besturings systemen installeert het [Windows Management Framework 5,0 RTM](https://devblogs.microsoft.com/powershell/windows-management-framework-wmf-5-0-rtm-is-now-available-via-the-microsoft-update-catalog/) (installatie van WMF moet opnieuw worden opgestart).
- **Nieuwe functies:**
  - Maakt gebruik van WMF RTM.
  - Hiermee schakelt u het verzamelen van gegevens in om de kwaliteit van de DSC-uitbrei ding te verbeteren. Zie [de blog](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)voor meer informatie.
  - Voorziet in een bijgewerkte instellingen indeling voor de extensie in een resource manager-sjabloon. Zie [de blog](https://devblogs.microsoft.com/powershell/arm-dsc-extension-settings/)voor meer informatie.
  - Oplossingen voor fouten en andere verbeteringen.

## <a name="next-steps"></a>Volgende stappen

- Voor meer informatie over Power shell DSC raadpleegt u het [Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview).
- Bekijk de [Resource Manager-sjabloon voor de DSC-extensie](/azure/virtual-machines/extensions/dsc-template).
- Voor meer functionaliteit die u kunt beheren met Power shell DSC, en voor meer DSC-resources, gaat u naar de [Power shell Gallery](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Zie voor meer informatie over het door geven van gevoelige para meters in configuraties [veilig beheer referenties met de DSC-extensie-handler](/azure/virtual-machines/extensions/dsc-credentials).
