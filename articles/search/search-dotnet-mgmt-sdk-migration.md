---
title: Upgraden naar de Azure Search .NET Management SDK-versie 2
titleSuffix: Azure Cognitive Search
description: Upgrade naar de Azure Search .NET Management SDK versie 2 van eerdere versies. Meer informatie over wat er nieuw is en welke codewijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847542"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Versies van de Azure Search .NET Management SDK upgraden

> [!Important]
> Deze inhoud is nog in aanbouw. Versie 3.0 van het Azure Search Management .NET SDK is beschikbaar op NuGet. We werken aan het bijwerken van deze migratiegids om uit te leggen hoe u upgraden naar de nieuwe versie. 
>

Als u versie 1.0.2 of ouder van de [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)gebruikt, helpt dit artikel u uw toepassing te upgraden naar versie 2.

Versie 2 van de Azure Search .NET Management SDK bevat enkele wijzigingen ten opzichte van eerdere versies. Deze zijn meestal klein, dus het wijzigen van uw code moet slechts minimale inspanning vereisen. Zie [Stappen om te upgraden](#UpgradeSteps) voor instructies over het wijzigen van uw code om de nieuwe SDK-versie te gebruiken.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Nieuwe versie 2
Versie 2 van de Azure Search .NET Management SDK is gericht op dezelfde algemeen beschikbare versie van de Azure Search Management REST API als eerdere SDK-versies, met name 2015-08-19. De wijzigingen in de SDK zijn strikt client-side veranderingen om de bruikbaarheid van de SDK zelf te verbeteren. Deze wijzigingen omvatten de volgende gegevens:

* `Services.CreateOrUpdate`en de asynchrone versies nu `SearchService` automatisch poll de provisioning en niet terug te keren totdat de service provisioning is voltooid. Dit bespaart u van het hebben van een dergelijke polling code zelf te schrijven.
* Als u servicevoorzieningen nog steeds handmatig wilt peilen, u de nieuwe `Services.BeginCreateOrUpdate` methode of een van de asynchrone versies gebruiken.
* Nieuwe methoden `Services.Update` en de asynchrone versies zijn toegevoegd aan de SDK. Deze methoden gebruiken HTTP PATCH om incrementele updates van een service te ondersteunen. U nu bijvoorbeeld een service `SearchService` schalen door een instantie door `partitionCount` te `replicaCount` geven aan deze methoden die alleen de gewenste en eigenschappen bevatten. De oude manier `Services.Get`van bellen, `SearchService`het wijzigen `Services.CreateOrUpdate` van de geretourneerde , en het doorgeven aan wordt nog steeds ondersteund, maar is niet langer nodig. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen om te upgraden
Werk eerst uw NuGet-referentie bij voor `Microsoft.Azure.Management.Search` het gebruik van de NuGet Package Manager Console of door met de rechtermuisknop op uw projectreferenties te klikken en "NuGet-pakketten beheren..." te selecteren. in Visual Studio.

Zodra NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u uw project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat zo is, ben je klaar om te gaan!

Als uw build mislukt, kan dit zijn omdat u een aantal van de SDK-interfaces hebt geïmplementeerd (bijvoorbeeld voor het testen van eenheden), die zijn gewijzigd. Om dit op te lossen, moet u de `BeginCreateOrUpdateWithHttpMessagesAsync`nieuwe methoden implementeren, zoals .

Zodra u buildfouten hebt opgelost, u wijzigingen aanbrengen in uw toepassing om te profiteren van nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 2](#WhatsNew).

## <a name="next-steps"></a>Volgende stappen
Wij zijn blij met uw feedback op de SDK. Als je problemen ondervindt, plaats dan je vragen op [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest) Als u een bug vindt, u een probleem indienen in de [Azure .NET SDK GitHub-repository.](https://github.com/Azure/azure-sdk-for-net/issues) Zorg ervoor dat u de titel van uw probleem labelt met '[zoeken]'.
