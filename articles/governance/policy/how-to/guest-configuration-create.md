---
title: Gastconfiguratiebeleid voor Windows maken
description: Meer informatie over het maken van een Azure Policy Guest Configuration policy voor Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: deb51cf502d26dc994bf74ef3cb0c728f624afde
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313984"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Gastconfiguratiebeleid voor Windows maken

Voordat u aangepaste beleidsregels maakt, is het een goed idee om de conceptuele overzichtsgegevens te lezen op de pagina [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Zie de pagina [Hoe u gastconfiguratiebeleid voor Linux](./guest-configuration-create-linux.md) maakt voor meer informatie over het maken van gastconfiguratiebeleid voor Linux

Bij het controleren van Windows gebruikt Gastconfiguratie een DSC-bronmodule [(Desired State Configuration)](/powershell/scripting/dsc/overview/overview) en configuratiebestand. De DSC-configuratie bepaalt de voorwaarde waarin de machine zich moet bevinden.
Als de evaluatie van de configuratie mislukt, wordt de **beleidseffectauditIfNotExists** geactiveerd en wordt de machine als **niet-compatibel**beschouwd .

[Azure Policy Guest Configuration](../concepts/guest-configuration.md) kan alleen worden gebruikt om instellingen in machines te controleren. Herstel van instellingen in machines is nog niet beschikbaar.

Gebruik de volgende acties om uw eigen configuratie te maken voor het valideren van de status van een Azure- of niet-Azure-machine.

> [!IMPORTANT]
> Aangepast beleid met gastconfiguratie is een voorbeeldfunctie.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

Het maken van een gastconfiguratieartefact, het automatisch testen van het artefact, het maken van een beleidsdefinitie en het publiceren van het beleid, is volledig te automatiseren met behulp van de gastconfiguratiemodule in PowerShell. De module kan worden geïnstalleerd op een machine met Windows, macOS of Linux met PowerShell 6.2 of hoger die lokaal wordt uitgevoerd, of met [Azure Cloud Shell](https://shell.azure.com)of met de Azure [PowerShell Core Docker-afbeelding](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Compilatie van configuraties wordt nog niet ondersteund op Linux.

### <a name="base-requirements"></a>Basisvereisten

Besturingssystemen waar de module kan worden geïnstalleerd:

- Linux
- macOS
- Windows

De resourcemodule Gastconfiguratie vereist de volgende software:

- PowerShell 6.2 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/scripting/install/installing-powershell) op.
- Azure PowerShell 1.5.0 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/azure/install-az-ps) op.
  - Alleen de AZ-modules 'Az.Accounts' en 'Az.Resources' zijn vereist.

### <a name="install-the-module"></a>Installeer de module

Ga als bedoeld als u de **module GuestConfiguration installeert** in PowerShell:

1. Voer vanuit een PowerShell-prompt de volgende opdracht uit:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Valideren of de module is geïmporteerd:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Gastconfiguratieartefacten en -beleid voor Windows

Gastconfiguratie gebruikt PowerShell Desired State Configuration als taalabstractie voor het schrijven van wat te controleren in Windows. De agent laadt een standalone exemplaar van PowerShell 6.2, dus er is geen conflict met het gebruik van PowerShell DSC in Windows PowerShell 5.1 en er is geen vereiste om PowerShell 6.2 of hoger vooraf te installeren.

Zie [PowerShell DSC Overview](/powershell/scripting/dsc/overview/overview)voor een overzicht van DSC-concepten en terminologie.

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Hoe gastconfiguratiemodules verschillen van Windows PowerShell DSC-modules

Wanneer gastconfiguratie een machine controleert:

1. De agent `Test-TargetResource` voert eerst uit om te bepalen of de configuratie in de juiste staat is.
1. De booleaanse waarde die door de functie wordt geretourneerd, bepaalt of de Azure Resource Manager-status voor de gasttoewijzing compatibel/niet-compatibel moet zijn.
1. De provider `Get-TargetResource` voert uit om de huidige status van elke instelling terug te geven, zodat er details beschikbaar zijn over de reden waarom een machine niet voldoet en om te bevestigen dat de huidige status voldoet.

### <a name="get-targetresource-requirements"></a>Get-TargetResource-vereisten

