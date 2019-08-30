---
title: Een upgrade uitvoeren naar de Azure Search .NET Management SDK versie 2-Azure Search
description: Voer een upgrade uit naar de Azure Search .NET Management SDK versie 2 van eerdere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
author: brjohnstmsft
manager: nitinme
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 2a59cff7f5313b0ac5a060d698950a4c82160f67
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182247"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version"></a>Een upgrade uitvoeren naar de versie van Azure Search .NET Management SDK 

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
* Nieuwe methoden `Services.Update` en de bijbehorende asynchrone versies zijn toegevoegd aan de SDK. Deze methoden gebruiken HTTP-PATCH ter ondersteuning van incrementele update van een service. Zo kunt u een service nu schalen door een `SearchService` exemplaar door te geven aan deze methoden die alleen de gewenste `partitionCount` eigenschappen en `replicaCount` bevatten. De oude manier van aanroepen `Services.Get`, wijzigen van de geretourneerde `SearchService`en door `Services.CreateOrUpdate` geven aan wordt nog steeds ondersteund, maar is niet langer nodig. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
Werk eerst uw NuGet-verwijzing bij `Microsoft.Azure.Management.Search` voor het gebruik van de NuGet Package Manager-console of door met de rechter muisknop te klikken op uw project verwijzingen en vervolgens NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build mislukt, is het mogelijk dat u een aantal van de SDK-interfaces (bijvoorbeeld voor het testen van eenheden) hebt geïmplementeerd, die zijn gewijzigd. Als u dit wilt oplossen, moet u de nieuwe methoden implementeren, zoals `BeginCreateOrUpdateWithHttpMessagesAsync`.

Wanneer u opgebouwde fouten hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 2](#WhatsNew).

## <a name="conclusion"></a>Conclusie
We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons om hulp vragen over het [MSDN-forum van Azure Search](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Search] '.

Hartelijk dank dat u Azure Search hebt gebruikt.
