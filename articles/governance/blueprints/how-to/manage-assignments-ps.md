---
title: Opdrachten beheren met PowerShell
description: Meer informatie over het beheren van blauwdruktoewijzingen met de officiële Azure Blueprints PowerShell-module, Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677428"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Opdrachten beheren met PowerShell

Een blauwdruktoewijzing kan worden beheerd met de **Az.Blueprint** Azure PowerShell-module. De module ondersteunt het ophalen, maken, bijwerken en verwijderen van toewijzingen. De module kan ook details ophalen over bestaande blauwdrukdefinities. In dit artikel wordt geverslagd over het installeren van de module en het gebruik ervan.

## <a name="add-the-azblueprint-module"></a>De module Az.Blueprint toevoegen

Als azure PowerShell blauwdruktoewijzingen wilt beheren, moet de module worden toegevoegd. Deze module kan worden gebruikt met lokaal geïnstalleerde PowerShell, met [Azure Cloud Shell](https://shell.azure.com) of met de [Azure PowerShell Docker-installatiekopie](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Basisvereisten

De azure blueprints-module vereist de volgende software:

- Azure PowerShell 1.5.0 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/azure/install-az-ps) op.
- PowerShellGet 2.0.1 of hoger. Als deze nog niet is geïnstalleerd of bijgewerkt, volgt u [deze instructies](/powershell/scripting/gallery/installing-psget) op.

### <a name="install-the-module"></a>Installeer de module

De Azure Blueprints-module voor PowerShell is **Az.Blueprint**.

1. Voer vanuit een PowerShell-prompt met **beheerdersrechten** de volgende opdracht uit:

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Als **Az.Accounts** al is geïnstalleerd, kan `-AllowClobber` het nodig zijn om de installatie te forceren.

