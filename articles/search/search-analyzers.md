---
title: Analyse functies voor taal kundige en tekst verwerking
titleSuffix: Azure Cognitive Search
description: U kunt analyse functies toewijzen aan Doorzoek bare tekst velden in een index om de standaard standaard-lucene te vervangen door aangepaste, vooraf gedefinieerde of taalspecifieke alternatieven.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: 2e4a6ab8825982969ffa4654c2418f7a9d168d2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75460716"
---
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analyse functies voor tekst verwerking in azure Cognitive Search

Een *Analyzer* is een onderdeel van de [Zoek machine voor volledige tekst](search-lucene-query-architecture.md) die verantwoordelijk is voor het verwerken van tekst in query reeksen en geïndexeerde documenten. Verschillende analyse functies bewerken tekst op verschillende manieren, afhankelijk van het scenario. Language analyzers verwerken tekst met taal kundige regels om de zoek kwaliteit te verbeteren, terwijl andere analyse functies meer basis taken uitvoeren, zoals het omzetten van tekens in kleine letters, bijvoorbeeld. 

Taal analyse functies worden het vaakst gebruikt en er is een standaardtaal analyse toegewezen aan elk Doorzoek bare veld in een Azure Cognitive Search-index. De volgende taal transformaties zijn gebruikelijk tijdens de analyse van tekst:

+ Niet-essentiële woorden (stopwords) en interpunctie worden verwijderd.
+ Woord groepen en afgebroken woorden worden onderverdeeld in onderdeel onderdelen.
+ Hoofd letters zijn kleine woorden.
+ Woorden worden gereduceerd tot hoofd formulieren, zodat er een overeenkomst kan worden gevonden, ongeacht de tien tallen.

Taal analysen converteren een tekst invoer in primitieve of hoofd formulieren die efficiënt zijn voor het opslaan en ophalen van informatie. Conversie vindt plaats tijdens het indexeren, wanneer de index is gemaakt en wanneer de index wordt gelezen tijdens de zoek opdracht. Het is waarschijnlijker dat u de verwachte Zoek resultaten krijgt als u dezelfde analyse functie voor beide bewerkingen gebruikt.

## <a name="default-analyzer"></a>Standaard-Analyzer  

