---
title: Upgraden naar Azure Search .NET SDK versie 3
titleSuffix: Azure Cognitive Search
description: Migreer code naar de Azure Search .NET SDK versie 3 van oudere versies. Meer informatie over wat er nieuw is en welke codewijzigingen vereist zijn.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fcad05749892e3a652e110a7e351450bffaca6f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792987"
---
# <a name="upgrade-to-azure-search-net-sdk-version-3"></a>Upgraden naar Azure Search .NET SDK versie 3

<!--- DETAILS in the word doc
cosmosdb
NER v1 skill 
Indexer execution result errors no longer have status
the data source API will no longer return in the response of any REST operation, the connection string specified by the user.
--->

Als u versie 2.0-preview of ouder van de [Azure Search .NET SDK](https://aka.ms/search-sdk)gebruikt, helpt dit artikel u uw toepassing te upgraden naar versie 3.

Zie [Azure Search gebruiken vanuit een .NET-toepassing](search-howto-dotnet-sdk.md)voor een meer algemene walkthrough van de SDK met voorbeelden.

Versie 3 van de Azure Search .NET SDK bevat enkele wijzigingen ten opzichte van eerdere versies. Deze zijn meestal klein, dus het wijzigen van uw code moet slechts minimale inspanning vereisen. Zie [Stappen om te upgraden](#UpgradeSteps) voor instructies over het wijzigen van uw code om de nieuwe SDK-versie te gebruiken.

> [!NOTE]
> Als u versie 1.0.2-preview of ouder gebruikt, moet u eerst upgraden naar versie 1.1 en vervolgens upgraden naar versie 3. Zie [Upgraden naar de Azure Search .NET SDK versie 1.1](search-dotnet-sdk-migration-version-1.md) voor instructies.
>
> Uw Azure Search-serviceinstantie ondersteunt verschillende REST API-versies, waaronder de nieuwste. U een versie blijven gebruiken wanneer deze niet langer de laatste is, maar we raden u aan uw code te migreren om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u de API-versie in elke aanvraag opgeven via de parameter api-versie. Bij gebruik van de .NET SDK bepaalt de versie van de SDK die u gebruikt de overeenkomstige versie van de REST API. Als u een oudere SDK gebruikt, u die code zonder wijzigingen blijven uitvoeren, zelfs als de service is geüpgraded om een nieuwere API-versie te ondersteunen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Nieuwe versie 3
Versie 3 van de Azure Search .NET SDK is gericht op de nieuwste algemeen beschikbare versie van de Azure Search REST API, met name 2016-09-01. Dit maakt het mogelijk om veel nieuwe functies van Azure Search te gebruiken vanuit een .NET-toepassing, waaronder het volgende:

* [Analysevoorzieningen aanpassen](https://aka.ms/customanalyzers)
* Ondersteuning voor [Azure Blob Storage-](search-howto-indexing-azure-blob-storage.md) en [Azure Table Storage-indexeerders](search-howto-indexing-azure-tables.md)
* Indexeraanpassing via [veldtoewijzingen](search-indexer-field-mappings.md)
* ETags-ondersteuning om een veilige gelijktijdige actualisering van indexdefinities, indexeerders en gegevensbronnen mogelijk te maken
* Ondersteuning voor het bouwen van indexvelddefinities declaratief door `FieldBuilder` het verfraaien van uw modelklasse en het gebruik van de nieuwe klasse.
* Ondersteuning voor .NET Core en .NET Portable Profile 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen om te upgraden
Werk eerst uw NuGet-referentie bij voor `Microsoft.Azure.Search` het gebruik van de NuGet Package Manager Console of door met de rechtermuisknop op uw projectreferenties te klikken en "NuGet-pakketten beheren..." te selecteren. in Visual Studio.

Zodra NuGet de nieuwe pakketten en hun afhankelijkheden heeft gedownload, bouwt u uw project opnieuw op. Afhankelijk van hoe uw code is gestructureerd, kan deze opnieuw worden opgebouwd. Als dat zo is, ben je klaar om te gaan!

Als uw build mislukt, ziet u een buildfout zoals:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

De volgende stap is het oplossen van deze buildfout. Zie [Wijzigingen in versie 3 verbreken](#ListOfChanges) voor meer informatie over de oorzaak van de fout en hoe u deze oplossen.

Mogelijk ziet u aanvullende buildwaarschuwingen met betrekking tot verouderde methoden of eigenschappen. De waarschuwingen bevatten instructies over wat te gebruiken in plaats van de afgeschafte functie. Als uw toepassing bijvoorbeeld `IndexingParameters.Base64EncodeKeys` de eigenschap gebruikt, moet u een waarschuwing krijgen die zegt`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

Zodra u buildfouten hebt opgelost, u wijzigingen aanbrengen in uw toepassing om te profiteren van nieuwe functionaliteit als u dat wilt. Nieuwe functies in de SDK worden beschreven in [Wat is er nieuw in versie 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Wijzigingen in versie 3 verbreken
Er is een klein aantal wijzigingen in versie 3 waarvoor mogelijk ook codewijzigingen nodig zijn naast het opnieuw opbouwen van uw toepassing.

### <a name="indexesgetclient-return-type"></a>Indexen.GetClient retourtype
De `Indexes.GetClient` methode heeft een nieuw retourtype. Voorheen keerde `SearchIndexClient`het terug , `ISearchIndexClient` maar dit werd gewijzigd in versie 2.0-preview, en die wijziging gaat over naar versie 3. Dit is ter ondersteuning van `GetClient` klanten die willen de methode `ISearchIndexClient`voor unit tests bespotten door het retourneren van een mock implementatie van .

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

U het hierin wijzigen om eventuele buildfouten op te lossen:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType en anderen zijn niet langer impliciet converteerbaar naar tekenreeksen
Er zijn veel typen in de Azure `ExtensibleEnum`Search .NET SDK die afkomstig zijn van . Voorheen waren deze types allemaal `string`impliciet converteerbaar naar type . Er is echter een `Object.Equals` bug ontdekt in de implementatie voor deze klassen en de fout moet worden opgelost. Expliciete conversie `string` naar is nog steeds toegestaan.

#### <a name="example"></a>Voorbeeld
Als uw code er als volgt uitziet:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

U het hierin wijzigen om eventuele buildfouten op te lossen:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Verouderde leden verwijderd

Mogelijk ziet u buildfouten met betrekking tot methoden of eigenschappen die in versie 2.0-preview als verouderd zijn gemarkeerd en vervolgens zijn verwijderd in versie 3. Als u dergelijke fouten tegenkomt, u deze als geen andere manier oplossen:

- Als u deze constructeur `ScoringParameter(string name, string value)`gebruikt: , gebruik deze in plaats daarvan:`ScoringParameter(string name, IEnumerable<string> values)`
- Als u de `ScoringParameter.Value` eigenschap gebruikt, gebruikt u in plaats daarvan de `ScoringParameter.Values` eigenschap of de `ToString` methode.
- Als u de `SearchRequestOptions.RequestId` eigenschap gebruikte, gebruikt u de `ClientRequestId` eigenschap in plaats daarvan.

### <a name="removed-preview-features"></a>Verwijderde voorbeeldfuncties

Als u een upgrade uitvoert van versie 2.0-preview naar versie 3, moet u er rekening mee houden dat json- en CSV-parsing-ondersteuning voor Blob Indexers is verwijderd omdat deze functies nog steeds in de preview staan. In het bijzonder zijn `IndexingParametersExtensions` de volgende methoden van de klasse verwijderd:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Als uw toepassing een harde afhankelijkheid van deze functies heeft, u niet upgraden naar versie 3 van de Azure Search .NET SDK. U versie 2.0-preview blijven gebruiken. Houd er echter rekening mee dat **we het gebruik van preview-SDK's in productietoepassingen niet aanraden.** Preview-functies zijn alleen voor evaluatie en kunnen veranderen.

## <a name="conclusion"></a>Conclusie
Zie de [.NET How-to](search-howto-dotnet-sdk.md).

Wij zijn blij met uw feedback op de SDK. Als u problemen ondervindt, u ons gerust om hulp vragen op [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-search) Als u een bug vindt, u een probleem indienen in de [Azure .NET SDK GitHub-repository.](https://github.com/Azure/azure-sdk-for-net/issues) Zorg ervoor dat u de uitgiftetitel vooraf opmaakt met '[Azure Search]'.

Bedankt voor het gebruik van Azure Search!