1. Valideren of de module is geïmporteerd en de juiste versie is (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Blueprint-definities

De eerste stap naar het werken met een opdracht is vaak het krijgen van een verwijzing naar een blauwdruk definitie.
De `Get-AzBlueprint` cmdlet krijgt een of meer blauwdrukdefinities. De cmdlet kan blauwdrukdefinities krijgen `-ManagementGroupId {mgId}` van een `-SubscriptionId {subId}`beheergroep met of een abonnement met . De parameter **Name** krijgt een blauwdrukdefinitie, maar moet worden gebruikt met **ManagementGroupId** of **SubscriptionId**. **Versie** kan worden gebruikt met **Name** om explicieter te zijn over welke blauwdrukdefinitie wordt geretourneerd. In **Version**plaats van `-LatestPublished` versie , de schakelaar grijpt de meest recent gepubliceerde versie.

In het `Get-AzBlueprint` volgende voorbeeld wordt gebruik gemaakt van alle versies van een blauwdrukdefinitie met de naam `{subId}`'101-blueprints-definition-subscription' van een specifiek abonnement dat wordt weergegeven als:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

De voorbeelduitvoer voor een blauwdrukdefinitie met meerdere versies ziet er als volgt uit:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

De [blauwdrukparameters](../concepts/parameters.md#blueprint-parameters) voor de blauwdrukdefinitie kunnen worden uitgebreid om meer informatie te verstrekken.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Blauwdruktoewijzingen krijgen

Als de blauwdruktoewijzing al bestaat, u `Get-AzBlueprintAssignment` er een verwijzing naar krijgen met de cmdlet. De cmdlet neemt **SubscriptionId** en **Name** als optionele parameters. Als **SubscriptionId** niet is opgegeven, wordt de huidige abonnementscontext gebruikt.

In het `Get-AzBlueprintAssignment` volgende voorbeeld wordt gebruik gemaakt van één blauwdruktoewijzing met de naam `{subId}`'Toewijzing-lock-resource-groepen' van een specifiek abonnement dat wordt weergegeven als:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

De voorbeelduitvoer voor een blauwdruktoewijzing ziet er als volgt uit:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Blauwdruktoewijzingen maken

Als de blauwdruktoewijzing nog niet bestaat, `New-AzBlueprintAssignment` u deze maken met de cmdlet. Deze cmdlet gebruikt de volgende parameters:

- **Naam** [vereist]
  - Hiermee geeft u de naam van de blauwdruktoewijzing op
  - Moet uniek zijn en nog niet bestaan in **SubscriptionId**
- **Blauwdruk** [vereist]
  - Hiermee geeft u de blauwdrukdefinitie op die moet worden toegewezen
  - Gebruiken `Get-AzBlueprint` om het referentieobject op te halen
- **Locatie** [vereist]
  - Hiermee geeft u het gebied op voor het door het systeem toegewezen beheerde identiteits- en abonnementsimplementatieobject dat moet worden gemaakt in
- **Abonnement** (optioneel)
  - Hiermee geeft u het abonnement op waarin de toewijzing wordt geïmplementeerd
  - Als dit niet is opgegeven, wordt de huidige abonnementscontext standaard weergegeven
- **Vergrendelen** (optioneel)
  - Definieert de [blauwdrukbronvergrendeling](../concepts/resource-locking.md) die moet worden gebruikt voor geïmplementeerde resources
  - Ondersteunde opties: _Geen,_ _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Als dit niet is _None_ opgegeven, wordt het niet
- **SystemAssignedIdentity** (optioneel)
  - Selecteer om een door het systeem toegewezen beheerde identiteit voor de toewijzing te maken en de resources te implementeren
  - Standaard voor de parameterset 'identiteit'
  - Kan niet worden gebruikt met **UserAssignedIdentity**
- **UserAssignedIdentity** (optioneel)
  - Hiermee geeft u de door de gebruiker toegewezen beheerde identiteit op die moet worden gebruikt voor de toewijzing en om de resources te implementeren
  - Onderdeel van de parameterset 'identiteit'
  - Kan niet worden gebruikt met **SystemAssignedIdentity**
- **Parameter** (optioneel)
  - Een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met sleutel-/waardeparen voor het instellen van [dynamische parameters](../concepts/parameters.md#dynamic-parameters) op de blauwdruktoewijzing
  - Standaard voor een dynamische parameter is de **standaardwaarde** in de definitie
  - Als een parameter niet is opgegeven en geen **standaardwaarde**heeft, is de parameter niet optioneel

    > [!NOTE]
    > **Parameter** ondersteunt secureStrings niet.

- **ResourceGroupParameter** (optioneel)
  - Een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met artefacten van resourcegroepen
  - Elke artefact-tijdelijke aanduiding voor artefact en object resourcegroep heeft sleutel-/waardeparen voor het dynamisch instellen van **naam** en **locatie** op het artefact van die resourcegroep
  - Als een parameter resourcegroep niet is opgegeven en geen **standaardwaarde**heeft, is de parameter resourcegroep niet optioneel
- **AssignmentFile** (optioneel)
  - Het pad naar een JSON-bestand van een blauwdruktoewijzing
  - Deze parameter maakt deel uit van een PowerShell-parameterset die alleen **Naam,** **Blauwdruk**en **SubscriptionId**bevat, plus de algemene parameters.

### <a name="example-1-provide-parameters"></a>Voorbeeld 1: Parameters opgeven

In het volgende voorbeeld wordt een nieuwe toewijzing van versie '1.1' `Get-AzBlueprint`gemaakt van de blauwdrukdefinitie 'mijn blauwdruk' die is opgehaald met , wordt de locatie van het beheerde identiteit en toewijzingsobject ingesteld op 'westus2', worden de resources vergrendeld met _AllResourcesReadOnly_en worden de hashtabellen voor zowel **Parameter** als **ResourceGroupParameter** ingesteld op een specifiek abonnement dat wordt weergegeven als `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

De voorbeelduitvoer voor het maken van een blauwdruktoewijzing ziet er als volgt uit:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Voorbeeld 2: Een JSON-toewijzingsdefinitiebestand gebruiken

In het volgende voorbeeld wordt bijna dezelfde toewijzing als [voorbeeld 1.](#example-1-provide-parameters)
In plaats van parameters door te geven aan de cmdlet, wordt in het voorbeeld het gebruik van een JSON-toewijzingsdefinitiebestand en de parameter **AssignmentFile** weergegeven. Bovendien is de eigenschap **excludedPrincipals** geconfigureerd als onderdeel van **vergrendelingen.** Er is geen PowerShell-parameter voor **uitgeslotenPrincipals** en de eigenschap kan alleen worden geconfigureerd door deze in te stellen via het JSON-toewijzingsdefinitiebestand.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Zie de aanvraaginstantie in [Voorbeeld: Toewijzing met door de gebruiker toegewezen beheerde identiteit](/rest/api/blueprints/assignments/createorupdate#examples) voor een door de gebruiker toegewezen beheerde identiteit voor een door de gebruiker toegewezen definitiebestand voor een door de gebruiker toegewezen beheerde identiteit.

## <a name="update-blueprint-assignments"></a>Blauwdruktoewijzingen bijwerken

Soms is het nodig om een blauwdruktoewijzing bij te werken die al is gemaakt. De `Set-AzBlueprintAssignment` cmdlet verwerkt deze actie. De cmdlet neemt de meeste `New-AzBlueprintAssignment` van dezelfde parameters die de cmdlet doet, waardoor alles wat is ingesteld op de opdracht worden bijgewerkt. De uitzonderingen zijn de _naam,_ _blauwdruk_en _abonnementid_. Alleen de opgegeven waarden worden bijgewerkt.

Zie [regels voor het bijwerken van toewijzingen](./update-existing-assignments.md#rules-for-updating-assignments)als u wilt begrijpen wat er gebeurt bij het bijwerken van een blauwdruktoewijzing.

- **Naam** [vereist]
  - Hiermee geeft u de naam op van de blauwdruktoewijzing die moet worden bijgewerkt
  - Wordt gebruikt om de toewijzing te vinden die moet worden bijgewerkt, niet om de toewijzing te wijzigen
- **Blauwdruk** [vereist]
  - Hiermee geeft u de blauwdrukdefinitie van de blauwdruktoewijzing op
  - Gebruiken `Get-AzBlueprint` om het referentieobject op te halen
  - Wordt gebruikt om de toewijzing te vinden die moet worden bijgewerkt, niet om de toewijzing te wijzigen
- **Locatie** (optioneel)
  - Hiermee geeft u het gebied op voor het door het systeem toegewezen beheerde identiteits- en abonnementsimplementatieobject dat moet worden gemaakt in
- **Abonnement** (optioneel)
  - Hiermee geeft u het abonnement op waarin de toewijzing wordt geïmplementeerd
  - Als dit niet is opgegeven, wordt de huidige abonnementscontext standaard weergegeven
  - Wordt gebruikt om de toewijzing te vinden die moet worden bijgewerkt, niet om de toewijzing te wijzigen
- **Vergrendelen** (optioneel)
  - Definieert de [blauwdrukbronvergrendeling](../concepts/resource-locking.md) die moet worden gebruikt voor geïmplementeerde resources
  - Ondersteunde opties: _Geen,_ _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (optioneel)
  - Selecteer om een door het systeem toegewezen beheerde identiteit voor de toewijzing te maken en de resources te implementeren
  - Standaard voor de parameterset 'identiteit'
  - Kan niet worden gebruikt met **UserAssignedIdentity**
- **UserAssignedIdentity** (optioneel)
  - Hiermee geeft u de door de gebruiker toegewezen beheerde identiteit op die moet worden gebruikt voor de toewijzing en om de resources te implementeren
  - Onderdeel van de parameterset 'identiteit'
  - Kan niet worden gebruikt met **SystemAssignedIdentity**
- **Parameter** (optioneel)
  - Een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met sleutel-/waardeparen voor het instellen van [dynamische parameters](../concepts/parameters.md#dynamic-parameters) op de blauwdruktoewijzing
  - Standaard voor een dynamische parameter is de **standaardwaarde** in de definitie
  - Als een parameter niet is opgegeven en geen **standaardwaarde**heeft, is de parameter niet optioneel

    > [!NOTE]
    > **Parameter** ondersteunt secureStrings niet.

- **ResourceGroupParameter** (optioneel)
  - Een [hashtabel](/powershell/module/microsoft.powershell.core/about/about_hash_tables) met artefacten van resourcegroepen
  - Elke artefact-tijdelijke aanduiding voor artefact en object resourcegroep heeft sleutel-/waardeparen voor het dynamisch instellen van **naam** en **locatie** op het artefact van die resourcegroep
  - Als een parameter resourcegroep niet is opgegeven en geen **standaardwaarde**heeft, is de parameter resourcegroep niet optioneel

In het volgende voorbeeld wordt de toewijzing van versie '1.1' van `Get-AzBlueprint` de blauwdrukdefinitie 'mijn blauwdruk' bijgewerkt door de vergrendelingsmodus te wijzigen:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

De voorbeelduitvoer voor het maken van een blauwdruktoewijzing ziet er als volgt uit:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Blauwdruktoewijzingen verwijderen

Wanneer het tijd is voor een blauwdruktoewijzing, verwerkt de `Remove-AzBlueprintAssignment` cmdlet deze actie. De cmdlet heeft **naam** of **invoerobject** nodig om op te geven welke blauwdruktoewijzing moet worden verwijderd. **Een abonnement** is _vereist_ en moet in alle gevallen worden verstrekt.

In het volgende voorbeeld wordt `Get-AzBlueprintAssignment` een bestaande blauwdruktoewijzing opgehaald en `{subId}`vervolgens verwijderd uit het specifieke abonnement dat wordt weergegeven als:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Voorbeeld van end-to-end code

Als u alle stappen bij elkaar brengt, krijgt het volgende voorbeeld de blauwdrukdefinitie, maakt, `{subId}`wordt een blauwdruktoewijzing in het specifieke abonnement weergegeven als:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van de blauwdruk](../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../concepts/resource-locking.md)
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)