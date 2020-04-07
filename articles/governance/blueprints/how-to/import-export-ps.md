---
title: Blauwdrukken importeren en exporteren met PowerShell
description: Meer informatie over het werken met uw blauwdrukdefinities als code. Deel, bronbeheer en beheer ze met behulp van de opdrachten exporteren en importeren.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: 98bd21aad944346a17d8bdce7fb74c0eb8be2ed7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677142"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Blauwdrukdefinities importeren en exporteren met PowerShell

Azure Blueprints kunnen volledig worden beheerd via Azure portal. Naarmate organisaties verder gaan in hun gebruik van Azure Blueprints, moeten ze beginnen met het denken van blauwdrukdefinities als beheerde code. Dit concept wordt vaak aangeduid als Infrastructuur als Code (IaC). Het behandelen van uw blauwdrukdefinities als code biedt extra voordelen die verder gaan dan wat Azure-portal biedt. Deze voordelen zijn onder andere:

- Blauwdrukdefinities delen
- Back-ups maken van uw blauwdrukdefinities
- Blauwdrukdefinities opnieuw gebruiken in verschillende tenants of abonnementen
- De blauwdrukdefinities plaatsen in bronbeheer
  - Geautomatiseerd testen van blauwdrukdefinities in testomgevingen
  - Ondersteuning van continue integratie en continue implementatie (CI/CD) pijplijnen

Wat uw redenen ook zijn, het beheren van uw blauwdrukdefinities als code heeft voordelen. In dit artikel ziet `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` u hoe u de opdrachten en opdrachten gebruiken in de [az.blueprint-module.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt uitgegaan van een matige werkkennis van Azure Blueprints. Als u dit nog niet hebt gedaan, u de volgende artikelen doornemen:

- [Een blauwdruk maken in de portal](../create-blueprint-portal.md)
- Lees meer over [implementatiefasen](../concepts/deployment-stages.md) en [de levenscyclus van de blauwdruk](../concepts/lifecycle.md)
- [Blueprintdefinities](../create-blueprint-powershell.md) en -toewijzingen maken en [beheren](./manage-assignments-ps.md) met PowerShell

Als deze nog niet is geïnstalleerd, volgt u de instructies in [De module Az.Blueprint toevoegen](./manage-assignments-ps.md#add-the-azblueprint-module) om de **Az.Blueprint-module** uit de PowerShell-galerie te installeren en te valideren.

## <a name="folder-structure-of-a-blueprint-definition"></a>Mapstructuur van een blauwdrukdefinitie

Voordat we kijken naar het exporteren en importeren van blauwdrukken, laten we eens kijken naar hoe de bestanden die deel uitmaken van de blauwdrukdefinitie zijn gestructureerd. Een blauwdrukdefinitie moet worden opgeslagen in een eigen map.

> [!IMPORTANT]
> Als er geen waarde wordt doorgegeven aan de parameter **Naam** van de `Import-AzBlueprintWithArtifact` cmdlet, wordt de naam van de map waarin de blauwdrukdefinitie is opgeslagen, gebruikt.

Samen met de blauwdruk definitie, `blueprint.json`die moet worden genoemd , zijn de artefacten die de blauwdruk definitie is samengesteld uit. Elk artefact moet zich in `artifacts`de submap met de naam bevinden.
Samen moet de structuur van uw blauwdrukdefinitie als JSON-bestanden in mappen er als volgt uitzien:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Uw blauwdrukdefinitie exporteren

De stappen voor het exporteren van uw blauwdrukdefinitie zijn eenvoudig. Het exporteren van de blauwdrukdefinitie kan handig zijn voor het delen, back-ups of het plaatsen in bronbeheer.

- **Blauwdruk** [vereist]
  - Hiermee geeft u de blauwdrukdefinitie op
  - Gebruiken `Get-AzBlueprint` om het referentieobject op te halen
- **OutputPath** [vereist]
  - Hiermee geeft u het pad op waarop de JSON-bestanden met blauwdrukdefinitie moeten worden opgeslagen
  - De uitvoerbestanden bevinden zich in een submap met de naam van de blauwdrukdefinitie
- **Versie** (optioneel)
  - Hiermee geeft u de versie op die moet worden uitgevoerd als het verwijzingsobject **Blueprint** verwijzingen bevat naar meer dan één versie.

1. Hier krijg je een verwijzing naar de blauwdrukdefinitie die moet worden geëxporteerd vanuit het abonnement dat wordt weergegeven als: `{subId}`

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Gebruik `Export-AzBlueprintWithArtifact` de cmdlet om de opgegeven blauwdrukdefinitie te exporteren:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Uw blauwdrukdefinitie importeren

Zodra u een [geëxporteerde blauwdrukdefinitie](#export-your-blueprint-definition) hebt of een handmatig gemaakte blauwdrukdefinitie hebt in de [vereiste mapstructuur,](#folder-structure-of-a-blueprint-definition)u die blauwdrukdefinitie importeren in een andere beheergroep of abonnement.

Zie de [Azure Blueprint GitHub repo](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins)voor voorbeelden van ingebouwde blauwdrukdefinities.

- **Naam** [vereist]
  - Hiermee geeft u de naam op voor de nieuwe blauwdrukdefinitie
- **InputPath** [vereist]
  - Hiermee geeft u het pad op waaruit de blauwdrukdefinitie moet worden gemaakt
  - Moet overeenkomen met de [vereiste mapstructuur](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (optioneel)
  - De beheergroep-id om de blauwdrukdefinitie op te slaan als de huidige contextstandaard niet
  - **ManagementGroupId** of **SubscriptionId** moeten zijn opgegeven
- **AbonnementId** (optioneel)
  - De abonnements-ID om de blauwdrukdefinitie op te slaan als de huidige contextstandaard niet
  - **ManagementGroupId** of **SubscriptionId** moeten zijn opgegeven

1. Gebruik `Import-AzBlueprintWithArtifact` de cmdlet om de opgegeven blauwdrukdefinitie te importeren:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Zodra de blauwdrukdefinitie is geïmporteerd, [wijst u deze toe met PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Zie de volgende artikelen voor informatie over het maken van geavanceerde blauwdrukdefinities:

- Gebruik [statische en dynamische parameters](../concepts/parameters.md).
- De [volgorde voor blauwdruksequencing](../concepts/sequencing-order.md)aanpassen .
- Bebescherm implementaties met [het vergrendelen van blauwdrukbronnen.](../concepts/resource-locking.md)
- [Blauwdrukken beheren als code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [levenscyclus van de blauwdruk](../concepts/lifecycle.md).
- Begrijpen hoe [statische en dynamische parameters](../concepts/parameters.md)te gebruiken.
- Leer de volgorde van de [blauwdrukvolgorde](../concepts/sequencing-order.md)aan te passen.
- Ontdek hoe u gebruik maken van het vergrendelen van [blauwdrukbronnen.](../concepts/resource-locking.md)
- Los problemen op tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing.](../troubleshoot/general.md)