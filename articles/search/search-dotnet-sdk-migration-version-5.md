---
title: Een upgrade uitvoeren naar Azure Search .NET SDK versie 5
titleSuffix: Azure Cognitive Search
description: Code migreren naar de Azure Search .NET SDK versie 5 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb0cd191ba7e5939c55d11b484ed7a2c422f8c6d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793023"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Een upgrade uitvoeren naar Azure Search .NET SDK versie 5

Als u versie 4,0-Preview of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u bij het upgraden van uw toepassing tot het gebruik van versie 5.

Zie [Azure Search van een .NET-toepassing gebruiken](search-howto-dotnet-sdk.md)voor een meer algemeen overzicht van de SDK, inclusief voor beelden.

Versie 5 van de Azure Search .NET SDK bevat enkele wijzigingen ten opzichte van eerdere versies. Dit zijn voornamelijk minder jarigen, zodat het wijzigen van uw code slechts een minimale inspanning vereist. Zie de stappen voor het uitvoeren van een [upgrade](#UpgradeSteps) voor instructies over het wijzigen van de code voor het gebruik van de nieuwe SDK-versie.

> [!NOTE]
> Als u versie 2,0-Preview of ouder gebruikt, moet u eerst een upgrade uitvoeren naar versie 3 en vervolgens een upgrade uitvoeren naar versie 5. Zie [upgraden naar de Azure Search .NET SDK versie 3](search-dotnet-sdk-migration.md) voor instructies.
>
> Uw Azure Search service-exemplaar ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Wat is er nieuw in versie 5
Versie 5 van de Azure Search .NET SDK streeft naar de meest recente, algemeen beschik bare versie van de Azure Search REST API, met name 2017-11-11. Dit maakt het mogelijk om nieuwe functies van Azure Search van een .NET-toepassing te gebruiken, met inbegrip van het volgende:

* [Synoniemen](search-synonyms.md).
* U kunt nu programmatisch toegang krijgen tot waarschuwingen in de indexerings geschiedenis (Zie de eigenschap `Warning` van `IndexerExecutionResult` in de [.net-referentie](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet) voor meer informatie).
* Ondersteuning voor .NET Core 2.
* De nieuwe pakket structuur biedt ondersteuning voor het gebruik van alleen de onderdelen van de SDK die u nodig hebt (Zie voor meer informatie [wijzigingen in versie 5 afbreken](#ListOfChanges) ).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade
U moet eerst uw NuGet-referentie voor `Microsoft.Azure.Search` bijwerken met behulp van de NuGet Package Manager-console of door met de rechter muisknop op uw project verwijzingen te klikken en NuGet-pakketten beheren te selecteren... in Visual Studio.

Nadat NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u het project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat het geval is, bent u klaar om aan de slag te gaan.

Als uw build mislukt, ziet u een build-fout als de volgende:

    The name 'SuggesterSearchMode' does not exist in the current context

De volgende stap is het oplossen van deze build-fout. Zie belang rijke [wijzigingen in versie 5](#ListOfChanges) voor meer informatie over de oorzaak van de fout en hoe u deze kunt oplossen.

Als gevolg van wijzigingen in de verpakking van de Azure Search .NET SDK, moet u uw toepassing opnieuw samen stellen om versie 5 te kunnen gebruiken. Deze wijzigingen worden gedetailleerd beschreven in de belang rijke [wijzigingen in versie 5](#ListOfChanges).

Mogelijk worden er aanvullende build-waarschuwingen met betrekking tot verouderde methoden of eigenschappen weer gegeven. De waarschuwingen bevatten instructies over wat u moet gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld gebruikmaakt van de methode `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType`, krijgt u een waarschuwing met de tekst ' dit gedrag is nu standaard ingeschakeld, zodat deze methode niet langer nodig is. '

Wanneer u eventuele build-fouten of-waarschuwingen hebt opgelost, kunt u wijzigingen aanbrengen in uw toepassing om te profiteren van de nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 5](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Wijzigingen in versie 5 verbreken

### <a name="new-package-structure"></a>Nieuwe pakket structuur

De meest substantiële wijziging in versie 5 is dat de `Microsoft.Azure.Search`-assembly en de inhoud ervan zijn onderverdeeld in vier afzonderlijke assembly's die nu worden gedistribueerd als vier afzonderlijke NuGet-pakketten:

 - `Microsoft.Azure.Search`: dit is een meta pakket dat alle andere Azure Search pakketten als afhankelijkheden bevat. Als u een upgrade uitvoert van een eerdere versie van de SDK, moet u een upgrade uitvoeren voor het pakket en het opnieuw samen stellen van de nieuwe versie.
 - `Microsoft.Azure.Search.Data`: gebruik dit pakket als u een .NET-toepassing ontwikkelt met behulp van Azure Search en u alleen documenten in uw indexen hoeft op te vragen of bij te werken. Als u ook indexen, synoniemen of andere bronnen op service niveau wilt maken of bijwerken, gebruikt u in plaats daarvan het `Microsoft.Azure.Search`-pakket.
 - `Microsoft.Azure.Search.Service`: gebruik dit pakket als u Automation in .NET ontwikkelt voor het beheren van Azure Search indexen, synoniemen, Indexeer functies, gegevens bronnen of andere resources op service niveau. Als u alleen documenten in uw indexen hoeft op te vragen of bij te werken, gebruikt u in plaats daarvan het `Microsoft.Azure.Search.Data`-pakket. Als u de functionaliteit van Azure Search nodig hebt, gebruikt u het `Microsoft.Azure.Search` pakket in plaats daarvan.
 - `Microsoft.Azure.Search.Common`: algemene typen die nodig zijn voor de Azure Search .NET-bibliotheken. U hoeft dit pakket niet rechtstreeks in uw toepassing te gebruiken. Het is alleen bedoeld om te worden gebruikt als afhankelijkheid.
 
Deze wijziging is technisch afgebroken omdat er veel typen tussen assembly's zijn verplaatst. Daarom is het opnieuw samen stellen van uw toepassing nood zakelijk om een upgrade uit te kunnen zetten naar versie 5 van de SDK.

Er is een klein aantal andere belang rijke wijzigingen in versie 5 waarvoor code wijzigingen nodig zijn naast het opnieuw samen stellen van uw toepassing.

### <a name="change-to-suggesters"></a>Wijzigen in suggesties 

De `Suggester`-constructor heeft geen `enum`-para meter meer voor `SuggesterSearchMode`. Deze enum heeft slechts één waarde en is daarom overbodig. Als u fouten opbouwt als gevolg hiervan, verwijdert u verwijzingen naar de para meter `SuggesterSearchMode`.

### <a name="removed-obsolete-members"></a>Verouderde leden zijn verwijderd

Mogelijk worden er Build-fouten met betrekking tot methoden of eigenschappen weer geven die in eerdere versies als verouderd zijn gemarkeerd en vervolgens in versie 5 zijn verwijderd. Als u dergelijke fouten tegen komt, kunt u deze als volgt oplossen:

- Als u de `IndexingParametersExtensions.IndexStorageMetadataOnly` methode gebruikt, gebruikt u `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` in plaats daarvan.
- Als u de `IndexingParametersExtensions.SkipContent` methode gebruikt, gebruikt u `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` in plaats daarvan.

### <a name="removed-preview-features"></a>De preview-functies zijn verwijderd

Als u een upgrade uitvoert van versie 4,0-Preview naar versie 5, moet u er rekening mee houden dat JSON-matrix en de ondersteuning voor het parseren van CSV voor BLOB-Indexeer functies zijn verwijderd, omdat deze onderdelen nog in Preview zijn. Met name de volgende methoden van de `IndexingParametersExtensions` klasse zijn verwijderd:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Als uw toepassing een vaste afhankelijkheid van deze functies heeft, kunt u niet upgraden naar versie 5 van de Azure Search .NET SDK. U kunt versie 4,0-Preview blijven gebruiken. Bedenk echter dat het **gebruik van preview-sdk's in productie toepassingen niet wordt aangeraden**. Preview-functies zijn alleen voor evaluatie en kunnen worden gewijzigd.

## <a name="conclusion"></a>Conclusie
Als u meer informatie wilt over het gebruik van de Azure Search .NET SDK, raadpleegt u de [.net How-to](search-howto-dotnet-sdk.md).

We stellen uw feedback op de SDK. Als u problemen ondervindt, kunt u ons vragen om hulp over [stack overflow](https://stackoverflow.com/questions/tagged/azure-search). Als u een bug vindt, kunt u een probleem in de [Azure .NET SDK github-opslag plaats](https://github.com/Azure/azure-sdk-for-net/issues)opslaan. Zorg ervoor dat u de titel van het probleem voordoet met ' [Azure Search] '.

Hartelijk dank dat u Azure Search hebt gebruikt.
