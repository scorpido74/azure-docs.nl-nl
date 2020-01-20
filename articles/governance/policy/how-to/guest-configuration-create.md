---
title: Gast configuratie beleidsregels maken
description: Meer informatie over het maken van een Azure Policy gast configuratie beleid voor Windows-of Linux-Vm's met Azure PowerShell.
ms.date: 12/16/2019
ms.topic: how-to
ms.openlocfilehash: 7a6c6bb68302d41cd750c59062432a40cf01e8bd
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278457"
---
# <a name="how-to-create-guest-configuration-policies"></a>Gast configuratie beleidsregels maken

Gast configuratie maakt gebruik van een resource module voor [desired state Configuration](/powershell/scripting/dsc/overview/overview) (DSC) om de configuratie voor de controle van de Azure-machines te maken. De DSC-configuratie definieert de toestand waarin de machine zich moet bevindt. Als de evaluatie van de configuratie mislukt, wordt het beleids effect **auditIfNotExists** geactiveerd en wordt de computer als **niet-compatibel**beschouwd.

[Azure Policy-gast configuratie](../concepts/guest-configuration.md) kan alleen worden gebruikt voor het controleren van instellingen binnen machines. Herstel van instellingen binnen computers is nog niet beschikbaar.

Gebruik de volgende acties om uw eigen configuratie te maken voor het valideren van de status van een Azure-machine.

> [!IMPORTANT]
> Aangepaste beleids regels met gast configuratie is een preview-functie.

## <a name="add-the-guestconfiguration-resource-module"></a>De GuestConfiguration-resource module toevoegen

Als u een gast configuratie beleid wilt maken, moet u de resource module toevoegen. Deze resource module kan worden gebruikt met lokaal geïnstalleerde Power shell, met [Azure Cloud shell](https://shell.azure.com), of met de [Azure PowerShell core docker-installatie kopie](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Hoewel de **GuestConfiguration** -module in de bovenstaande omgevingen werkt, moeten de stappen voor het compileren van een DSC-configuratie worden uitgevoerd in Windows power shell 5,1.

### <a name="base-requirements"></a>Basisvereisten

Voor de module gast configuratie resource is de volgende software vereist:

- PowerShell. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/scripting/install/installing-powershell) op.
- Azure PowerShell 1.5.0 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/azure/install-az-ps) op.

### <a name="install-the-module"></a>Installeer de module

Gast configuratie maakt gebruik van de **GuestConfiguration** -resource module voor het maken van DSC-configuraties en het publiceren ervan naar Azure Policy:

1. Voer de volgende opdracht uit vanuit een Power shell-prompt:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Controleer of de module is geïmporteerd:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Aangepaste configuratie voor gast configuratie en-resources maken

De eerste stap voor het maken van een aangepast beleid voor gast configuratie is het maken van de DSC-configuratie. Zie [overzicht van Power shell DSC](/powershell/scripting/dsc/overview/overview)voor een overzicht van DSC-concepten en terminologie.

Als voor uw configuratie alleen resources zijn vereist die zijn ingebouwd met de installatie van de gast configuratie agent, hoeft u alleen een configuratie-MOF-bestand te schrijven. Als u een aanvullend script wilt uitvoeren, moet u een aangepaste resource module ontwerpen.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Vereisten voor aangepaste gast configuratie-resources

Wanneer een gast configuratie een machine controleert, wordt `Test-TargetResource` eerst uitgevoerd om te bepalen of deze in de juiste staat is. De Booleaanse waarde die door de functie wordt geretourneerd, bepaalt of de Azure Resource Manager status voor de gast toewijzing compatibel/niet-compatibel moet zijn. Als de Boole-waarde `$false` voor een resource in de configuratie, wordt `Get-TargetResource`uitgevoerd door de provider. Als de Booleaanse waarde `$true` dan wordt `Get-TargetResource` niet aangeroepen.

