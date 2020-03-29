---
title: Upgraden naar Azure Search .NET SDK-versie 9
titleSuffix: Azure Cognitive Search
description: Migreer code naar de Azure Search .NET SDK versie 9 van oudere versies. Meer informatie over wat nieuw is en welke codewijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcc70267754f7e66f29dd1b855d3efb8b814e78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793007"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Upgraden naar Azure Search .NET SDK-versie 9

Als u versie 7.0-preview of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u uw toepassing te upgraden naar versie 9.

> [!NOTE]
> Als u versie 8.0-preview wilt gebruiken om functies te evalueren die nog niet algemeen beschikbaar zijn, u ook de instructies in dit artikel volgen om te upgraden naar 8.0-preview van eerdere versies.

Zie [Azure Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor een meer algemene walkthrough van de SDK met voorbeelden.

Versie 9 van de Azure Search .NET SDK bevat veel wijzigingen ten opzichte van eerdere versies. Sommige van deze zijn het breken van wijzigingen, maar ze moeten alleen relatief kleine wijzigingen in uw code vereisen. Zie [Stappen om te upgraden](#UpgradeSteps) voor instructies over het wijzigen van uw code om de nieuwe SDK-versie te gebruiken.

> [!NOTE]
> Als u versie 4.0-preview of ouder gebruikt, moet u eerst upgraden naar versie 5 en vervolgens upgraden naar versie 9. Zie [Upgraden naar de Azure Search .NET SDK-versie 5](search-dotnet-sdk-migration-version-5.md) voor instructies.
>
> Uw Azure Search-serviceinstantie ondersteunt verschillende REST API-versies, waaronder de nieuwste. U een versie blijven gebruiken wanneer deze niet langer de laatste is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u de API-versie in elke aanvraag opgeven via de parameter api-versie. Bij gebruik van de .NET SDK bepaalt de versie van de SDK die u gebruikt de overeenkomstige versie van de REST API. Als u een oudere SDK gebruikt, u die code zonder wijzigingen blijven uitvoeren, zelfs als de service is geüpgraded om een nieuwere API-versie te ondersteunen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Nieuw in versie 9
Versie 9 van de Azure Search .NET SDK is gericht op de nieuwste algemeen beschikbare versie van de Azure Search REST API, met name 2019-05-06. Dit maakt het mogelijk om nieuwe functies van Azure Search te gebruiken vanuit een .NET-toepassing, waaronder het volgende:

* [AI-verrijking](cognitive-search-concept-intro.md) is de mogelijkheid om tekst uit afbeeldingen, blobs en andere ongestructureerde gegevensbronnen te extraheren - de inhoud verrijken om deze beter doorzoekbaar te maken in een Azure Search-index.
* Met ondersteuning voor [complexe typen](search-howto-complex-data-types.md) u bijna elke geneste JSON-structuur modelleren in een Azure Search-index.
* [Automatisch aanvullen](search-autocomplete-tutorial.md) biedt een alternatief voor de **API Voorstellen** voor het implementeren van zoek-naar-je-type gedrag. Automatisch voltooien "voltooit" het woord of de woordgroep die een gebruiker momenteel typt.
* [JsonLines parsing mode](search-howto-index-json-blobs.md), onderdeel van Azure Blob indexing, maakt één zoekdocument per JSON entiteit die wordt gescheiden door een nieuwe regel.

### <a name="new-preview-features-in-version-80-preview"></a>Nieuwe preview-functies in versie 8.0-preview
Versie 8.0-preview van de Azure Search .NET SDK is gericht op API-versie 2017-11-11-Preview. Deze versie bevat dezelfde functies van versie 9, plus:

* [Door de klant beheerde versleutelingssleutels](search-security-manage-encryption-keys.md) voor service-side encryption-at-rest is een nieuwe preview-functie. Naast de ingebouwde encryptie-at-rest die door Microsoft wordt beheerd, u een extra versleutelingslaag toepassen waarbij u de enige eigenaar van de sleutels bent.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen om te upgraden
Werk eerst uw NuGet-referentie bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager Console of door met de rechtermuisknop op uw projectreferenties te klikken en "NuGet-pakketten beheren..." te selecteren. in Visual Studio.

Zodra NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u uw project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat zo is, ben je klaar om te gaan!

Als uw build mislukt, moet u elke buildfout oplossen. Zie [Wijzigingen in versie 9 verbreken](#ListOfChanges) voor meer informatie over het oplossen van elke potentiële buildfout.

Mogelijk ziet u aanvullende buildwaarschuwingen met betrekking tot verouderde methoden of eigenschappen. De waarschuwingen bevatten instructies over wat te gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld `DataSourceType.DocumentDb` de eigenschap gebruikt, moet u een waarschuwing krijgen met de tekst 'Dit lid is afgeschaft. Gebruik CosmosDb in plaats daarvan".

Zodra u buildfouten of waarschuwingen hebt opgelost, u wijzigingen aanbrengen in uw toepassing om te profiteren van nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 9](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Wijzigingen in versie 9 verbreken

Er zijn verschillende wijzigingen in versie 9 waarvoor mogelijk ook codewijzigingen nodig zijn naast het opnieuw opbouwen van uw toepassing.

> [!NOTE]
> De onderstaande lijst met wijzigingen is niet volledig. Sommige wijzigingen zullen waarschijnlijk niet resulteren in bouwfouten, maar zijn technisch breken omdat ze binaire compatibiliteit met verzamelingen die afhankelijk zijn van eerdere versies van de Azure Search .NET SDK-assemblages breken. Dergelijke wijzigingen worden hieronder niet vermeld. Herbouw uw toepassing bij het upgraden naar versie 9 om binaire compatibiliteitsproblemen te voorkomen.

### <a name="immutable-properties"></a>Onveranderlijke eigenschappen

De openbare eigenschappen van verschillende modelklassen zijn nu onveranderlijk. Als u aangepaste exemplaren van deze klassen moet maken voor het testen, u de nieuwe parameterparameters gebruiken:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Wijzigingen in veld

De `Field` klasse is gewijzigd nu deze ook complexe velden kan vertegenwoordigen.

De `bool` volgende eigenschappen zijn nu nietig verklaard:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Dit komt omdat deze `null` eigenschappen nu in het geval van complexe velden moeten zijn. Als u code hebt die deze eigenschappen leest, `null`moet deze worden voorbereid om deze te verwerken. Houd er rekening `Field` mee dat alle andere eigenschappen van altijd te nietteer baar zijn en blijven, en sommige daarvan zullen ook in het geval van complexe velden zijn `null` - met name de volgende:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

De parameterloze constructor `Field` van `internal`is gemaakt . Vanaf nu vereist `Field` elke een expliciete naam en gegevenstype op het moment van de bouw.

### <a name="simplified-batch-and-results-types"></a>Vereenvoudigde batch- en resultatentypen

In versie 7.0-preview en eerder werden de verschillende klassen die groepen documenten inkapselen gestructureerd in parallelle klassenhiërarchieën:

  -  `DocumentSearchResult`en `DocumentSearchResult<T>` geërfd van`DocumentSearchResultBase`
  -  `DocumentSuggestResult`en `DocumentSuggestResult<T>` geërfd van`DocumentSuggestResultBase`
  -  `IndexAction`en `IndexAction<T>` geërfd van`IndexActionBase`
  -  `IndexBatch`en `IndexBatch<T>` geërfd van`IndexBatchBase`
  -  `SearchResult`en `SearchResult<T>` geërfd van`SearchResultBase`
  -  `SuggestResult`en `SuggestResult<T>` geërfd van`SuggestResultBase`

De afgeleide typen zonder een generieke typeparameter waren bedoeld om te worden gebruikt `Document` in "dynamisch getypte" scenario's en het veronderstelde gebruik van het type.

Vanaf versie 8.0-preview zijn de basisklassen en niet-generieke afgeleide klassen allemaal verwijderd. Voor dynamisch getypte scenario's `IndexBatch<Document>`kunt `DocumentSearchResult<Document>`u , enzovoort, gebruiken.
 
### <a name="removed-extensibleenum"></a>Verwijderd ExtensibleEnum

De `ExtensibleEnum` basisklasse is verwijderd. Alle klassen die daaruit zijn afgeleid, zijn nu `AnalyzerName` `DataType`structs, zoals , en `DataSourceType` bijvoorbeeld. Hun `Create` methoden zijn ook verwijderd. U oproepen `Create` eenvoudig verwijderen omdat deze typen impliciet converteerbaar zijn van tekenreeksen. Als dit leidt tot compilerfouten, u de conversieoperator expliciet aanroepen via casten naar disambiguatetypen. U bijvoorbeeld de code als volgt wijzigen:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

in deze:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Eigenschappen met optionele waarden van deze typen worden nu expliciet als niet-teder getypt, zodat ze optioneel blijven.

### <a name="removed-facetresults-and-hithighlights"></a>Verwijderde FacetResults en HitHighlights

De `FacetResults` `HitHighlights` klassen zijn verwijderd. Facet resultaten zijn nu `IDictionary<string, IList<FacetResult>>` getypt als `IDictionary<string, IList<string>>`en hit hoogtepunten als . Een snelle manier om buildfouten op te `using` lossen die door deze wijziging zijn geïntroduceerd, is door aliassen toe te voegen aan de bovenkant van elk bestand dat de verwijderde typen gebruikt. Bijvoorbeeld:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Wijzigen in Synoniemmap 

De `SynonymMap` constructeur heeft `enum` geen `SynonymMapFormat`parameter meer voor . Dit enum had slechts één waarde en was daarom overbodig. Als u buildfouten als gevolg hiervan ziet, verwijdert `SynonymMapFormat` u verwijzingen naar de parameter.

### <a name="miscellaneous-model-class-changes"></a>Diverse wijzigingen in modelklassen

De `AutocompleteMode` eigenschap `AutocompleteParameters` van is niet langer tenietbaar. Als u code hebt die `null`deze eigenschap toewijst aan, u deze eenvoudig verwijderen en wordt de eigenschap automatisch geïnitialiseerd tot de standaardwaarde.

De volgorde van de `IndexAction` parameters voor de constructor is gewijzigd nu deze constructor automatisch wordt gegenereerd. In plaats van de constructeur te gebruiken, raden we aan om de fabrieksmethoden `IndexAction.Upload`te gebruiken, `IndexAction.Merge`enzovoort.

### <a name="removed-preview-features"></a>Verwijderde voorbeeldfuncties

Als u een upgrade uitvoert van versie 8.0-preview naar versie 9, moet u er rekening mee houden dat versleuteling met door de klant beheerde sleutels is verwijderd omdat deze functie nog steeds in preview is. Specifiek, `EncryptionKey` de `Index` eigenschappen `SynonymMap` van en zijn verwijderd.

Als uw toepassing een harde afhankelijkheid van deze functie heeft, u niet upgraden naar versie 9 van de Azure Search .NET SDK. U versie 8.0-preview blijven gebruiken. Houd er echter rekening mee dat **we het gebruik van preview-SDK's in productietoepassingen niet aanraden.** Preview-functies zijn alleen voor evaluatie en kunnen veranderen.

> [!NOTE]
> Als u versleutelde indexen of synoniemkaarten hebt gemaakt met versie 8.0-preview van de SDK, u deze nog steeds gebruiken en hun definities wijzigen met versie 9 van de SDK zonder de versleutelingsstatus ervan te schaden. Versie 9 van de SDK `encryptionKey` stuurt de eigenschap niet naar de REST API en als gevolg daarvan verandert de REST API de versleutelingsstatus van de bron niet. 

### <a name="behavioral-change-in-data-retrieval"></a>Gedragsverandering in het ophalen van gegevens

Als u de 'dynamisch getypte' `Search` `Suggest`of `Get` API's gebruikt die `Document`typetypen retourneren, moet u er rekening `object[]` mee `string[]`houden dat ze nu lege JSON-arrays deserialiseren naar in plaats van .

## <a name="conclusion"></a>Conclusie
Zie de [.NET How-to](search-howto-dotnet-sdk.md).

Wij zijn blij met uw feedback op de SDK. Als u problemen ondervindt, u ons gerust om hulp vragen op [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Als u een bug vindt, u een probleem indienen in de [Azure .NET SDK GitHub-repository.](https://github.com/Azure/azure-sdk-for-net/issues) Zorg ervoor dat u de uitgiftetitel vooraf opmaakt met '[Azure Search]'.

Bedankt voor het gebruik van Azure Search!
