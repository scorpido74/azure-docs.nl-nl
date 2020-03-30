---
title: Een app klonen met PowerShell
description: Meer informatie over het klonen van uw App Service-app naar een nieuwe app met PowerShell. Er komen verschillende kloonscenario's aan bod, waaronder traffic manager-integratie.
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.topic: article
ms.date: 01/14/2016
ms.custom: seodec18
ms.openlocfilehash: e7ad45ea4cb1049ed7eeb454162e23e81ed35019
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255192"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>Azure App Service App klonen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de release van Microsoft Azure PowerShell-versie 1.1.0 `New-AzWebApp` is een nieuwe optie toegevoegd waarmee u een bestaande App Service-app klonen naar een nieuw gemaakte app in een andere regio of in dezelfde regio. Met deze optie kunnen klanten snel en eenvoudig een aantal apps in verschillende regio's implementeren.

App-klonen wordt ondersteund voor standaard-, premium-, premium-, serviceplannen voor apps en apps. De nieuwe functie gebruikt dezelfde beperkingen als de functie Back-up van App Service, zie [Een back-up maken van een app in Azure App Service](manage-backup.md).

## <a name="cloning-an-existing-app"></a>Een bestaande app klonen
Scenario: een bestaande app in de regio Zuid Centraal in de VS en u wilt de inhoud klonen naar een nieuwe app in de regio Noord Centraal in de VS. Dit kan worden bereikt door de Azure Resource Manager-versie van de PowerShell-cmdlet te gebruiken om een nieuwe app met de `-SourceWebApp` optie te maken.

Als u de naam van de brongroep kent die de bron-app bevat, u `source-webapp`de volgende PowerShell-opdracht gebruiken om de informatie van de bron-app te krijgen (in dit geval):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Als u een nieuw App-serviceplan wilt maken, u de opdracht gebruiken `New-AzAppServicePlan` zoals in het volgende voorbeeld

```powershell
New-AzAppServicePlan -Location "North Central US" -ResourceGroupName DestinationAzureResourceGroup -Name DestinationAppServicePlan -Tier Standard
```

Met `New-AzWebApp` de opdracht u de nieuwe app maken in de regio Noord Centraal in de VS en deze koppelen aan een bestaand App-serviceplan. Bovendien u dezelfde brongroep gebruiken als de bron-app of een nieuwe brongroep definiëren, zoals weergegeven in de volgende opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

Als u een bestaande app wilt klonen, inclusief `IncludeSourceWebAppSlots` alle bijbehorende implementatiesleuven, moet u de parameter gebruiken.  Houd er `IncludeSourceWebAppSlots` rekening mee dat de parameter alleen wordt ondersteund voor het klonen van een hele app, inclusief alle sleuven. Met de volgende PowerShell-opdracht wordt het `New-AzWebApp` gebruik van die parameter met de opdracht aangetoond:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

Als u een bestaande app binnen dezelfde regio wilt klonen, moet u een nieuwe brongroep en een nieuw app-serviceplan in dezelfde regio maken en vervolgens de volgende PowerShell-opdracht gebruiken om de app te klonen:

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcapp
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Een bestaande app vastmaken aan een app-serviceomgeving
Scenario: een bestaande app in de regio Zuid Centraal in de VS en u wilt de inhoud klonen naar een nieuwe app naar een bestaande App Service Environment (ASE).

Als u de naam van de brongroep kent die de bron-app bevat, u `source-webapp`de volgende PowerShell-opdracht gebruiken om de informatie van de bron-app te krijgen (in dit geval):

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

Als u de naam van de ASE en de naam van de brongroep kent waartoe de ASE behoort, u de nieuwe app maken in de bestaande ASE, zoals wordt weergegeven in de volgende opdracht:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

De `Location` parameter is vereist vanwege de reden van de erfenis, maar deze wordt genegeerd wanneer u de app in een ASE maakt. 

## <a name="cloning-an-existing-app-slot"></a>Een bestaande app-sleuf klonen
Scenario: U wilt een bestaande implementatiesleuf van een app klonen naar een nieuwe app of een nieuwe sleuf. De nieuwe app kan zich in dezelfde regio bevinden als de oorspronkelijke app-sleuf of in een andere regio.

Als u de naam van de brongroep kent die de bron-app bevat, u de `source-appslot`volgende `source-app`PowerShell-opdracht gebruiken om de informatie van de bron-app-sleuf (in dit geval ) gekoppeld te krijgen aan:

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

Met de volgende opdracht wordt het maken van een kloon van de bron-app naar een nieuwe app aangetoond:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>Traffic Manager configureren tijdens het klonen van een app
Het maken van apps met meerdere regio's en het configureren van Azure Traffic Manager om verkeer naar al deze apps te routeren, is een belangrijk scenario om ervoor te zorgen dat de apps van klanten in hoge mate beschikbaar zijn. Wanneer u een bestaande app kloont, hebt u de mogelijkheid om beide apps te verbinden met een nieuw profiel van de verkeersbeheerder of een bestaand profiel. Alleen de Azure Resource Manager-versie van Traffic Manager wordt ondersteund.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>Een nieuw Traffic Manager-profiel maken tijdens het klonen van een app
Scenario: U wilt een app naar een andere regio klonen, terwijl u een Azure Resource Manager-verkeersbeheerprofiel configureert dat beide apps bevat. Met de volgende opdracht wordt het maken van een kloon van de bron-app naar een nieuwe app weergegeven terwijl u een nieuw Traffic Manager-profiel configureert:

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>Nieuwe gekloonde app toevoegen aan een bestaand Traffic Manager-profiel
Scenario: U hebt al een Azure Resource Manager-verkeersbeheerprofiel en wilt beide apps toevoegen als eindpunten. Hiervoor moet u eerst de bestaande profiel-ID van de verkeersbeheerder samenstellen. U hebt de abonnements-ID, de naam van de resourcegroep en de bestaande profielnaam van de verkeersbeheerder nodig.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Nadat de verkeersbevracht-ID is gewijzigd, toont de volgende opdracht het maken van een kloon van de bron-app aan een nieuwe app terwijl deze wordt toegevoegd aan een bestaand Traffic Manager-profiel:

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>Huidige beperkingen
Hier zijn de bekende beperkingen van app klonen:

* Instellingen voor automatische schaal worden niet gekloond
* Instellingen voor back-upschema's worden niet gekloond
* VNET-instellingen worden niet gekloond
* App-inzichten worden niet automatisch ingesteld in de doel-app
* Eenvoudige Auth-instellingen zijn niet gekloond
* Kudu Extension worden niet gekloond
* TiP-regels worden niet gekloond
* Database-inhoud is niet gekloond
* Uitgaande IP-adressen worden gewijzigd als het klonen naar een andere schaaleenheid
* Niet beschikbaar voor Linux-apps

### <a name="references"></a>Verwijzingen
* [App Service Klonen](app-service-web-app-cloning.md)
* [Een back-up maken van een app in Azure App Service](manage-backup.md)
* [Azure Resource Manager-ondersteuning voor Azure Traffic Manager Preview](../traffic-manager/traffic-manager-powershell-arm.md)
* [Inleiding tot de App Service-omgeving](environment/intro.md)
* [Azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