#### <a name="configuration-requirements"></a>Configuratie vereisten

De enige vereiste voor een gast configuratie voor het gebruik van een aangepaste configuratie is de naam van de configuratie die consistent is, ongeacht waar deze wordt gebruikt. Deze naam vereiste bevat de naam van het zip-bestand voor het inhouds pakket, de configuratie naam in het MOF-bestand dat is opgeslagen in het inhouds pakket en de configuratie naam die in een resource manager-sjabloon wordt gebruikt als de naam van de gast toewijzing.

#### <a name="get-targetresource-requirements"></a>Vereisten voor Get-TargetResource

De functie `Get-TargetResource` heeft speciale vereisten voor gast configuratie die niet nodig is voor de configuratie van de desired state van Windows.

- De geretourneerde hashtabel moet een eigenschap met de naam **redenen**bevatten.
- De eigenschap redenen moet een matrix zijn.
- Elk item in de matrix moet een hashtabel zijn met sleutels met de naam **code** en een **zin**.

De eigenschap redenen wordt door de service gebruikt om te standaardiseren hoe informatie wordt gepresenteerd wanneer een computer niet meer compatibel is. U kunt elk item beschouwen als een ' reden ' dat de resource niet aan het beleid voldoet. De eigenschap is een matrix omdat een bron niet meer dan één reden kan worden nageleefd.

De instellingen voor de eigenschappen **code** en de **term** worden verwacht door de service. Wanneer u een aangepaste resource ontwerpt, stelt u de tekst (meestal stdout) in die u wilt weer geven als de reden waarom de resource niet aan de voor waarden voor **woord groep**voldoet. **Code** heeft specifieke opmaak vereisten, zodat rapportage duidelijk informatie kan weer geven over de resource die is gebruikt om de controle uit te voeren. Met deze oplossing wordt de gast configuratie uitbreidbaar. Elke opdracht kan worden uitgevoerd om een machine te controleren zolang de uitvoer kan worden vastgelegd en geretourneerd als een teken reeks waarde voor de eigenschap **phrase** .

- **Code** (teken reeks): de naam van de resource, herhaald en vervolgens een korte naam zonder spaties als een id voor de reden. Deze drie waarden moeten door een dubbele punt worden gescheiden zonder spaties.
  - Er wordt een voor beeld `registry:registry:keynotpresent`
