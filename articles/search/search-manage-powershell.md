---
title: PowerShell-scripts met de Az.Search-module
titleSuffix: Azure Cognitive Search
description: Maak en configureer een Azure Cognitive Search-service met PowerShell. U een service omhoog of omlaag schalen, api-sleutels voor beheerders en query's beheren en systeemgegevens opvragen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209293"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Uw Azure Cognitive Search-service beheren met PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

U PowerShell-cmdlets en -scripts uitvoeren op Windows, Linux of in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) om Azure Cognitive Search te maken en te configureren. De **az.search-module** breidt [Azure PowerShell](https://docs.microsoft.com/powershell/) met volledige pariteit uit tot de [API's voor zoekbeheer](https://docs.microsoft.com/rest/api/searchmanagement) en de mogelijkheid om de volgende taken uit te voeren:

> [!div class="checklist"]
> * [Zoekservices in een abonnement weergeven](#list-search-services)
> * [Servicegegevens retourneren](#get-search-service-information)
> * [Een service maken of verwijderen](#create-or-delete-a-service)
> * [API-sleutels voor beheerders regenereren](#regenerate-admin-keys)
> * [Queryapi-toetsen maken of verwijderen](#create-or-delete-query-keys)
> * [Omhoog of omlaag schalen met replica's en partities](#scale-replicas-and-partitions)

Af en toe worden er vragen gesteld over taken *die niet* op de bovenstaande lijst staan. Momenteel u de **az.search-module** of de API voor beheerREST niet gebruiken om een servernaam, regio of laag te wijzigen. Speciale resources worden toegewezen wanneer een service wordt gemaakt. Als zodanig vereist het wijzigen van de onderliggende hardware (locatie of knooppunttype) een nieuwe service. Op dezelfde manier zijn er geen tools of API's voor het overbrengen van inhoud, zoals een index, van de ene service naar de andere.

Binnen een service is het maken en beheren van inhoud via [Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/) of [.NET SDK.](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) Hoewel er geen speciale PowerShell-opdrachten voor inhoud zijn, u PowerShell-script schrijven dat REST- of .NET-API's aanroept om indexen te maken en te laden.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Versies controleren en modules laden

De voorbeelden in dit artikel zijn interactief en vereisen verhoogde machtigingen. Azure PowerShell (de **Az-module)** moet worden geïnstalleerd. Zie [Azure PowerShell installeren](/powershell/azure/overview)voor meer informatie .

### <a name="powershell-version-check-51-or-later"></a>PowerShell-versiecontrole (5.1 of hoger)

Lokale PowerShell moet 5.1 of hoger zijn, op elk ondersteund besturingssysteem.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure PowerShell laden

Als u niet zeker weet of **Az** is geïnstalleerd, voert u de volgende opdracht uit als verificatiestap. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Sommige systemen laden geen modules automatisch. Als er een fout optreedt bij de vorige opdracht, probeert u de module te laden en als dat niet lukt, gaat u terug naar de installatie-instructies om te zien of u een stap hebt gemist.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Verbinding maken met Azure met een aanmeldingstoken voor de browser

U uw aanmeldingsreferenties voor portals gebruiken om verbinding te maken met een abonnement in PowerShell. U ook [niet-interactief verifiëren met een serviceprincipal.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

```azurepowershell-interactive
Connect-AzAccount
```

Als u meerdere Azure-abonnementen hebt, stelt u uw Azure-abonnement in. Voer deze opdracht uit om een lijst met huidige abonnementen te bekijken.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Voer de volgende opdracht uit om het abonnement op te geven. In het volgende voorbeeld is `ContosoSubscription`de naam van het abonnement .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Services in een abonnement aanbieden

De volgende opdrachten zijn afkomstig van [**Az.Resources,**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)waarbij informatie wordt teruggegeven over bestaande bronnen en services die al in uw abonnement zijn ingericht. Als u niet weet hoeveel zoekservices al zijn gemaakt, geven deze opdrachten die informatie terug, zodat u een reis naar de portal opslaan.

De eerste opdracht retourneert alle zoekservices.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Geef in de lijst met services informatie over een specifieke bron terug.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Az.Search importeren

Opdrachten van [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) zijn pas beschikbaar nadat u de module hebt geladen.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Alle opdrachten voor Az.Search weergeven

Als verificatiestap retourneert u een lijst met opdrachten in de module.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Informatie over de zoekservice

Nadat **Az.Search** is geïmporteerd en u de resourcegroep kent die uw zoekservice bevat, voert u [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) uit om de servicedefinitie terug te sturen, inclusief naam, regio, laag en replica- en partitietellingen.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Een service maken of verwijderen

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) wordt gebruikt om [een nieuwe zoekservice](search-create-service-portal.md)te maken.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Beheerderssleutels regenereren

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) wordt gebruikt om over admin [API-sleutels](search-security-api-keys.md)te rollen. Bij elke service worden twee beheerderssleutels gemaakt voor geverifieerde toegang. Sleutels zijn vereist op elk verzoek. Beide beheerderssleutels zijn functioneel gelijkwaardig en verlenen volledige schrijftoegang tot een zoekservice met de mogelijkheid om alle informatie op te halen of om een object te maken en te verwijderen. Er bestaan twee toetsen, zodat u de ene gebruiken terwijl u de andere vervangt. 

U slechts één voor één regenereren, opgegeven als de `primary` toets of `secondary` sleutel. Voor een ononderbroken service moet u alle clientcode bijwerken om een secundaire sleutel te gebruiken terwijl u de primaire sleutel overrolt. Vermijd het veranderen van de sleutels tijdens de vlucht.

Zoals u zou verwachten, zullen aanvragen met behulp van de oude sleutel mislukken als u sleutels regenereert zonder clientcode bij te werken. Het regenereren van alle nieuwe sleutels sluit u niet permanent uit uw service en u hebt nog steeds toegang tot de service via de portal. Nadat u primaire en secundaire sleutels hebt geregenereren, u de clientcode bijwerken om de nieuwe sleutels te gebruiken en worden de bewerkingen dienovereenkomstig hervat.

Waarden voor de API-sleutels worden gegenereerd door de service. U geen aangepaste sleutel voor Azure Cognitive Search leveren om te gebruiken. Op dezelfde manier is er geen door de gebruiker gedefinieerde naam voor api-sleutels voor beheerders. Verwijzingen naar de sleutel zijn `primary` vaste `secondary`tekenreeksen, of . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer. Beide sleutels worden geretourneerd, ook al wijzigt u er slechts één tegelijk.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Querytoetsen maken of verwijderen

[**Nieuw-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) wordt gebruikt om [query-API-sleutels](search-security-api-keys.md) te maken voor alleen-lezen toegang van client-apps tot een Azure Cognitive Search-index. Querysleutels worden gebruikt om te verifiëren aan een specifieke index met het oog op het ophalen van zoekresultaten. Querysleutels verlenen alleen-lezen geen toegang tot andere items in de service, zoals een index, gegevensbron of indexer.

U geen sleutel verstrekken voor Azure Cognitive Search om te gebruiken. API-sleutels worden gegenereerd door de service.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Replica's en partities schalen

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) wordt gebruikt om [replica's en partities](search-capacity-planning.md) te verhogen of te verkleinen om de gefactureerde bronnen binnen uw service aan te passen. Het verhogen van replica's of partities draagt bij aan uw factuur, die zowel vaste als variabele kosten heeft. Als u tijdelijk extra verwerkingskracht nodig hebt, u replica's en partities verhogen om de werkbelasting te verwerken. Het controlegebied op de portalpagina Overzicht bevat tegels op querylatentie, query's per seconde en beperking, die aangeven of de huidige capaciteit toereikend is.

Het kan even duren voordat resourcing is toegevoegd of verwijderd. Op de achtergrond vinden aanpassingen van de capaciteit plaats, waardoor bestaande workloads kunnen worden voortgezet. Extra capaciteit wordt gebruikt voor binnenkomende aanvragen zodra deze klaar is, zonder dat extra configuratie vereist is. 

Het verwijderen van capaciteit kan storend zijn. Het stoppen van alle indexerings- en indexertaken voorafgaand aan het verminderen van de capaciteit wordt aanbevolen om gevallen aanvragen te voorkomen. Als dat niet haalbaar is, u overwegen de capaciteit stapsgewijs te verminderen, één replica en partitie tegelijk, totdat uw nieuwe doelniveaus zijn bereikt.

Zodra u de opdracht indient, is er geen manier om het halverwege te beëindigen. U moet wachten tot de opdracht is voltooid voordat u de tellingen herziet.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Resultaten moeten er vergelijkbaar uitzien met de volgende uitvoer.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Volgende stappen

Een [index](search-what-is-an-index.md)maken, [een index opvragen](search-query-overview.md) met behulp van de portal, REST API's of de .NET SDK.

* [Een Azure Cognitive Search-index maken in de Azure-portal](search-create-index-portal.md)
* [Een indexer instellen om gegevens van andere services te laden](search-indexer-overview.md)
* [Een Azure Cognitive Search-index opvragen met Zoekverkenner in de Azure-portal](search-explorer.md)
* [Azure Cognitive Search gebruiken in .NET](search-howto-dotnet-sdk.md)