Azure Cognitive Search maakt gebruik van [Apache Lucene Standard Analyzer (standaard-lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) als standaard, waardoor tekst wordt gesplitst in elementen volgens de regels voor [Unicode-tekst segmenten](https://unicode.org/reports/tr29/) . Daarnaast worden alle tekens in de vorm van een kleine letters door de standaard-Analyzer geconverteerd. Zowel geïndexeerde documenten als zoek termen voeren de analyse uit tijdens het indexeren en verwerken van query's.  

Deze wordt automatisch gebruikt voor elk Doorzoek bare veld. U kunt de standaard waarde voor een veld per veld overschrijven. Alternatieve analyse functies kunnen een [taal analyse](index-add-language-analyzers.md), een [aangepaste analyse functie](index-add-custom-analyzers.md)of een vooraf gedefinieerde analyse zijn van de [lijst met beschik bare analyse](index-add-custom-analyzers.md#AnalyzerTable)functies.


## <a name="types-of-analyzers"></a>Typen analyse functies

In de volgende lijst wordt beschreven welke analyse functies beschikbaar zijn in azure Cognitive Search.

| Categorie | Beschrijving |
|----------|-------------|
| [Standard-lucene Analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standaard. Er is geen specificatie of configuratie vereist. Deze General-Purpose Analyzer werkt goed voor de meeste talen en scenario's.|
| Vooraf gedefinieerde analyse functies | Wordt aangeboden als een voltooid product dat is bedoeld om te worden gebruikt als-is. <br/>Er zijn twee typen: speciaal en taal. Wat maakt het ' vooraf gedefinieerd ' dat u naar de naam verwijst, zonder configuratie of aanpassing. <br/><br/>[Gespecialiseerde analyse functies (taal-neutraal)](index-add-custom-analyzers.md#AnalyzerTable) worden gebruikt wanneer tekst invoer gespecialiseerde verwerking of minimale verwerking vereist. Vooraf gedefinieerde analyse functies zonder taal zijn **Asciifolding**, **tref woord**, **patroon**, **eenvoudig**, **Stop**, **spatie**.<br/><br/>[Taal analysen](index-add-language-analyzers.md) worden gebruikt wanneer u ondersteuning voor uitgebreide taal functionaliteit voor afzonderlijke talen nodig hebt. Azure Cognitive Search biedt ondersteuning voor taal analyses van 35 en 50 micro soft voor de verwerking van natuurlijke taal. |
|[Analysevoorzieningen aanpassen](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Verwijst naar een door de gebruiker gedefinieerde configuratie van een combi natie van bestaande elementen, die bestaat uit één tokenizer (vereist) en optionele filters (char of Token).|

Een paar vooraf gedefinieerde analyse functies, zoals **patroon** of **Stop**, ondersteunen een beperkte set configuratie opties. Als u deze opties wilt instellen, maakt u effectief een aangepaste analyse functie die bestaat uit de vooraf gedefinieerde analyse functie en een van de alternatieve opties die worden beschreven in de [vooraf gedefinieerde analyse verwijzing](index-add-custom-analyzers.md#AnalyzerTable). Net als bij elke aangepaste configuratie geeft u de nieuwe configuratie op met een naam, zoals *myPatternAnalyzer* om deze te onderscheiden van de Lucene-patroon analyse.

## <a name="how-to-specify-analyzers"></a>Analyse functies opgeven

1. (alleen voor aangepaste analyse functies) Maak een sectie met de naam **analyse** in de index definitie. Zie [index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) en [aangepaste analyse functies toevoegen](index-add-custom-analyzers.md)voor meer informatie.

2. Stel op een [veld definitie](https://docs.microsoft.com/rest/api/searchservice/create-index) in de index de eigenschap **Analyzer** van het veld in op de naam van een doel-Analyzer (bijvoorbeeld `"analyzer" = "keyword"`. Geldige waarden zijn naam van een vooraf gedefinieerde Analyzer, taal analyse of aangepaste Analyzer die ook in het index schema is gedefinieerd. Plan het toewijzen van Analyzer in de index definitie fase voordat de index in de service wordt gemaakt.

3. U kunt eventueel, in plaats van één **analyse** -eigenschap, verschillende analyse functies instellen voor indexering en query's uitvoeren met behulp van de veld parameters **indexAnalyzer** en **searchAnalyzer** . U gebruikt verschillende analyse functies voor het voorbereiden en ophalen van gegevens als een van deze activiteiten vereist dat een specifieke trans formatie niet nodig is voor de andere.

> [!NOTE]
> Het is niet mogelijk om een andere [taal analyse](index-add-language-analyzers.md) te gebruiken tijdens de indexerings tijd dan bij het opvragen van een veld. Deze mogelijkheid is gereserveerd voor [aangepaste analyse](index-add-custom-analyzers.md)functies. Als u daarom probeert de eigenschappen **searchAnalyzer** of **indexAnalyzer** in te stellen op de naam van een taal analyse, retourneert de rest API een fout bericht. U moet in plaats daarvan de eigenschap **Analyzer** gebruiken.

Het is niet toegestaan om **analyse** -of **indexAnalyzer** toe te wijzen aan een veld dat al fysiek is gemaakt. Als dat niet het geval is, raadpleegt u de volgende tabel voor een overzicht van de acties die moeten worden opgebouwd.
 
 | Scenario | Impact | Stappen |
 |----------|--------|-------|
 | Een nieuw veld toevoegen | minimale | Als het veld nog niet in het schema bestaat, is er geen veld revisie om te maken, omdat het veld nog geen fysieke aanwezigheid in uw index heeft. U kunt [update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) gebruiken om een nieuw veld toe te voegen aan een bestaande index en [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) te vullen.|
 | Een **Analyzer** -of **indexAnalyzer** toevoegen aan een bestaand geïndexeerd veld. | [opnieuw opbouwen](search-howto-reindex.md) | De omgekeerde index voor dat veld moet vanaf het begin opnieuw worden gemaakt en de inhoud voor deze velden moet opnieuw worden geïndexeerd. <br/> <br/>Voor indexen onder actieve ontwikkeling [verwijdert](https://docs.microsoft.com/rest/api/searchservice/delete-index) en [maakt](https://docs.microsoft.com/rest/api/searchservice/create-index) u de index om de nieuwe veld definitie op te halen. <br/> <br/>Voor indexen in productie kunt u een nieuwe build uitstellen door een nieuw veld te maken om de gereviseerde definitie te geven en te gebruiken in plaats van de oude. Gebruik [update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) om het nieuwe veld en [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) op te nemen. Later kunt u als onderdeel van de geplande index onderhoud de index opschonen om verouderde velden te verwijderen. |

## <a name="when-to-add-analyzers"></a>Wanneer u analyse functies wilt toevoegen

De beste manier om analyseers toe te voegen en toe te wijzen, is tijdens de actieve ontwikkeling, wanneer het verwijderen en opnieuw maken van indexen de routine is.

Als index definitie solidifies kunt u nieuwe analyse constructies toevoegen aan een index, maar moet u de **allowIndexDowntime** -vlag door geven om de index bij te [werken](https://docs.microsoft.com/rest/api/searchservice/update-index) als u deze fout wilt voor komen:

*De index update is niet toegestaan omdat deze downtime zou veroorzaken. Als u nieuwe analyse functies, tokenizers, token filters of teken filters wilt toevoegen aan een bestaande index, stelt u de query parameter ' allowIndexDowntime ' in op ' True ' in de aanvraag voor het bijwerken van de index. Houd er rekening mee dat met deze bewerking de index ten minste enkele seconden offline wordt gezet, waardoor uw indexerings-en query aanvragen mislukken. De prestaties en schrijf Beschik baarheid van de index kunnen enkele minuten na de index worden bijgewerkt, of langer voor zeer grote indexen. "*

Hetzelfde geldt voor het toewijzen van een Analyzer aan een veld. Een Analyzer is een integraal onderdeel van de definitie van het veld, zodat u het alleen kunt toevoegen wanneer het veld wordt gemaakt. Als u analyse functies wilt toevoegen aan bestaande velden, moet u de index [verwijderen en opnieuw samen stellen](search-howto-reindex.md) , of een nieuw veld toevoegen met de analyse die u wilt.

Zoals aangegeven, is een uitzonde ring de **searchAnalyzer** -variant. Op de drie manieren om analyse functies op te geven (**Analyzer**, **indexAnalyzer**, **searchAnalyzer**), kan alleen het kenmerk **searchAnalyzer** worden gewijzigd in een bestaand veld.

## <a name="recommendations-for-working-with-analyzers"></a>Aanbevelingen voor het werken met analyse functies

In deze sectie vindt u advies over het werken met analyse functies.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Eén analyse functie voor lezen/schrijven, tenzij u specifieke vereisten hebt

Met Azure Cognitive Search kunt u verschillende analyse functies opgeven voor indexering en zoeken via aanvullende **indexAnalyzer** -en **searchAnalyzer** -veld parameters. Als u geen waarde opgeeft, wordt de analyseset met de eigenschap **Analyzer** gebruikt voor het indexeren en zoeken. Als `analyzer` niet wordt opgegeven, wordt de standaard standaard-lucene Analyzer gebruikt.

Een algemene regel is het gebruik van dezelfde analyse functie voor indexering en query's, tenzij specifieke vereisten anders worden bepaald. Zorg ervoor dat u zorgvuldig test. Wanneer tekst verwerking afwijkt van de zoek-en indexerings tijd, loopt u het risico dat de query termen en geïndexeerde voor waarden niet overeenkomen wanneer de zoek-en indexerings analyse configuraties niet zijn uitgelijnd.

### <a name="test-during-active-development"></a>Testen tijdens actieve ontwikkeling

Voor het overschrijven van de Standard Analyzer is het opnieuw samen stellen van de index vereist. U kunt, indien mogelijk, bepalen welke analyse functies moeten worden gebruikt tijdens de actieve ontwikkeling voordat u een index naar productie rolt.

### <a name="inspect-tokenized-terms"></a>Token-termen controleren

Als voor een zoek opdracht verwachte resultaten niet worden geretourneerd, zijn de meest waarschijnlijke token verschillen tussen de invoer van de term en de tokens in de index. Als de tokens niet hetzelfde zijn, komen overeenkomsten niet overeen met realiseren. Als u tokenizer-uitvoer wilt controleren, kunt u het beste de [analyse-API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) als een hulp programma voor onderzoek gebruiken. Het antwoord bestaat uit tokens, zoals wordt gegenereerd door een specifieke analyse functie.

<a name="examples"></a>

## <a name="rest-examples"></a>REST-voor beelden

In de onderstaande voor beelden worden de analyse definities voor enkele van de belangrijkste scenario's weer gegeven.

+ [Voor beeld van aangepaste analyse](#Custom-analyzer-example)
+ [Analyse functies toewijzen aan een voor beeld van een veld](#Per-field-analyzer-assignment-example)
+ [Analyse functies combi neren voor indexering en zoek acties](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Language Analyzer-voor beeld](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Voor beeld van aangepaste analyse

In dit voor beeld ziet u een analyse definitie met aangepaste opties. Aangepaste opties voor teken filters, tokenizers en Token filters worden afzonderlijk opgegeven als benoemde constructs en vervolgens verwezen in de analyse definitie. Vooraf gedefinieerde elementen worden gebruikt als-is en worden simpelweg naar de naam verwezen.

Dit voor beeld door lopen:

* Analyse functies zijn een eigenschap van de veld klasse voor een doorzoekbaar veld.
* Een aangepaste analyse functie maakt deel uit van een index definitie. Het kan licht worden aangepast (u kunt bijvoorbeeld één optie in één filter aanpassen) of op meerdere locaties aangepast.
* In dit geval is de aangepaste Analyzer "my_analyzer", die op zijn beurt gebruikmaakt van een aangepaste standaard tokenizer "my_standard_tokenizer" en twee token filters: kleine en aangepaste asciifolding-filter "my_asciifolding".
* Ook worden er 2 aangepaste teken filters map_dash en remove_whitespace gedefinieerd. De eerste Hiermee vervangt u alle streepjes door onderstrepings tekens, terwijl de tweede een spatie verwijdert. Spaties moeten UTF-8-code ring zijn in de toewijzings regels. De teken filters worden vóór het tokenen toegepast en beïnvloeden de resulterende tokens (de standaard-tokenizer pauzes op een streepje en spaties, maar niet op een onderstrepings teken).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>Voor beeld van analyse toewijzing per veld

Standaard-Analyzer is de standaard instelling. Stel dat u de standaard instelling wilt vervangen door een andere vooraf gedefinieerde analyse, zoals de patroon analyse. Als u geen aangepaste opties instelt, hoeft u deze alleen op naam op te geven in de veld definitie.

Het element ' Analyzer ' overschrijft de standaard analyse functie op basis van een veld. Er is geen globale onderdrukking. In dit voor beeld `text1` maakt gebruik van de patroon `text2`analyse en, waarmee geen Analyzer wordt opgegeven, gebruikt de standaard waarde.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Analyse functies combi neren voor indexerings-en zoek bewerkingen

De Api's bevatten aanvullende index kenmerken voor het opgeven van verschillende analyse functies voor indexering en zoek opdrachten. De kenmerken **searchAnalyzer** en **indexAnalyzer** moeten worden opgegeven als een paar, waarbij het één **analyse** kenmerk wordt vervangen.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>Language Analyzer-voor beeld

Velden met teken reeksen in verschillende talen kunnen gebruikmaken van een taal analyse, terwijl andere velden de standaard waarde behouden (of een andere vooraf gedefinieerde of aangepaste analyse functie gebruiken). Als u een taal analyse gebruikt, moet deze worden gebruikt voor indexerings-en zoek bewerkingen. Velden die gebruikmaken van een taal analyse kunnen geen verschillende analyse functies hebben voor indexering en zoek opdrachten.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#-voor beelden

Als u de .NET SDK-code voorbeelden gebruikt, kunt u deze voor beelden toevoegen om analyse functies te gebruiken of te configureren.

+ [Een ingebouwde analyse functie toewijzen](#Assign-a-language-analyzer)
+ [Een analyse functie configureren](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Een taal analyse toewijzen

Elke Analyzer die wordt gebruikt als-is, zonder configuratie, is opgegeven voor een veld definitie. Er is geen vereiste voor het maken van een Analyzer-constructie. 

In dit voor beeld worden de Engelse en Franse analyse functies van micro soft toegewezen aan de velden Beschrijving. Het is een fragment dat is gemaakt op basis van een grotere definitie van de hotels-index, gemaakt met behulp van de klasse hotel in het hotels.cs-bestand van het [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) -voor beeld.

Call [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), het type [analysator](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) opgeven dat een tekst analyse levert die wordt ondersteund in azure Cognitive Search.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>Een aangepaste analyse functie definiëren

Wanneer aanpassing of configuratie vereist is, moet u een Analyzer-constructie toevoegen aan een index. Zodra u het hebt gedefinieerd, kunt u de definitie van het veld toevoegen, zoals wordt getoond in het vorige voor beeld.

Maak een [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) -object. Zie [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)voor meer voor beelden.

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>Volgende stappen

+ Bekijk onze uitgebreide uitleg [over de manier waarop zoeken in volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md). In dit artikel worden voor beelden gebruikt om gedrag te verklaren dat op het Opper vlak kan lijken op het licht.

+ Probeer extra query syntaxis uit de sectie voor beeld van [Zoeken in documenten](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) of van een [eenvoudige query syntaxis](query-simple-syntax.md) in Search Explorer in de portal.

+ Meer informatie over het Toep assen van [taalspecifieke lexicale analyse](index-add-language-analyzers.md)functies.

+ [Aangepaste analyse functies configureren](index-add-custom-analyzers.md) voor een minimale verwerking of gespecialiseerde verwerking op afzonderlijke velden.

## <a name="see-also"></a>Zie ook

 [REST API voor documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Vereenvoudigde querysyntaxis](query-simple-syntax.md) 

 [Volledige Lucene-querysyntaxis](query-lucene-syntax.md) 
 
 [Zoekresultaten verwerken](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