- **Woordgroepen** (teken reeks): Lees bare tekst om uit te leggen waarom de instelling niet aan het beleid voldoet.
  - Er wordt een voor beeld `The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

#### <a name="scaffolding-a-guest-configuration-project"></a>Een configuratie project voor een gast steiger

Voor ontwikkel aars die het proces van het aan de slag gaan en het werken vanuit voorbeeld code willen versnellen, bestaat een community-project met de naam **gast configuratie project** als een sjabloon voor de [gips](https://github.com/powershell/plaster) -Power shell-module. Dit hulp programma kan worden gebruikt om een project te maken, met inbegrip van een werkende configuratie en voorbeeld resource, en een verzameling [ziekte](https://github.com/pester/pester) tests om het project te valideren. De sjabloon bevat ook taak lopers voor Visual Studio code voor het automatiseren van het maken en valideren van het gast configuratie pakket. Zie het project [gast configuratie](https://github.com/microsoft/guestconfigurationproject)van het project github voor meer informatie.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Configuratie van aangepaste gast configuratie op Linux

De DSC-configuratie voor gast configuratie in Linux maakt gebruik van de `ChefInSpecResource`-resource om de-engine de naam van de [chef-specificatie](https://www.chef.io/inspec/) definitie te geven. **Name** is de enige vereiste bron eigenschap.

In het volgende voor beeld wordt een configuratie met de naam **baseline**gemaakt, wordt de resource module **GuestConfiguration** geïmporteerd en wordt de `ChefInSpecResource` resource de naam van de Inspec-definitie ingesteld op **linux-patch-Baseline**:

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Zie [een configuratie schrijven, compileren en Toep assen](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)voor meer informatie.

### <a name="custom-guest-configuration-configuration-on-windows"></a>Configuratie van aangepaste gast configuratie in Windows

De DSC-configuratie voor Azure Policy gast configuratie wordt alleen gebruikt door de gast configuratie agent, maar niet met Windows Power shell desired state Configuration.

In het volgende voor beeld wordt een configuratie met de naam **AuditBitLocker**gemaakt, wordt de **GuestConfiguration** -resource module geïmporteerd en wordt de `Service` resource gebruikt om te controleren op een actieve service:

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Zie [een configuratie schrijven, compileren en Toep assen](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)voor meer informatie.

## <a name="create-guest-configuration-custom-policy-package"></a>Aangepast beleids pakket voor de gast configuratie maken

Zodra de MOF is gecompileerd, moeten de ondersteunende bestanden samen worden verpakt. Het voltooide pakket wordt gebruikt door de gast configuratie om de Azure Policy definities te maken. Het pakket bestaat uit:

- De gecompileerde DSC-configuratie als een MOF
- Map modules
  - GuestConfiguration-module
  - DscNativeResources-module
  - Spreek Een map met de chef-specificatie definitie en aanvullende inhoud
  - Windows DSC-resource modules die niet zijn ingebouwd

Met de cmdlet `New-GuestConfigurationPackage` maakt u het pakket. De volgende indeling wordt gebruikt voor het maken van een aangepast pakket:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Para meters van de cmdlet `New-GuestConfigurationPackage`:

- **Naam**: naam van het gast configuratie pakket.
- **Configuratie**: gecompileerd DSC-configuratie document volledig pad.
- **Pad**: pad naar map voor uitvoer. Deze parameter is optioneel. Als u niets opgeeft, wordt het pakket gemaakt in de huidige map.
- **ChefProfilePath**: volledig pad naar het INSPEC-profiel. Deze para meter wordt alleen ondersteund bij het maken van inhoud om Linux te controleren.

Het voltooide pakket moet worden opgeslagen op een locatie die toegankelijk is voor de beheerde virtuele machines. Voor beelden zijn GitHub-opslag plaatsen, een Azure-opslag plaats of Azure-opslag. Als u het pakket liever niet openbaar wilt maken, kunt u een [SAS-token](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) in de URL toevoegen.
U kunt ook [service-eind punten](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) implementeren voor computers in een particulier netwerk, hoewel deze configuratie alleen van toepassing is op toegang tot het pakket en niet communiceert met de service.

## <a name="test-a-guest-configuration-package"></a>Een gast configuratie pakket testen

Nadat u het configuratie pakket hebt gemaakt, maar voordat u het naar Azure publiceert, kunt u de functionaliteit van het pakket testen vanaf uw werk station of CI/CD-omgeving. De GuestConfiguration-module bevat een cmdlet `Test-GuestConfigurationPackage` die dezelfde agent in uw ontwikkelings omgeving laadt als wordt gebruikt binnen Azure-machines. Met deze oplossing kunt u integratie testen lokaal uitvoeren voordat u overbrengt naar gefactureerde test/QA/productie omgevingen.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Para meters van de cmdlet `Test-GuestConfigurationPackage`:

- **Naam**: naam van het gast configuratie beleid.
- **Para meter**: beleids parameters die zijn opgegeven in de hashtabel-indeling.
- **Pad**: volledig pad van het gast configuratie pakket.

De cmdlet ondersteunt ook invoer van de Power shell-pijp lijn. Pipet de uitvoer van `New-GuestConfigurationPackage` cmdlet naar de `Test-GuestConfigurationPackage`-cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Voor meer informatie over het testen met para meters, zie de sectie hieronder [para meters gebruiken in aangepaste gast configuratie beleidsregels](#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>De implementatie bestanden voor Azure Policy definitie en initiatief maken

Wanneer een aangepast beleids pakket voor de gast configuratie is gemaakt en geüpload naar een locatie die toegankelijk is voor de machines, maakt u de beleids definitie voor gast configuratie voor Azure Policy. De `New-GuestConfigurationPolicy`-cmdlet neemt een aangepast beleids pakket voor een openbaar toegankelijke gast configuratie en maakt een **auditIfNotExists** -en **deployIfNotExists** -beleids definitie. Er wordt ook een beleids initiatief definitie gemaakt die beide beleids definities bevat.

In het volgende voor beeld worden het beleid en initiatief definities in een opgegeven pad vanuit een gast configuratie aangepast beleids pakket voor Windows gemaakt, met een naam, beschrijving en versie:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Para meters van de cmdlet `New-GuestConfigurationPolicy`:

- **ContentUri**: open bare http (s)-URI van het inhouds pakket voor de gast configuratie.
- **DisplayName**: weergave naam beleid.
- **Beschrijving**: beschrijving van het beleid.
- **Para meter**: beleids parameters die zijn opgegeven in de hashtabel-indeling.
- **Versie**: beleids versie.
- **Pad**: doelpad voor het maken van beleids definities.
- **Platform**: doel platform (Windows/Linux) voor gast configuratie beleid en inhouds pakket.

De volgende bestanden worden gemaakt door `New-GuestConfigurationPolicy`:

- **auditIfNotExists. json**
- **deployIfNotExists. json**
- **Initiative. json**

De cmdlet-uitvoer retourneert een object dat de weergave naam en het pad van de beleids bestanden bevat.

Als u deze opdracht wilt gebruiken om een aangepast beleids project te maken, kunt u wijzigingen aanbrengen in deze bestanden. Een voor beeld is het wijzigen van de sectie ' If ' om te evalueren of een specifieke tag aanwezig is voor machines. Zie [programmatisch beleids regels maken](./programmatically-create.md)voor meer informatie over het maken van beleid.

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Para meters gebruiken in aangepaste gast configuratie beleidsregels

Gast configuratie ondersteunt het overschrijven van eigenschappen van een configuratie tijdens runtime. Deze functie betekent dat de waarden in het MOF-bestand in het pakket niet als statisch moeten worden beschouwd. De onderdrukkings waarden worden geleverd via Azure Policy en hebben geen invloed op de manier waarop de configuraties worden gemaakt of gecompileerd.

De cmdlets `New-GuestConfigurationPolicy` en `Test-GuestConfigurationPolicyPackage` bevatten een para meter met de naam **para meters**. Met deze para meter wordt een hash-tabel definitie gebruikt, inclusief alle details van elke para meter en worden automatisch alle vereiste secties gemaakt van de bestanden die worden gebruikt voor het maken van elke Azure Policy definitie.

In het volgende voor beeld wordt een Azure Policy gemaakt voor het controleren van een service, waarbij de gebruiker selecteert in een lijst met Services op het moment van beleids toewijzing.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Voor Linux-beleid voegt u de eigenschap **AttributesYmlContent** in uw configuratie toe en overschrijft u de waarden naar behoefte. De gast configuratie agent maakt automatisch het YAML-bestand dat wordt gebruikt door de specificatie voor het opslaan van kenmerken. Zie onderstaand voorbeeld.

```powershell
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Voor elke aanvullende para meter voegt u een hashtabel toe aan de matrix. In de beleids bestanden ziet u eigenschappen die zijn toegevoegd aan de configuratie naam van de gast configuratie die het resource type, de naam, de eigenschap en de waarde identificeert.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Publiceren naar Azure Policy