De `Get-TargetResource` functie heeft speciale vereisten voor gastconfiguratie die niet nodig zijn voor windows gewenste statusconfiguratie.

- De hashtable die wordt geretourneerd, moet een eigenschap met de naam **Reasons**.
- De eigenschap Redenen moet een array zijn.
- Elk item in de array moet een hashtable zijn met sleutels met de naam **Code** en **Woordgroep.**

De eigenschap Reasons wordt door de service gebruikt om te standaardiseren hoe informatie wordt gepresenteerd wanneer een machine niet aan de voorschriften voldoet. U elk item in Redenen zien als een 'reden' dat de resource niet voldoet. De eigenschap is een array omdat een resource om meer dan één reden niet aan de regels voldoet.

De eigenschappen **Code** en **Woordgroep** worden verwacht door de service. Wanneer u een aangepaste resource maakt, stelt u de tekst (meestal stdout) in die u wilt weergeven als de reden dat de resource niet voldoet als de waarde voor **Woordgroep.** **Code** heeft specifieke opmaakvereisten, zodat rapportage duidelijk informatie kan weergeven over de resource die wordt gebruikt om de controle uit te voeren. Deze oplossing maakt gastconfiguratie uitbreidbaar. Elke opdracht kan worden uitgevoerd zolang de uitvoer kan worden geretourneerd als een tekenreekswaarde voor de eigenschap **Phrase.**

- **Code** (tekenreeks): de naam van de resource, herhaald en vervolgens een korte naam zonder spaties als id om de reden. Deze drie waarden moeten worden dubbelpunt-afgebakend zonder spaties.
  - Een voorbeeld hiervan is`registry:registry:keynotpresent`
- **Woordgroep** (tekenreeks): Tekst die door de mens kan worden gelezen om uit te leggen waarom de instelling niet voldoet.
  - Een voorbeeld hiervan is`The registry key $key is not present on the machine.`

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

De eigenschap Redenen moet ook worden toegevoegd aan het schema MOF voor de resource als ingesloten klasse.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Configuratievereisten

De naam van de aangepaste configuratie moet overal consistent zijn. De naam van het .zip-bestand voor het inhoudspakket, de configuratienaam in het MOF-bestand en de naam van de gasttoewijzing in de sjabloon Resourcebeheer moeten hetzelfde zijn.

### <a name="scaffolding-a-guest-configuration-project"></a>Steigers een gastconfiguratieproject

