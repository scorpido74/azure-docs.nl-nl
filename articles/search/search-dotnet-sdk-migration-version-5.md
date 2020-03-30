---
title: Upgraden naar Azure Search .NET SDK versie 5
titleSuffix: Azure Cognitive Search
description: Migreer code naar de Azure Search .NET SDK versie 5 van oudere versies. Meer informatie over wat nieuw is en welke codewijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793023"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Upgraden naar Azure Search .NET SDK versie 5

Als u versie 4.0-preview of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u uw toepassing te upgraden naar versie 5.

Zie [Azure Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor een meer algemene walkthrough van de SDK met voorbeelden.

Versie 5 van de Azure Search .NET SDK bevat enkele wijzigingen ten opzichte van eerdere versies. Deze zijn meestal klein, dus het wijzigen van uw code moet slechts minimale inspanning vereisen. Zie [Stappen om te upgraden](#UpgradeSteps) voor instructies over het wijzigen van uw code om de nieuwe SDK-versie te gebruiken.

> [!NOTE]
> Als u versie 2.0-preview of ouder gebruikt, moet u eerst upgraden naar versie 3 en vervolgens upgraden naar versie 5. Zie [Upgraden naar de Azure Search .NET SDK versie 3](search-dotnet-sdk-migration.md) voor instructies.
>
> Uw Azure Search-serviceinstantie ondersteunt verschillende REST API-versies, waaronder de nieuwste. U een versie blijven gebruiken wanneer deze niet langer de laatste is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u de API-versie in elke aanvraag opgeven via de parameter api-versie. Bij gebruik van de .NET SDK bepaalt de versie van de SDK die u gebruikt de overeenkomstige versie van de REST API. Als u een oudere SDK gebruikt, u die code zonder wijzigingen blijven uitvoeren, zelfs als de service is geüpgraded om een nieuwere API-versie te ondersteunen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Nieuw in versie 5
Versie 5 van de Azure Search .NET SDK is gericht op de nieuwste algemeen beschikbare versie van de Azure Search REST API, met name 2017-11-11. Dit maakt het mogelijk om nieuwe functies van Azure Search te gebruiken vanuit een .NET-toepassing, waaronder het volgende:

* [Synoniemen](search-synonyms.md).
* U nu programmatisch toegang krijgen tot waarschuwingen `Warning` in `IndexerExecutionResult` de uitvoeringsgeschiedenis van de indexer (zie de eigenschap in de [.NET-verwijzing](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) voor meer details).
* Ondersteuning voor .NET Core 2.
* Nieuwe pakketstructuur ondersteunt het gebruik van alleen de onderdelen van de SDK die u nodig hebt (zie [Wijzigingen in versie 5 verbreken](#ListOfChanges) voor meer informatie).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen om te upgraden
Werk eerst uw NuGet-referentie bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager Console of door met de rechtermuisknop op uw projectreferenties te klikken en "NuGet-pakketten beheren..." te selecteren. in Visual Studio.

Zodra NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u uw project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat zo is, ben je klaar om te gaan!

Als uw build mislukt, ziet u een buildfout zoals:

    The name 'SuggesterSearchMode' does not exist in the current context

De volgende stap is het oplossen van deze buildfout. Zie [Wijzigingen in versie 5 verbreken](#ListOfChanges) voor meer informatie over de oorzaak van de fout en hoe u deze oplossen.

Houd er rekening mee dat als gevolg van wijzigingen in de verpakking van de Azure Search .NET SDK, u uw toepassing opnieuw moet opbouwen om versie 5 te kunnen gebruiken. Deze wijzigingen worden beschreven in [Het breken van wijzigingen in versie 5](#ListOfChanges).

Mogelijk ziet u aanvullende buildwaarschuwingen met betrekking tot verouderde methoden of eigenschappen. De waarschuwingen bevatten instructies over wat te gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` de methode gebruikt, moet u een waarschuwing krijgen met de tekst 'Dit gedrag is nu standaard ingeschakeld, dus het aanroepen van deze methode is niet langer nodig.'

Zodra u buildfouten of waarschuwingen hebt opgelost, u wijzigingen aanbrengen in uw toepassing om te profiteren van nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Wijzigingen in versie 5 verbreken

### <a name="new-package-structure"></a>Nieuwe pakketstructuur

De meest substantiële verandering in versie `Microsoft.Azure.Search` 5 is dat de assemblage en de inhoud ervan zijn verdeeld in vier afzonderlijke samenstellingen die nu worden gedistribueerd als vier afzonderlijke NuGet-pakketten:

 - `Microsoft.Azure.Search`: Dit is een meta-pakket dat alle andere Azure Search-pakketten als afhankelijkheden bevat. Als u een upgrade uitvoert van een eerdere versie van de SDK, moet het upgraden van dit pakket en het opnieuw bouwen voldoende zijn om de nieuwe versie te gebruiken.
 - `Microsoft.Azure.Search.Data`: Gebruik dit pakket als u een .NET-toepassing ontwikkelt met Azure Search en u hoeft alleen documenten in uw indexen op te vragen of bij te werken. Als u ook indexen, synoniemkaarten of andere bronnen op serviceniveau `Microsoft.Azure.Search` moet maken of bijwerken, gebruikt u het pakket in plaats daarvan.
 - `Microsoft.Azure.Search.Service`: Gebruik dit pakket als u automatisering ontwikkelt in .NET om Azure Search-indexen, synoniemkaarten, indexers, gegevensbronnen of andere bronnen op serviceniveau te beheren. Als u alleen documenten in uw indexen hoeft `Microsoft.Azure.Search.Data` op te vragen of bij te werken, gebruikt u het pakket in plaats daarvan. Als u alle functionaliteit van Azure Search `Microsoft.Azure.Search` nodig hebt, gebruikt u het pakket in plaats daarvan.
 - `Microsoft.Azure.Search.Common`: Veelvoorkomende typen die nodig zijn voor de Azure Search .NET-bibliotheken. U hoeft dit pakket niet rechtstreeks in uw toepassing te gebruiken; Het is alleen bedoeld om te worden gebruikt als een afhankelijkheid.
 
Deze verandering is technisch breken, omdat veel soorten werden verplaatst tussen de samenstellingen. Dit is de reden waarom het opnieuw opbouwen van uw toepassing noodzakelijk is om te upgraden naar versie 5 van de SDK.

Er is een klein aantal andere wijzigingen in versie 5 waarvoor mogelijk ook codewijzigingen nodig zijn naast het opnieuw opbouwen van uw toepassing.

### <a name="change-to-suggesters"></a>Wijzigen in suggesties 

De `Suggester` constructeur heeft `enum` geen `SuggesterSearchMode`parameter meer voor . Dit enum had slechts één waarde en was daarom overbodig. Als u buildfouten als gevolg hiervan ziet, verwijdert `SuggesterSearchMode` u verwijzingen naar de parameter.

### <a name="removed-obsolete-members"></a>Verouderde leden verwijderd

Mogelijk ziet u buildfouten met betrekking tot methoden of eigenschappen die in eerdere versies als verouderd zijn gemarkeerd en vervolgens zijn verwijderd in versie 5. Als u dergelijke fouten tegenkomt, u deze als geen andere manier oplossen:

- Als u de `IndexingParametersExtensions.IndexStorageMetadataOnly` methode `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` gebruikt, gebruikt u in plaats daarvan.
- Als u de `IndexingParametersExtensions.SkipContent` methode `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` gebruikt, gebruikt u in plaats daarvan.

### <a name="removed-preview-features"></a>Verwijderde voorbeeldfuncties

Als u een upgrade uitvoert van versie 4.0-preview naar versie 5, moet u er rekening mee houden dat json-array- en CSV-parsing-ondersteuning voor Blob Indexers is verwijderd omdat deze functies nog steeds in preview zijn. In het bijzonder zijn `IndexingParametersExtensions` de volgende methoden van de klasse verwijderd:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Als uw toepassing een harde afhankelijkheid van deze functies heeft, u niet upgraden naar versie 5 van de Azure Search .NET SDK. U versie 4.0-preview blijven gebruiken. Houd er echter rekening mee dat **we het gebruik van preview-SDK's in productietoepassingen niet aanraden.** Preview-functies zijn alleen voor evaluatie en kunnen veranderen.

## <a name="conclusion"></a>Conclusie
Zie de [.NET How-to](search-howto-dotnet-sdk.md).

Wij zijn blij met uw feedback op de SDK. Als u problemen ondervindt, u ons gerust om hulp vragen op [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Als u een bug vindt, u een probleem indienen in de [Azure .NET SDK GitHub-repository.](https://github.com/Azure/azure-sdk-for-net/issues) Zorg ervoor dat u de uitgiftetitel vooraf opmaakt met '[Azure Search]'.

Bedankt voor het gebruik van Azure Search!