De **GuestConfiguration** -resource module biedt een manier om zowel beleids definities als initiatief definitie in azure te maken met één stap door de `Publish-GuestConfigurationPolicy`-cmdlet.
De cmdlet heeft alleen de para meter **Path** die verwijst naar de locatie van de drie json-bestanden die zijn gemaakt door `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

De cmdlet `Publish-GuestConfigurationPolicy` accepteert het pad van de Power shell-pijp lijn. Deze functie houdt in dat u de beleids bestanden kunt maken en publiceren in één set opdrachten die zijn gesluisd.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

Met het beleid en de initiatief definities die zijn gemaakt in azure, is de laatste stap het initiatief toe te wijzen. Zie hoe u het initiatief toewijst met [Portal](../assign-policy-portal.md), [Azure cli](../assign-policy-azurecli.md)en [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Gast configuratie beleidsregels moeten **altijd** worden toegewezen met het initiatief waarbij het beleid voor _AuditIfNotExists_ en _DeployIfNotExists_ wordt gecombineerd. Als alleen het _AuditIfNotExists_ -beleid is toegewezen, worden de vereisten niet geïmplementeerd en wordt in het beleid altijd weer gegeven dat ' 0 '-servers compatibel zijn.

## <a name="policy-lifecycle"></a>Levens duur van beleid

Nadat u een aangepaste Azure Policy hebt gepubliceerd met behulp van uw aangepaste inhouds pakket, zijn er twee velden die moeten worden bijgewerkt als u een nieuwe release wilt publiceren.

- **Versie**: wanneer u de `New-GuestConfigurationPolicy` cmdlet uitvoert, moet u een versie nummer opgeven dat groter is dan het aantal dat momenteel is gepubliceerd. Met de eigenschap wordt de versie van de toewijzing van de gast configuratie in het nieuwe beleids bestand bijgewerkt zodat de extensie herkent dat het pakket is bijgewerkt.
- **contentHash**: deze eigenschap wordt automatisch bijgewerkt door de cmdlet `New-GuestConfigurationPolicy`. Het is een hash-waarde van het pakket dat door `New-GuestConfigurationPackage`is gemaakt. De eigenschap moet correct zijn voor het `.zip` bestand dat u publiceert. Als alleen de eigenschap **contentUri** wordt bijgewerkt, bijvoorbeeld in het geval dat iemand een hand matige wijziging van de beleids definitie kan aanbrengen vanuit de portal, wordt het inhouds pakket niet geaccepteerd door de extensie.

De eenvoudigste manier om een bijgewerkt pakket vrij te geven, is het proces dat wordt beschreven in dit artikel herhalen en een bijgewerkt versie nummer opgeven. Dit proces garandeert dat alle eigenschappen correct zijn bijgewerkt.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Windows-groepsbeleid inhoud converteren naar Azure Policy-gast configuratie

Gast configuratie, bij het controleren van Windows-computers, is een implementatie van de desired state-configuratie syntaxis van Power shell. De DSC-Community heeft hulp middelen gepubliceerd voor het converteren van geëxporteerde groepsbeleid sjablonen naar de DSC-indeling. Als u dit hulp programma gebruikt in combi natie met de hierboven beschreven gast configuratie-cmdlets, kunt u Windows-groepsbeleid inhoud en-pakket converteren en publiceren voor Azure Policy naar controle. Voor meer informatie over het gebruik van het hulp programma raadpleegt u het artikel [Snelstartgids: Groepsbeleid converteren naar DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Nadat de inhoud is geconverteerd, zijn de bovenstaande stappen voor het maken van een pakket en het publiceren ervan als Azure Policy hetzelfde als voor elke DSC-inhoud.

## <a name="optional-signing-guest-configuration-packages"></a>Optioneel: gast configuratie pakketten ondertekenen

Aangepaste beleids regels voor gast configuratie SHA256 hash gebruiken om te valideren dat het beleids pakket niet is gewijzigd wanneer het is gepubliceerd op de server die wordt gecontroleerd.
Klanten kunnen eventueel ook een certificaat gebruiken om pakketten te ondertekenen en de gast configuratie-extensie dwingen alleen ondertekende inhoud toe te staan.

Om dit scenario in te scha kelen, zijn er twee stappen die u moet volt ooien. Voer de cmdlet uit om het inhouds pakket te ondertekenen en voeg een tag toe aan de machines waarvoor code moet worden ondertekend.

Als u de functie voor handtekening validatie wilt gebruiken, voert u de `Protect-GuestConfigurationPackage`-cmdlet uit om het pakket te ondertekenen voordat het wordt gepubliceerd. Voor deze cmdlet is het certificaat voor ondertekening van programma code vereist.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Para meters van de cmdlet `Protect-GuestConfigurationPackage`:

- **Pad**: volledig pad van het gast configuratie pakket.
- **Certificaat**: certificaat voor ondertekening van programma code voor het ondertekenen van het pakket. Deze para meter wordt alleen ondersteund bij het ondertekenen van inhoud voor Windows.
- **PrivateGpgKeyPath**: pad naar de persoonlijke GPG-sleutel. Deze para meter wordt alleen ondersteund bij het ondertekenen van inhoud voor Linux.
- **PublicGpgKeyPath**: pad naar de open bare GPG-sleutel. Deze para meter wordt alleen ondersteund bij het ondertekenen van inhoud voor Linux.

GuestConfiguration-agent verwacht dat de open bare sleutel van het certificaat aanwezig is in ' vertrouwde basis certificerings instanties ' op Windows-computers en in het pad `/usr/local/share/ca-certificates/extra` op Linux-machines. Installeer de open bare sleutel van het certificaat op de computer voordat u het aangepaste beleid toepast, zodat het knoop punt ondertekende inhoud verifieert. Dit proces kan worden uitgevoerd met behulp van een wille keurige techniek binnen de virtuele machine of met behulp van Azure Policy. [Hier](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)vindt u een voorbeeld sjabloon.
Het Key Vault toegangs beleid moet de compute-resource provider toegang geven tot certificaten tijdens implementaties. Zie [Key Vault instellen voor virtuele machines in azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)voor gedetailleerde stappen.

Hieronder volgt een voor beeld van het exporteren van de open bare sleutel van een handtekening certificaat om te importeren naar de machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Een goede referentie voor het maken van GPG-sleutels voor gebruik met Linux-machines wordt verschaft door een artikel op GitHub, waardoor [een nieuwe GPG-sleutel wordt gegenereerd](https://help.github.com/en/articles/generating-a-new-gpg-key).

Nadat de inhoud is gepubliceerd, voegt u een tag met de naam `GuestConfigPolicyCertificateValidation` en waarde `enabled` toe aan alle virtuele machines waarvoor ondertekening van programma code vereist is. Deze tag kan op schaal worden geleverd met behulp van Azure Policy. Zie de [Tags Toep assen en het voor beeld van de standaard waarde](../samples/apply-tag-default-value.md) . Zodra deze tag is geïmplementeerd, maakt de beleids definitie die is gegenereerd met de `New-GuestConfigurationPolicy` cmdlet de vereiste via de gast configuratie-extensie.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Problemen met toewijzing van gast configuratie beleid oplossen (preview-versie)

Er is een hulp programma beschikbaar in de preview-versie om te helpen bij het oplossen van problemen Azure Policy toewijzing van gast configuraties. Het hulp programma is in Preview en gepubliceerd op de PowerShell Gallery als module naam [gast configuratie probleem Oplosser](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Voor meer informatie over de cmdlets in dit hulp programma gebruikt u de opdracht Get-Help in Power shell om de ingebouwde richt lijnen weer te geven. Wanneer het hulp programma veelvuldige updates ontvangt, is dat de beste manier om de meest recente informatie te verkrijgen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het controleren van Vm's met [gast configuratie](../concepts/guest-configuration.md).
- Meer informatie over het [programmatisch maken van beleids regels](programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](get-compliance-data.md).
