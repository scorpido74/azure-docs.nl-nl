---
title: Gastconfiguratiebeleid voor Linux maken
description: Meer informatie over het maken van een Azure Policy Guest Configuration policy voor Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: 24442a89d55e34f9ce9697c2f6a32cfc740bcd85
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758960"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Gastconfiguratiebeleid voor Linux maken

Voordat u aangepast beleid maakt, leest u de overzichtsgegevens bij [Azure Policy Guest Configuration](../concepts/guest-configuration.md).
 
Zie de pagina [Hoe u gastconfiguratiebeleid voor Windows](./guest-configuration-create.md) maakt voor meer informatie over het maken van gastconfiguratiebeleid voor Windows

Bij het controleren van Linux maakt Gastconfiguratie gebruik van [Chef InSpec.](https://www.inspec.io/) Het InSpec-profiel definieert de voorwaarde waarin de machine zich moet begeven. Als de evaluatie van de configuratie mislukt, wordt de **beleidseffectauditIfNotExists** geactiveerd en wordt de machine als **niet-compatibel**beschouwd .

[Azure Policy Guest Configuration](../concepts/guest-configuration.md) kan alleen worden gebruikt om instellingen in machines te controleren. Herstel van instellingen in machines is nog niet beschikbaar.

Gebruik de volgende acties om uw eigen configuratie te maken voor het valideren van de status van een Azure- of niet-Azure-machine.

> [!IMPORTANT]
> Aangepast beleid met gastconfiguratie is een voorbeeldfunctie.
>
> De extensie Gastconfiguratie is vereist om audits uit te voeren in virtuele Azure-machines.
> Als u de extensie op schaal wilt implementeren, wijst u de volgende beleidsdefinities toe:
>   - Werkvereisten implementeren om gastconfiguratiebeleid in te schakelen op Windows VM's.
>   - Implementeer vereisten om gastconfiguratiebeleid voor Linux-VM's in te schakelen.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

Het maken van een gastconfiguratieartefact, het automatisch testen van het artefact, het maken van een beleidsdefinitie en het publiceren van het beleid, is volledig te automatiseren met behulp van de gastconfiguratiemodule in PowerShell. De module kan worden geïnstalleerd op een machine met Windows, macOS of Linux met PowerShell 6.2 of hoger die lokaal wordt uitgevoerd, of met [Azure Cloud Shell](https://shell.azure.com)of met de Azure [PowerShell Core Docker-afbeelding](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Compilatie van configuraties wordt niet ondersteund op Linux.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Gastconfiguratieartefacten en -beleid voor Linux

Zelfs in Linux-omgevingen gebruikt Gastconfiguratie gewenste statusconfiguratie als taalabstractie. De implementatie is gebaseerd op native code (C++), dus het vereist geen laden PowerShell. Het vereist echter wel een configuratie MOF die details over de omgeving beschrijft. DSC fungeert als een wrapper voor InSpec om te standaardiseren hoe het wordt uitgevoerd, hoe parameters worden geleverd en hoe de uitvoer wordt geretourneerd naar de service. Er is weinig kennis van DSC vereist bij het werken met aangepaste InSpec-inhoud.

#### <a name="configuration-requirements"></a>Configuratievereisten

De naam van de aangepaste configuratie moet overal consistent zijn. De naam van het .zip-bestand voor het inhoudspakket, de configuratienaam in het MOF-bestand en de naam van de gasttoewijzing in de sjabloon Resourcebeheer moeten hetzelfde zijn.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Aangepaste configuratie van gasten op Linux

Gastconfiguratie op Linux `ChefInSpecResource` gebruikt de bron om de engine de naam van het [InSpec-profiel](https://www.inspec.io/docs/reference/profiles/)te geven. **Naam** is de enige vereiste resourceeigenschap. Maak een YaML-bestand en een Ruby-scriptbestand, zoals hieronder beschreven.

Maak eerst het YaML-bestand dat door InSpec wordt gebruikt. Het bestand bevat basisinformatie over de omgeving. Een voorbeeld wordt hieronder gegeven:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Sla dit bestand `inspec.yml` met naam `linux-path` op in een map met de naam in uw projectmap.

Maak vervolgens het Ruby-bestand met de InSpec-taalabstractie die wordt gebruikt om de machine te controleren.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Sla dit bestand `linux-path.rb` met naam `controls` op `linux-path` in een nieuwe map met de naam in de map.

Maak ten slotte een configuratie, importeer de **PSDesiredStateConfiguration-bronmodule** en compileer de configuratie.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
import-module PSDesiredStateConfiguration
AuditFilePathExists -out ./Config
```

Sla dit bestand `config.ps1` met naam op in de projectmap. Voer het uit in `./config.ps1` PowerShell door uit te voeren in de terminal. Er wordt een nieuw mof-bestand gemaakt.

De `Node AuditFilePathExists` opdracht is technisch niet vereist, maar `AuditFilePathExists.mof` het produceert een `localhost.mof`bestand met de naam in plaats van de standaard, . Als de .mof-bestandsnaam de configuratie volgt, u eenvoudig veel bestanden ordenen wanneer u op schaal werkt.



U moet nu een projectstructuur hebben zoals hieronder:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

De ondersteunende bestanden moeten samen worden verpakt. Het voltooide pakket wordt gebruikt door Gastconfiguratie om de Azure Policy-definities te maken.

De `New-GuestConfigurationPackage` cmdlet maakt het pakket. Parameters van `New-GuestConfigurationPackage` de cmdlet bij het maken van Linux-inhoud:

- **Naam**: Naam gastconfiguratiepakket.
- **Configuratie:** Volledig verloop van het configuratiedocument gecompileerd.
- **Pad:** mappad uitvoer. Deze parameter is optioneel. Als dit niet is opgegeven, wordt het pakket gemaakt in de huidige map.
- **ChefProfilePath:** Volledig pad naar InSpec profiel. Deze parameter wordt alleen ondersteund bij het maken van inhoud om Linux te controleren.

Voer de volgende opdracht uit om een pakket te maken met behulp van de configuratie in de vorige stap:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Nadat u het configuratiepakket hebt gemaakt, maar het vervolgens hebt gepubliceerd in Azure, u het pakket testen vanuit uw werkstation of CI/CD-omgeving. De guestconfiguration-cmdlet `Test-GuestConfigurationPackage` bevat dezelfde agent in uw ontwikkelomgeving als in Azure-machines. Met deze oplossing u lokaal integratietests uitvoeren voordat u deze vrijgeeft aan gefactureerde cloudomgevingen.

Aangezien de agent de lokale omgeving daadwerkelijk evalueert, moet u in de meeste gevallen de test-cmdlet uitvoeren op hetzelfde OS-platform als u van plan bent te controleren.

Parameters van `Test-GuestConfigurationPackage` de cmdlet:

- **Naam**: Naam gastconfiguratiebeleid.
- **Parameter**: Beleidsparameters in hashtabelformaat.
- **Pad:** Volledig pad van het gastconfiguratiepakket.

Voer de volgende opdracht uit om het pakket te testen dat door de vorige stap is gemaakt:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

De cmdlet ondersteunt ook input van de PowerShell-pijplijn. Pipet de `New-GuestConfigurationPackage` output van `Test-GuestConfigurationPackage` cmdlet naar de cmdlet.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
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
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

De volgende bestanden `New-GuestConfigurationPolicy`worden gemaakt door:

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Initiative.json**

De cmdlet-uitvoer retourneert een object met de naam en het pad van de beleidsbestanden.

Publiceer ten slotte `Publish-GuestConfigurationPolicy` de beleidsdefinities met behulp van de cmdlet.
De cmdlet heeft alleen de parameter **Path** die verwijst naar `New-GuestConfigurationPolicy`de locatie van de JSON-bestanden die zijn gemaakt door .

Als u de opdracht Publiceren wilt uitvoeren, hebt u toegang nodig om beleid in Azure te maken. De specifieke autorisatievereisten worden gedocumenteerd op de pagina [Azure Policy Overview.](../overview.md) De beste ingebouwde rol is **Resource Policy Contributor**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 De `Publish-GuestConfigurationPolicy` cmdlet accepteert het pad van de PowerShell-pijplijn. Deze functie betekent dat u de beleidsbestanden maken en publiceren in één set opdrachten met piped.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
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

Met InSpec worden parameters meestal verwerkt als invoer, hetzij tijdens runtime, hetzij als code met behulp van kenmerken. Gastconfiguratie versluiert dit proces, zodat invoer kan worden verstrekt wanneer het beleid is toegewezen. Er wordt automatisch een kenmerkbestand gemaakt in de machine. U hoeft geen bestand in uw project te maken en toe te voegen. Er zijn twee stappen om parameters toe te voegen aan uw Linux-auditproject.

Definieer de invoer in het Ruby-bestand waar u scriptt wat u moet controleren op de machine. Hieronder vindt u een voorbeeld.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

De `New-GuestConfigurationPolicy` cmdlets `Test-GuestConfigurationPolicyPackage` en bevatten een parameter met de naam **Parameters**. Deze parameter neemt een hashtable met alle details over elke parameter en maakt automatisch alle vereiste secties van de bestanden die worden gebruikt om elke Azure Policy-definitie te maken.

In het volgende voorbeeld wordt een beleidsdefinitie gedefinieerd om een bestandspad te controleren, waarbij de gebruiker het pad opgeeft op het moment van beleidstoewijzing.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Neem voor Linux-beleid de eigenschap **AttributesYmlContent** op in uw configuratie en overschrijf de waarden indien nodig. De gastconfiguratieagent maakt automatisch het YAML-bestand dat door InSpec wordt gebruikt om kenmerken op te slaan. Zie het voorbeeld hieronder.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Beleidslevenscyclus

Als u een update van de beleidsdefinitie wilt vrijgeven, zijn er twee velden die aandacht vereisen.

- **Versie:** Wanneer u `New-GuestConfigurationPolicy` de cmdlet uitvoert, moet u een versienummer opgeven dat groter is dan wat momenteel wordt gepubliceerd. De eigenschap werkt de versie van de toewijzing voor gastconfiguratie bij, zodat de agent het bijgewerkte pakket herkent.
- **contentHash**: Deze eigenschap wordt `New-GuestConfigurationPolicy` automatisch bijgewerkt door de cmdlet. Het is een hash-waarde van `New-GuestConfigurationPackage`het pakket gemaakt door . De eigenschap moet correct `.zip` zijn voor het bestand dat u publiceert. Als alleen de eigenschap **contentUri** is bijgewerkt, accepteert de extensie het inhoudspakket niet.

De eenvoudigste manier om een bijgewerkt pakket vrij te geven, is door het proces dat in dit artikel wordt beschreven te herhalen en een bijgewerkt versienummer te geven. Dat proces garandeert dat alle eigenschappen correct zijn bijgewerkt.

## <a name="optional-signing-guest-configuration-packages"></a>Optioneel: Gastconfiguratiepakketten ondertekenen

Het aangepaste beleid voor gastconfiguratie gebruikt SHA256-hash om het beleidspakket te valideren dat niet is gewijzigd.
Optioneel kunnen klanten ook een certificaat gebruiken om pakketten te ondertekenen en de extensie Gastconfiguratie te dwingen alleen ondertekende inhoud toe te staan.

Om dit scenario in te schakelen, zijn er twee stappen die u moet voltooien. Voer de cmdlet uit om het inhoudspakket te ondertekenen en een tag toe te schrijven aan de machines waarvoor code moet worden ondertekend.

Als u de functie Handtekeningvalidatie wilt gebruiken, voert u de `Protect-GuestConfigurationPackage` cmdlet uit om het pakket te ondertekenen voordat het wordt gepubliceerd. Deze cmdlet vereist een 'Code Signing' certificaat.

Parameters van `Protect-GuestConfigurationPackage` de cmdlet:

- **Pad:** Volledig pad van het gastconfiguratiepakket.
- **PublicGpgKeyPath**: Public GPG-sleutelpad. Deze parameter wordt alleen ondersteund bij het ondertekenen van inhoud voor Linux.

Een goede referentie voor het maken van GPG-sleutels om te gebruiken met Linux-machines wordt geleverd door een artikel op GitHub, [het genereren van een nieuwe GPG-toets.](https://help.github.com/en/articles/generating-a-new-gpg-key)

GuestConfiguration agent verwacht dat de certificaat public `/usr/local/share/ca-certificates/extra` key aanwezig zijn in het pad op Linux machines. Installeer de openbare certificaatsleutel op de machine voordat u het aangepaste beleid toepast, voor het knooppunt om ondertekende inhoud te verifiëren. Dit proces kan worden uitgevoerd met behulp van elke techniek in de VM of met Azure Policy. Hier wordt een voorbeeldsjabloon [gegeven.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)
Het toegangsbeleid voor Key Vault moet de compute resource provider toegang geven tot certificaten tijdens implementaties. Zie [Sleutelkluis instellen voor virtuele machines in Azure Resource Manager voor](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault)gedetailleerde stappen.

Nadat uw inhoud is gepubliceerd, moet `GuestConfigPolicyCertificateValidation` u `enabled` een tag met naam en waarde toevoegen aan alle virtuele machines waar codeondertekening vereist moet zijn. Zie de [voorbeelden van tagen](../samples/built-in-policies.md#tags) voor hoe tags op schaal kunnen worden geleverd met Azure Policy. Zodra deze tag is ingevoerd, maakt `New-GuestConfigurationPolicy` de beleidsdefinitie die met de cmdlet wordt gegenereerd, de vereiste mogelijk via de extensie Gastconfiguratie.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Problemen met gastconfiguratiebeleidstoewijzingen oplossen (voorbeeld)

Een tool is beschikbaar in preview om problemen op te lossen bij azure policy guest configuration-toewijzingen. De tool is in preview en is gepubliceerd in de PowerShell Gallery als modulenaam [Guest Configuration Troubleshooter](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Voor meer informatie over de cmdlets in deze tool gebruikt u de opdracht Get-Help in PowerShell om de ingebouwde richtlijnen weer te geven. Aangezien de tool regelmatig updates krijgt, is dat de beste manier om de meest recente informatie te krijgen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het controleren van VM's met [gastconfiguratie](../concepts/guest-configuration.md).
- Begrijpen hoe [u programmatisch beleid maken.](programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](get-compliance-data.md).
