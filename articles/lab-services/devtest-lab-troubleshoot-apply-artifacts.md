---
title: Problemen met artefacten in Azure DevTest Labs oplossen | Microsoft Documenten
description: Meer informatie over het oplossen van problemen die optreden bij het toepassen van artefacten in een virtuele azure devTest Labs-machine.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456989"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Problemen oplossen bij het toepassen van artefacten in een virtuele azure devTest Labs-machine
Het toepassen van artefacten op een virtuele machine kan om verschillende redenen mislukken. Dit artikel leidt u door enkele van de methoden om mogelijke oorzaken te identificeren.

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure DevTest Labs-experts (DTL) op de [MSDN Azure- en Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer Ondersteuning opdoen.   

> [!NOTE]
> Dit artikel is van toepassing op zowel virtuele Windows- als niet-Windows-machines. Hoewel er enkele verschillen zijn, zullen ze expliciet worden opgeroepen in dit artikel.

## <a name="quick-troubleshooting-steps"></a>Snelle stappen voor het oplossen van problemen
Controleer of de VM wordt uitgevoerd. DevTest Labs vereist dat de VM wordt uitgevoerd en dat de [Microsoft Azure Virtual Machine Agent (VM Agent)](../virtual-machines/extensions/agent-windows.md) is geïnstalleerd en gereed.

> [!TIP]
> Navigeer in de **Azure-portal**naar de pagina **Artefacten beheren** voor de VM om te zien of de VM klaar is voor het toepassen van artefacten. U ziet een bericht helemaal boven aan die pagina. 
> 
> Met **Azure PowerShell**u de vlag **applyArtifacts**inspecteren, die alleen wordt geretourneerd wanneer u een GET-bewerking uitvouwt. Zie de volgende voorbeeldopdracht:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Manieren om problemen op te lossen 
U vm's die zijn gemaakt met DevTest Labs en het implementatiemodel Resource Manager oplossen met behulp van een van de volgende methoden:

- **Azure-portal** - geweldig als u snel een visuele hint wilt krijgen van wat het probleem kan veroorzaken.
- **Azure PowerShell** - als u vertrouwd bent met een PowerShell-prompt, zoekt u snel DevTest Labs-resources op met de Azure PowerShell-cmdlets.

> [!TIP]
> Zie [Artefact-fouten in het lab diagnosticeren voor](devtest-lab-troubleshoot-artifact-failure.md)meer informatie over het controleren van artefact-uitvoering binnen een vm.

## <a name="symptoms-causes-and-potential-resolutions"></a>Symptomen, oorzaken en mogelijke resoluties 

### <a name="artifact-appears-to-hang"></a>Artefact lijkt te hangen   
Een artefact lijkt te hangen totdat een vooraf gedefinieerde time-outperiode is verstreken en het artefact is gemarkeerd als **Mislukt.**

Wanneer een artefact lijkt te hangen, bepaal dan eerst waar het vastzit. Een artefact kan worden geblokkeerd bij een van de volgende stappen tijdens de uitvoering:

- **Tijdens het eerste verzoek**. DevTest Labs maakt een Azure Resource Manager-sjabloon om het gebruik van de Custom Script Extension (CSE) aan te vragen. Daarom wordt achter de schermen een implementatie van een resourcegroep geactiveerd. Wanneer er een fout op dit niveau optreedt, krijgt u details in de **activiteitslogboeken** van de resourcegroep voor de vm in kwestie.  
    - U hebt toegang tot het activiteitenlogboek via de navigatiebalk van de LAB VM-pagina. Wanneer u deze selecteert, ziet u een vermelding voor het **toepassen van artefacten op virtuele machines** (als de bewerking artefacten van toepassing rechtstreeks is geactiveerd) of virtuele machines toevoegen of **wijzigen** (als de bewerking van het toepassen van artefacten deel uitmaakte van het vm-creatieproces).
    - Zoek naar fouten onder deze vermeldingen. Soms wordt de fout niet dienovereenkomstig getagd en moet u elk item onderzoeken.
    - Bij het onderzoeken van de details van elk item, zorg ervoor dat de inhoud van de JSON payload te controleren. Mogelijk ziet u een fout onder aan dat document.
- **Bij het uitvoeren van het artefact.** Het kan zijn als gevolg van netwerk- of opslagproblemen. Zie de betreffende sectie later in dit artikel voor meer informatie. Het kan ook gebeuren vanwege de manier waarop het script is geschreven. Bijvoorbeeld:
    - Een PowerShell-script heeft **verplichte parameters,** maar een geeft er geen waarde aan door, omdat u de gebruiker toestaat om het leeg te laten of omdat u geen standaardwaarde hebt voor de eigenschap in het definitiebestand artifactfile.json. Het script wordt opgehangen omdat het wacht op invoer van de gebruiker.
    - Een PowerShell-script **vereist gebruikersinvoer** als onderdeel van de uitvoering. Scripts moeten worden geschreven om stil te werken zonder tussenkomst van de gebruiker.
- **VM Agent duurt lang om klaar te zijn.** Wanneer de VM voor het eerst wordt gestart of wanneer de aangepaste scriptextensie voor het eerst is geïnstalleerd om het verzoek om artefacten toe te passen, moet de VM mogelijk de VM-agent upgraden of wachten tot de VM-agent wordt geïnitialiseerd. Er kunnen services zijn waarvan de VM-agent afhankelijk is en die lang nodig hebben om te initialiseren. Zie in dergelijke gevallen [het overzicht van Azure Virtual Machine Agent](../virtual-machines/extensions/agent-windows.md) voor verdere probleemoplossing.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Controleren of het artefact lijkt te hangen vanwege het script

1. Log in bij de virtuele machine in kwestie.
2. Kopieer het script lokaal in de virtuele machine of `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`zoek het op de virtuele machine onder . Het is de locatie waar de artefactscripts worden gedownload.
3. Met behulp van een opdrachtprompt met verhoogde bevoegdheid voert u het script lokaal uit, met dezelfde parameterwaarden die worden gebruikt om het probleem te veroorzaken.
4. Bepaal of het script last heeft van ongewenst gedrag. Als dat het zo is, vraag dan een update van het artefact aan (als het afkomstig is van de openbare repo); of, maak de correcties zelf (als het van uw prive repo).

> [!TIP]
> U problemen met artefacten die worden gehost in onze [openbare repo](https://github.com/Azure/azure-devtestlab) corrigeren en de wijzigingen indienen voor onze beoordeling en goedkeuring. Zie de sectie **Bijdragen** in het [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) document.
> 
> Zie AUTHORING.md document [voor](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) informatie over het schrijven van uw eigen artefacten.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Ga als volgt te werk om te controleren of het artefact lijkt te hangen vanwege de VM-agent:
1. Log in bij de virtuele machine in kwestie.
2. Navigeer naar **C:\WindowsAzure\logs**.
3. Zoek en open bestand **WaAppAgent.log**.
4. Zoek naar items die worden weergegeven wanneer de VM-agent wordt gestart en wanneer de initialisatie wordt afgerond (dat wil zeggen dat de eerste hartslag wordt verzonden). Geef nieuwere vermeldingen of specifiek degenen die rond de periode waarvoor u het probleem ervaart.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    In dit voorbeeld u zien dat de begintijd van de VM-agent 10 minuten en 20 seconden heeft geduurd omdat er een hartslag is verzonden. De oorzaak in dit geval was de OOBE-service die lang duurde om te starten.

> [!TIP]
> Zie [Azure-extensies en -functies](../virtual-machines/extensions/overview.md)voor algemene informatie over Azure-extensies.

## <a name="storage-errors"></a>Opslagfouten
DevTest Labs vereist toegang tot het opslagaccount van het lab dat is gemaakt om artefacten in de cache te cachen. Wanneer DevTest Labs een artefact toepast, worden de artefactconfiguratie en de bestanden ervan uit de geconfigureerde opslagplaatsen gelezen. Standaard configureert DevTest Labs de toegang tot de **openbare artefactrepo.**

Afhankelijk van hoe een VM is geconfigureerd, heeft deze mogelijk geen directe toegang tot deze repo. Daarom cachet DevTest Labs de artefacten in een opslagaccount dat is gemaakt wanneer het lab voor het eerst wordt geïnitialiseerd.

Als de toegang tot dit opslagaccount op enigerlei wijze wordt geblokkeerd, zoals wanneer verkeer wordt geblokkeerd van de VM naar de Azure Storage-service, ziet u mogelijk een fout die vergelijkbaar is met de volgende:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

De bovenstaande fout wordt weergegeven in de sectie **Implementatiebericht** op de pagina **Artefact-resultaten** onder **Artefacten beheren**. Het wordt ook weergegeven in de **activiteitslogboeken** onder de resourcegroep van de virtuele machine in kwestie.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Ga als u ervoor zorgen dat de communicatie met de Azure Storage-service niet wordt geblokkeerd:

- **Controleer op toegevoegde netwerkbeveiligingsgroepen (NSG).** Het kan zijn dat er een abonnementsbeleid is toegevoegd waarbij NSG's automatisch worden geconfigureerd in alle virtuele netwerken. Het zou ook van invloed op het standaard virtuele netwerk van het lab, indien gebruikt, of andere virtuele netwerk geconfigureerd in uw lab, gebruikt voor het maken van VM's.
- **Controleer het opslagaccount van het standaardlab** (dat wil zeggen het eerste opslagaccount dat is gemaakt toen het lab werd gemaakt,\<waarvan\>de naam meestal begint met de letter "a" en eindigt met een multi-digit nummer dat wil zeggen, een labnaam #).
    1. Navigeer naar de resourcegroep van het lab.
    2. Zoek de bron van het type **opslagaccount**, waarvan de naam overeenkomt met de conventie.
    3. Navigeer naar de pagina met opslagaccount met de naam **Firewalls en virtuele netwerken.**
    4. Zorg ervoor dat het is ingesteld op **alle netwerken.** Als de optie **Geselecteerde netwerken** is geselecteerd, moet u ervoor zorgen dat de virtuele netwerken van het lab die worden gebruikt om VM's te maken, aan de lijst worden toegevoegd.

Zie [Azure Storage-firewalls en virtuele netwerken configureren](../storage/common/storage-network-security.md)voor meer diepgaande probleemoplossing.

> [!TIP]
> **Regels voor netwerkbeveiliging controleren**. Gebruik [IP-stroomcontrole](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) om te controleren of een regel in een netwerkbeveiligingsgroep verkeer van of naar een virtuele machine blokkeert. U ook effectieve regels voor beveiligingsgroepen controleren om ervoor te zorgen dat de **NSG-regel voor** binnenkomende toestaan bestaat. Zie [Effectieve beveiligingsregels gebruiken om de vm-verkeersstroom op te lossen voor](../virtual-network/diagnose-network-traffic-filter-problem.md)meer informatie.

## <a name="other-sources-of-error"></a>Andere bronnen van fouten
Er zijn andere minder frequente mogelijke bronnen van fouten. Zorg ervoor dat u elk te evalueren om te zien of het van toepassing is op uw zaak. Hier is een van hen: 

- **Verlopen persoonlijke toegangstoken voor de privérepo.** Wanneer het is verlopen, wordt het artefact niet weergegeven en worden scripts die verwijzen naar artefacten uit een opslagplaats met een verlopen privétoegangstoken dienovereenkomstig mislukt.

## <a name="next-steps"></a>Volgende stappen
Als geen van deze fouten is opgetreden en u nog steeds geen artefacten toepassen, u een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

