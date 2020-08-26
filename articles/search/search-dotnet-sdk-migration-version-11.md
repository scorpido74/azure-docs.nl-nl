---
title: Upgrade uitvoeren naar .NET SDK versie 11
titleSuffix: Azure Cognitive Search
description: Code migreren naar Azure Cognitive Search .NET SDK versie 11 van oudere versies. Meer informatie over wat er nieuw is en welke code wijzigingen vereist zijn.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: 5dd061309447dd6037d2dd664e7c5db2c7df38cc
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870201"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Upgrade uitvoeren naar Azure Cognitive Search .NET SDK versie 11

Als u versie 10,0 of ouder van de [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search)gebruikt, helpt dit artikel bij het bijwerken naar versie 11.

Versie 11 is een volledig opnieuw ontworpen client bibliotheek, uitgebracht door het team van Azure SDK Development (eerdere versies zijn gemaakt door het Azure Cognitive Search Development-Team). De tape wisselaar is opnieuw ontworpen voor een grotere consistentie met andere Azure-client Bibliotheken, met een afhankelijkheid van [Azure. core](https://docs.microsoft.com/dotnet/api/azure.core) en [System.Text.Jsop](https://docs.microsoft.com/dotnet/api/system.text.json)en het implementeren van bekende benaderingen voor algemene taken.

Enkele belang rijke verschillen in de nieuwe versie zijn:

+ Eén pakket en bibliotheek, in tegens telling tot meerdere
+ Een nieuwe pakket naam: `Azure.Search.Documents` in plaats van `Microsoft.Azure.Search` .
+ Drie clients in plaats van twee: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ Benoemt verschillen over een reeks Api's en kleine structurele verschillen die sommige taken vereenvoudigen

> [!NOTE]
> Bekijk het [**wijzigingslog bestand**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) voor een lijst met wijzigingen in .NET SDK versie 11.

## <a name="package-and-library-consolidation"></a>Pakket-en bibliotheek consolidatie

Met versie 11 worden meerdere pakketten en bibliotheken samengevoegd tot één. Na de migratie kunt u minder bibliotheken beheren.

+ [ Uments-pakketAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [API-verwijzing voor de client bibliotheek](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>Client verschillen

Indien van toepassing, wijst de volgende tabel de client bibliotheken toe tussen de twee versies.

| Bereik van bewerkingen | Micro soft. Azure. Search &nbsp; (v10 toevoegen) | Azure.Search.Documents &nbsp; (V11) |
|---------------------|------------------------------|------------------------------|
| Client die wordt gebruikt voor query's en voor het vullen van een index. | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient) |
| Client die wordt gebruikt voor indexen, analyse functies, synoniemen | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| Client die wordt gebruikt voor Indexeer functies, gegevens bronnen, vaardig heden | [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**Nieuw**)](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` bestaat in beide versies, maar ondersteunt verschillende dingen. Maak in versie 10 `SearchIndexClient` indexen en andere objecten. In versie 11 `SearchIndexClient` werkt met bestaande indexen. Om Verwar ring te voor komen bij het bijwerken van code, moet u mindful van de volg orde waarin de client verwijzingen worden bijgewerkt. Als u de volg orde van de [stappen voor het uitvoeren](#UpgradeSteps) van een upgrade volgt, moet u problemen met de vervanging van teken reeksen oplossen.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>Naamgeving en andere API-verschillen

Naast de verschillen tussen de client (eerder vermeld en daarom wegge laten), hebben meerdere andere Api's een andere naam gekregen en in sommige gevallen opnieuw ontworpen. Hieronder vindt u een overzicht van de namen van klassenaam. Deze lijst is niet volledig, maar groeps-API-wijzigingen per taak, wat nuttig kan zijn voor revisies van specifieke code blokken. Zie het [wijzigingslog bestand](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) voor op github voor een lijst met updates van de API `Azure.Search.Documents` .

### <a name="authentication-and-encryption"></a>Verificatie en versleuteling

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` (bestond in de [Preview-SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) als een algemeen beschik bare functie) | [SearchResourceEncryptionKey](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>Indexen, analyse functies, synoniemen kaarten

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [TabIndex](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [Veld](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field) | [SearchField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [Param1](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [Analyse](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (ook `AnalyzerName` naar `LexicalAnalyzerName` ) |
| [AnalyzeRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (ook `StandardTokenizerV2` naar `LuceneStandardTokenizerV2` ) |
| [TokenInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [Tokenizer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (ook `TokenizerName` naar `LexicalTokenizerName` ) |
| [SynonymMap. Format](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap.format) | Geen. Verwijder verwijzingen naar `Format` . |

Veld definities worden gestroomlijnd: [SearchableField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.simplefield), [ComplexField](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.complexfield) zijn nieuwe api's voor het maken van veld definities.

### <a name="indexers-datasources-skillsets"></a>Indexeer functies, gegevens bronnen, vaardig heden

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [Indexeerfunctie](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [Bron](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](https://docs.microsoft.com//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [Vaardigheid](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Vaardig heden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [Data Source Type](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>Gegevens importeren

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [IndexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction) | [IndexDocumentsAction](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch) | [IndexDocumentsBatch](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>Query definities en resultaten

| Versie 10 | Equivalent van versie 11 |
|------------|-----------------------|
| [DocumentSearchResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresult-1) of [searchresults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.searchresults-1), afhankelijk van het feit of het resultaat één document of meerdere is. |
| [DocumentSuggestResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](https://docs.microsoft.com/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters) |  [Search Options worden](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>Wat is er in versie 11

Elke versie van een Azure Cognitive Search-client bibliotheek is gericht op een bijbehorende versie van de REST API. De REST API wordt beschouwd als basis voor de service, waarbij afzonderlijke Sdk's een versie van de REST API inpakken. Als .NET-ontwikkelaar kan het nuttig zijn om [rest API documentatie](https://docs.microsoft.com/rest/api/searchservice/) te controleren als u meer achtergrond informatie wilt over specifieke objecten of bewerkingen.

Versie 11 streeft [naar de 2020-06-30-zoek service](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json). Omdat versie 11 ook een nieuwe client bibliotheek is die is gebouwd op basis van het begin, is het grootste deel van de ontwikkelings inspanning gericht op equivalentie met versie 10, met een aantal REST API functie-ondersteuning nog in behandeling.

Versie 11,0 biedt volledige ondersteuning voor de volgende objecten en bewerkingen:

+ Index maken en beheren
+ Synoniemen voor het maken en beheren van kaarten
+ Alle query typen en-syntaxis (met uitzonde ring van geo-ruimtelijke filters)
+ Indexeer objecten en bewerkingen voor het indexeren van Azure-gegevens bronnen, waaronder gegevens bronnen en vaardig heden

Versie 11,1 voegt het volgende toe:

+ [FieldBuilder](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.fieldbuilder) (toegevoegd in 11,1)
+ [Serializer-eigenschap](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclientoptions.serializer) (toegevoegd in 11,1) ter ondersteuning van aangepaste serialisatie

### <a name="pending-features"></a>Functies in behandeling

De volgende versie 10-functies zijn nog niet beschikbaar in versie 11. Als u deze functies nodig hebt, moet u de migratie uitschakelen totdat deze worden ondersteund.

+ georuimtelijke typen
+ [Kennisarchief](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Stappen voor het uitvoeren van een upgrade

Met de volgende stappen kunt u aan de slag gaan met een code migratie door de eerste set vereiste taken uit te voeren, met name met betrekking tot client verwijzingen.

1. Installeer het [Azure.Search.Documents-pakket](https://www.nuget.org/packages/Azure.Search.Documents/) door met de rechter muisknop op uw project verwijzingen te klikken en vervolgens NuGet-pakketten beheren te selecteren. in Visual Studio.

1. Vervang met behulp van de instructies voor micro soft. Azure. Search door het volgende:

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Voor klassen waarvoor JSON-serialisatie is vereist, vervangt u door `using Newtonsoft.Json` `using System.Text.Json.Serialization` .

1. Client verificatie code wijzigen. In vorige versies zou u eigenschappen op het client object gebruiken om de API-sleutel in te stellen (bijvoorbeeld de eigenschap [SearchServiceClient. credentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) ). Gebruik in de huidige versie de [AzureKeyCredential](https://docs.microsoft.com/dotnet/api/azure.azurekeycredential) -klasse om de sleutel als referentie door te geven, zodat u, indien nodig, de API-sleutel kunt bijwerken zonder nieuwe client objecten te maken.

   Client eigenschappen zijn gestroomlijnd tot alleen `Endpoint` , `ServiceName` en `IndexName` (indien van toepassing). In het volgende voor beeld wordt de systeem- [URI](https://docs.microsoft.com/dotnet/api/system.uri) -klasse gebruikt om het eind punt en de [omgevings](https://docs.microsoft.com//dotnet/api/system.environment) klasse op te geven voor het lezen van de sleutel waarde:

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. Voeg nieuwe client referenties toe voor Indexeer functie-gerelateerde objecten. Als u Indexeer functies, gegevens bronnen of vaardig heden gebruikt, wijzigt u de client verwijzingen naar [SearchIndexerClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.indexes.searchindexerclient). Deze client is nieuw in versie 11 en heeft geen ante cedent.

1. Client Referenties bijwerken voor query's en gegevens import. Instanties van [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) moeten worden gewijzigd in [SearchClient](https://docs.microsoft.com/dotnet/api/azure.search.documents.searchclient). Zorg ervoor dat u alle exemplaren onderschept voordat u verdergaat met de volgende stap om naam Verwar ring te voor komen.

1. Update client verwijzingen voor index, Indexeer functie, synoniemen kaart en analyse-objecten. Instanties van [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) moeten worden gewijzigd in [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient). 

1. Werk zo veel mogelijk klassen, methoden en eigenschappen bij om de Api's van de nieuwe bibliotheek te gebruiken. De sectie [naam verschillen](#naming-differences) is een plek waar u begint, maar u kunt ook het [wijzigings logboek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)controleren.

   Als u problemen ondervindt bij het vinden van gelijkwaardige Api's, wordt een probleem in een logboek vastgelegd, [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) zodat de documentatie kan worden verbeterd of het probleem kan worden onderzocht.

1. Bouw de oplossing opnieuw op. Na het oplossen van eventuele build-fouten of-waarschuwingen, kunt u aanvullende wijzigingen aanbrengen in uw toepassing om te profiteren van de [nieuwe functionaliteit](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>Belang rijke wijzigingen in versie 11

Gezien de veranderingen in bibliotheken en Api's, is een upgrade naar versie 11 niet-trivial en vormt het een belang rijke wijziging in de zin dat uw code niet langer compatibel is met versie 10 en eerder. Zie het [wijzigingslog bestand](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) voor voor een grondige beoordeling van de verschillen `Azure.Search.Documents` .

In termen van versie-updates van de service, waar de code wijzigingen in versie 11 zijn gerelateerd aan de bestaande functionaliteit (en niet alleen een refactorie van de Api's), vindt u de volgende gedrags wijzigingen:

+ [BM25-classificatie algoritme](index-ranking-similarity.md) vervangt het vorige classificatie algoritme met nieuwere technologie. Nieuwe services gebruiken dit algoritme automatisch. Voor bestaande services moet u para meters instellen voor het gebruik van het nieuwe algoritme.

+ [Geordende resultaten](search-query-odata-orderby.md) voor Null-waarden zijn gewijzigd in deze versie, waarbij Null-waarden eerst worden weer gegeven als de sorteer bewerking is `asc` en laatste als de sorteer bewerking is `desc` . Als u code hebt geschreven om te verwerken hoe Null-waarden worden gesorteerd, moet u deze code controleren en mogelijk verwijderen als deze niet langer nodig is.

## <a name="next-steps"></a>Volgende stappen

+ [ Uments-pakketAzure.Search.Doc](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [Voorbeelden op GitHub](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API-naslag informatie](https://docs.microsoft.com/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)