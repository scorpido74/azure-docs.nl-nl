---
title: Upgrade uitvoeren naar de Azure Search .NET Management SDK versie 2
titleSuffix: Azure Cognitive Search
description: Voer een upgrade uit naar de Azure Search .NET Management SDK versie 2 van eerdere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73847542"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Versies van de Azure Search .NET Management SDK bijwerken

> [!Important]
> Deze inhoud is nog steeds te bouwen. Versie 3,0 van de Azure Search management .NET SDK is beschikbaar op NuGet. We werken aan het bijwerken van deze migratie handleiding om uit te leggen hoe u een upgrade naar de nieuwe versie uitvoert. 
>

Als u versie 1.0.2 of ouder van de [Azure Search .net Management SDK](https://aka.ms/search-mgmt-sdk)gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 2.

Versie 2 van de Azure Search .NET Management SDK bevat enkele wijzigingen ten opzichte van eerdere versies. Dit zijn voornamelijk minder jarigen, zodat het wijzigen van uw code slechts een minimale inspanning vereist. Zie de stappen voor het uitvoeren van een [upgrade](#UpgradeSteps) voor instructies over het wijzigen van de code voor het gebruik van de nieuwe SDK-versie.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>Wat is er nieuw in versie 2
Versie 2 van de Azure Search .NET Management SDK streeft naar dezelfde algemene beschik bare versie van de Azure Search-beheer REST API als vorige SDK-versies, met name 2015-08-19. De wijzigingen in de SDK zijn strikt aan de client zijde gewijzigd om de bruikbaarheid van de SDK zelf te verbeteren. Deze wijzigingen omvatten het volgende:

* `Services.CreateOrUpdate`en de bijbehorende asynchrone versies worden nu automatisch door gegeven `SearchService` aan de inrichting en worden niet teruggestuurd totdat het inrichten van de service is voltooid. Zo hoeft u niet zelf een dergelijke polling code te schrijven.
* Als u nog steeds hand matig Service Provisioning wilt pollen, kunt u `Services.BeginCreateOrUpdate` de nieuwe methode of een van de bijbehorende asynchrone versies gebruiken.
* Nieuwe methoden `Services.Update` en de bijbehorende asynchrone versies zijn toegevoegd aan de SDK. Deze methoden gebruiken HTTP-PATCH ter ondersteuning van incrementele update van een service. Zo kunt u een service nu schalen door een `SearchService` exemplaar door te geven aan deze methoden die alleen de gewenste `partitionCount` eigenschappen en `replicaCount` bevatten. De oude manier van aanroepen `Services.Get`, wijzigen van `SearchService`de geretourneerde en door `Services.CreateOrUpdate` geven aan wordt nog steeds ondersteund, maar is niet langer nodig. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
Werk eerst uw NuGet-verwijzing bij `Microsoft.Azure.Management.Search` voor het gebruik van de NuGet Package Manager-console of door met de rechter muisknop te klikken op uw project verwijzingen en vervolgens NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build mislukt, is het mogelijk dat u een aantal van de SDK-interfaces (bijvoorbeeld voor het testen van eenheden) hebt geïmplementeerd, die zijn gewijzigd. Als u dit wilt oplossen, moet u de nieuwe methoden implementeren, zoals `BeginCreateOrUpdateWithHttpMessagesAsync`.

Wanneer u opgebouwde fouten hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 2](#WhatsNew).

## <a name="next-steps"></a>Volgende stappen
We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u uw vragen op [stack overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest)plaatsen. Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem labelt met ' [Search] '.
