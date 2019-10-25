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
ms.openlocfilehash: b81060bc3d8409a9ccb74ba85c85e6f779650ffd
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792422"
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

* `Services.CreateOrUpdate` en de bijbehorende asynchrone versies worden de inrichtings `SearchService` nu automatisch gecontroleerd en worden niet geretourneerd totdat het inrichten van de service is voltooid. Zo hoeft u niet zelf een dergelijke polling code te schrijven.
* Als u nog steeds hand matig Service Provisioning wilt pollen, kunt u de nieuwe `Services.BeginCreateOrUpdate` methode of een van de bijbehorende asynchrone versies gebruiken.
* Nieuwe methoden `Services.Update` en de bijbehorende asynchrone versies zijn toegevoegd aan de SDK. Deze methoden gebruiken HTTP-PATCH ter ondersteuning van incrementele update van een service. Zo kunt u een service nu schalen door een `SearchService`-exemplaar door te geven aan deze methoden die alleen de gewenste `partitionCount` en `replicaCount` eigenschappen bevatten. De oude manier om `Services.Get`aan te roepen, de geretourneerde `SearchService`aan te passen en deze aan `Services.CreateOrUpdate` door te geven, wordt nog steeds ondersteund, maar is niet langer nodig. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
U moet eerst uw NuGet-referentie voor `Microsoft.Azure.Management.Search` bijwerken met behulp van de NuGet Package Manager-console of door met de rechter muisknop op uw project verwijzingen te klikken en NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build mislukt, is het mogelijk dat u een aantal van de SDK-interfaces (bijvoorbeeld voor het testen van eenheden) hebt geïmplementeerd, die zijn gewijzigd. Als u dit wilt oplossen, moet u de nieuwe methoden, zoals `BeginCreateOrUpdateWithHttpMessagesAsync`, implementeren.

Wanneer u opgebouwde fouten hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 2](#WhatsNew).

## <a name="conclusion"></a>Conclusie
We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons om hulp vragen over het [MSDN-forum van Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Search] '.

Hartelijk dank dat u Azure Search hebt gebruikt.