Ontwikkelaars die het proces van aan de slag willen versnellen en werken vanuit voorbeeldcode, kunnen een communityproject met de naam **Guest Configuration Project**installeren. Het project installeert een sjabloon voor de [Plaster](https://github.com/powershell/plaster) PowerShell-module. Deze tool kan worden gebruikt om een project te steigeren, inclusief een werkende configuratie en voorbeeldbron, en een set [Pester-tests](https://github.com/pester/pester) om het project te valideren. De sjabloon bevat ook taakgebruikers voor Visual Studio Code om het bouwen en valideren van het gastconfiguratiepakket te automatiseren. Zie het GitHub-project [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject)voor meer informatie.

Zie [Een configuratie schrijven, compileren en toepassen voor](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)meer informatie over het werken met configuraties in het algemeen.

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Verwachte inhoud van een gastconfiguratieartefact

Het voltooide pakket wordt gebruikt door Gastconfiguratie om de Azure Policy-definities te maken. Het pakket bestaat uit:

- De gecompileerde DSC-configuratie als Een MOF
- Map Modules
  - Gastconfiguratiemodule
  - Module DscNativeResources
  - (Windows) DSC-resourcemodules vereist door de MOF

PowerShell-cmdlets helpen bij het maken van het pakket.
Er is geen map op basisniveau of versiemap vereist.
De pakketindeling moet een .zip-bestand zijn.

### <a name="storing-guest-configuration-artifacts"></a>Artefacten gastconfiguratie opslaan

Het .zip-pakket moet worden opgeslagen op een locatie die toegankelijk is voor de beheerde virtuele machines.
Voorbeelden hiervan zijn GitHub-repositories, een Azure Repo of Azure-opslag. Als u het pakket liever niet openbaar maakt, u een [SAS-token](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) in de URL opnemen.
U [serviceeindpunt](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) voor machines in een privénetwerk ook implementeren, hoewel deze configuratie alleen van toepassing is op toegang tot het pakket en niet met de service communiceert.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Stap voor stap een aangepast gastconfiguratiecontrolebeleid voor Windows maken

Maak een DSC-configuratie voor controle-instellingen. In het volgende voorbeeld van PowerShell-script wordt een configuratie met de naam `Service` **AuditBitLocker**gemaakt, wordt de **bronmodule PsDscResources** geïmporteerd en wordt de resource gebruikt om te controleren op een lopende service. Het configuratiescript kan worden uitgevoerd vanaf een Windows- of macOS-machine.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker -out ./Config
```

De `Node AuditBitlocker` opdracht is technisch niet vereist, maar `AuditBitlocker.mof` het produceert een `localhost.mof`bestand met de naam in plaats van de standaard, . Als de .mof-bestandsnaam de configuratie volgt, u eenvoudig veel bestanden ordenen wanneer u op schaal werkt.

Zodra de MOF is samengesteld, moeten de ondersteunende bestanden samen worden verpakt. Het voltooide pakket wordt gebruikt door Gastconfiguratie om de Azure Policy-definities te maken.

De `New-GuestConfigurationPackage` cmdlet maakt het pakket. Modules die nodig zijn door de `$Env:PSModulePath`configuratie moeten beschikbaar zijn in . Parameters van `New-GuestConfigurationPackage` de cmdlet bij het maken van Windows-inhoud:

- **Naam**: Naam gastconfiguratiepakket.
- **Configuratie**: Gecompileerd DSC-configuratiedocument volledig pad.
- **Pad:** mappad uitvoer. Deze parameter is optioneel. Als dit niet is opgegeven, wordt het pakket gemaakt in de huidige map.

Voer de volgende opdracht uit om een pakket te maken met behulp van de configuratie in de vorige stap:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Nadat u het configuratiepakket hebt gemaakt, maar het vervolgens hebt gepubliceerd in Azure, u het pakket testen vanuit uw werkstation of CI/CD-omgeving. De guestconfiguration-cmdlet `Test-GuestConfigurationPackage` bevat dezelfde agent in uw ontwikkelomgeving als in Azure-machines. Met deze oplossing u lokaal integratietests uitvoeren voordat u deze vrijgeeft aan gefactureerde cloudomgevingen.

Aangezien de agent de lokale omgeving daadwerkelijk evalueert, moet u in de meeste gevallen de test-cmdlet uitvoeren op hetzelfde OS-platform als u van plan bent te controleren. De test maakt alleen gebruik van modules die zijn opgenomen in het inhoudspakket.

Parameters van `Test-GuestConfigurationPackage` de cmdlet:

- **Naam**: Naam gastconfiguratiebeleid.
- **Parameter**: Beleidsparameters in hashtabelformaat.
- **Pad:** Volledig pad van het gastconfiguratiepakket.

Voer de volgende opdracht uit om het pakket te testen dat door de vorige stap is gemaakt:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

De cmdlet ondersteunt ook input van de PowerShell-pijplijn. Pipet de `New-GuestConfigurationPackage` output van `Test-GuestConfigurationPackage` cmdlet naar de cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

De volgende stap is het publiceren van het bestand naar blob-opslag. Het onderstaande script bevat een functie die u gebruiken om deze taak te automatiseren. De opdrachten die `publish` in de `Az.Storage` functie worden gebruikt, vereisen de module.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
```

Zodra een aangepast beleidspakket gastconfiguratie is gemaakt en geüpload, maakt u de beleidsdefinitie gastconfiguratie. De `New-GuestConfigurationPolicy` cmdlet neemt een aangepast beleidspakket en maakt een beleidsdefinitie.

Parameters van `New-GuestConfigurationPolicy` de cmdlet:

- **ContentUri:** Openbaar http(s) uri van gastconfiguratie inhoudpakket.
- **DisplayName:** De naam van de beleidsweergave.
- **Beschrijving**: Beleidsbeschrijving.
- **Parameter**: Beleidsparameters in hashtabelformaat.
- **Versie**: Beleidsversie.
- **Pad:** doelpad waar beleidsdefinities worden gemaakt.
- **Platform**: Doelplatform (Windows/Linux) voor gastconfiguratiebeleid en inhoudspakket.

In het volgende voorbeeld worden de beleidsdefinities in een bepaald pad gemaakt vanuit een aangepast beleidspakket:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

De volgende bestanden `New-GuestConfigurationPolicy`worden gemaakt door:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

De cmdlet-uitvoer retourneert een object met de naam en het pad van de beleidsbestanden.

Publiceer ten slotte `Publish-GuestConfigurationPolicy` de beleidsdefinities met behulp van de cmdlet. De cmdlet heeft alleen de parameter **Path** die verwijst naar `New-GuestConfigurationPolicy`de locatie van de JSON-bestanden die zijn gemaakt door .

Als u de opdracht Publiceren wilt uitvoeren, hebt u toegang nodig om beleid in Azure te maken. De specifieke autorisatievereisten worden gedocumenteerd op de pagina [Azure Policy Overview.](../overview.md) De beste ingebouwde rol is **Resource Policy Contributor**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

De `Publish-GuestConfigurationPolicy` cmdlet accepteert het pad van de PowerShell-pijplijn. Deze functie betekent dat u de beleidsbestanden maken en publiceren in één set opdrachten met piped.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

Met het beleid dat in Azure is gemaakt, is de laatste stap het toewijzen van het initiatief. Bekijk hoe u het initiatief toewijzen met [Portal,](../assign-policy-portal.md) [Azure CLI](../assign-policy-azurecli.md)en [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Gastconfiguratiebeleid moet **altijd** worden toegewezen aan de hand van het initiatief dat het beleid _AuditIfNotExists_ en _DeployIfNotExists_ combineert. Als alleen het _auditif-existsbeleid_ is toegewezen, worden de vereisten niet geïmplementeerd en toont het beleid altijd aan dat '0'-servers compatibel zijn.

Het toewijzen van een beleidsdefinitie met het effect _DeployIfNotExists_ vereist een extra toegangsniveau. Als u de minste bevoegdheden wilt verlenen, u een aangepaste roldefinitie maken die **de bijdrager resourcebeleid uitbreidt.** In het onderstaande voorbeeld wordt een rol gemaakt met de naam **Resource policy Contributor DINE** met de aanvullende machtiging _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Parameters gebruiken in aangepast gastconfiguratiebeleid

Gastconfiguratie ondersteunt overheersende eigenschappen van een configuratie tijdens uitvoering. Deze functie betekent dat de waarden in het MOF-bestand in het pakket niet als statisch hoeven te worden beschouwd. De overschrijfwaarden worden verstrekt via Azure Policy en hebben geen invloed op de manier waarop de configuraties worden geschreven of gecompileerd.

De `New-GuestConfigurationPolicy` cmdlets `Test-GuestConfigurationPolicyPackage` en bevatten een parameter met de naam **Parameters**. Deze parameter hanteert een hashtable-definitie met alle details over elke parameter en maakt de vereiste secties van elk bestand dat wordt gebruikt voor de Azure Policy-definitie.

In het volgende voorbeeld wordt een beleidsdefinitie gedefinieerd om een service te controleren, waarbij de gebruiker uit een lijst selecteert op het moment van beleidstoewijzing.

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
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Beleidslevenscyclus

Als u een update van het beleid wilt vrijgeven, zijn er twee velden die aandacht vereisen.

- **Versie:** Wanneer u `New-GuestConfigurationPolicy` de cmdlet uitvoert, moet u een versienummer opgeven dat groter is dan wat momenteel wordt gepubliceerd. De eigenschap werkt de versie van de toewijzing voor gastconfiguratie bij, zodat de agent het bijgewerkte pakket herkent.
- **contentHash**: Deze eigenschap wordt `New-GuestConfigurationPolicy` automatisch bijgewerkt door de cmdlet. Het is een hash-waarde van `New-GuestConfigurationPackage`het pakket gemaakt door . De eigenschap moet correct `.zip` zijn voor het bestand dat u publiceert. Als alleen de eigenschap **contentUri** is bijgewerkt, accepteert de extensie het inhoudspakket niet.

De eenvoudigste manier om een bijgewerkt pakket vrij te geven, is door het proces dat in dit artikel wordt beschreven te herhalen en een bijgewerkt versienummer te geven. Dat proces garandeert dat alle eigenschappen correct zijn bijgewerkt.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Inhoud van Windows-groepsbeleid converteren naar Azure Policy Guest Configuration

Gastconfiguratie is bij het controleren van Windows-machines een implementatie van de syntaxis van de gewenste powershell-configuratie. De DSC-community heeft tooling gepubliceerd om geëxporteerde groepsbeleidssjablonen om te zetten naar DSC-indeling. Door deze tool samen met de hierboven beschreven cmdlets gastconfiguratie te gebruiken, u de inhoud van Windows-groepsbeleid converteren en verpakken/publiceren om Azure Policy te controleren. Zie het artikel [Snelstart: Groepsbeleid omzetten in DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart)voor meer informatie over het gebruik van de tool.
Zodra de inhoud is geconverteerd, zijn de bovenstaande stappen om een pakket te maken en te publiceren als Azure Policy hetzelfde als voor elke DSC-inhoud.

## <a name="optional-signing-guest-configuration-packages"></a>Optioneel: Gastconfiguratiepakketten ondertekenen

Het aangepaste beleid voor gastconfiguratie gebruikt SHA256-hash om het beleidspakket te valideren dat niet is gewijzigd.
Optioneel kunnen klanten ook een certificaat gebruiken om pakketten te ondertekenen en de extensie Gastconfiguratie te dwingen alleen ondertekende inhoud toe te staan.

Om dit scenario in te schakelen, zijn er twee stappen die u moet voltooien. Voer de cmdlet uit om het inhoudspakket te ondertekenen en een tag toe te schrijven aan de machines waarvoor code moet worden ondertekend.

Als u de functie Handtekeningvalidatie wilt gebruiken, voert u de `Protect-GuestConfigurationPackage` cmdlet uit om het pakket te ondertekenen voordat het wordt gepubliceerd. Deze cmdlet vereist een 'Code Signing' certificaat.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parameters van `Protect-GuestConfigurationPackage` de cmdlet:

- **Pad:** Volledig pad van het gastconfiguratiepakket.
- **Certificaat**: Code signing certificaat om het pakket te ondertekenen. Deze parameter wordt alleen ondersteund bij het ondertekenen van inhoud voor Windows.

GuestConfiguration agent verwacht dat de certificaat public key aanwezig zijn in "Trusted `/usr/local/share/ca-certificates/extra` Root Certificate Authorities" op Windows machines en in het pad op Linux machines. Installeer de openbare certificaatsleutel op de machine voordat u het aangepaste beleid toepast, voor het knooppunt om ondertekende inhoud te verifiëren. Dit proces kan worden uitgevoerd met behulp van elke techniek in de VM of met behulp van Azure Policy. Hier wordt een voorbeeldsjabloon [gegeven.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
Het toegangsbeleid voor Key Vault moet de compute resource provider toegang geven tot certificaten tijdens implementaties. Zie [Sleutelkluis instellen voor virtuele machines in Azure Resource Manager voor](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)gedetailleerde stappen.

Hieronder volgt een voorbeeld om de openbare sleutel te exporteren van een ondertekeningscertificaat naar importeren naar de machine.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Nadat uw inhoud is gepubliceerd, moet `GuestConfigPolicyCertificateValidation` u `enabled` een tag met naam en waarde toevoegen aan alle virtuele machines waar codeondertekening vereist moet zijn. Zie de [voorbeelden van tagen](../samples/built-in-policies.md#tags) voor hoe tags op schaal kunnen worden geleverd met Azure Policy. Zodra deze tag is ingevoerd, maakt `New-GuestConfigurationPolicy` de beleidsdefinitie die met de cmdlet wordt gegenereerd, de vereiste mogelijk via de extensie Gastconfiguratie.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Problemen met gastconfiguratiebeleidstoewijzingen oplossen (voorbeeld)

Een tool is beschikbaar in preview om problemen op te lossen bij azure policy guest configuration-toewijzingen. De tool is in preview en is gepubliceerd in de PowerShell Gallery als modulenaam [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Voor meer informatie over de cmdlets in deze tool gebruikt u de opdracht Get-Help in PowerShell om de ingebouwde richtlijnen weer te geven. Aangezien de tool regelmatig updates krijgt, is dat de beste manier om de meest recente informatie te krijgen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het controleren van VM's met [gastconfiguratie](../concepts/guest-configuration.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](get-compliance-data.md).
