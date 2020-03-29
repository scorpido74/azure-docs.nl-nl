---
title: Analysers voor taalkundige en tekstverwerking
titleSuffix: Azure Cognitive Search
description: Wijs analysers toe aan doorzoekbare tekstvelden in een index om standaardstandaard Lucene te vervangen door aangepaste, vooraf gedefinieerde of taalspecifieke alternatieven.
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
# <a name="analyzers-for-text-processing-in-azure-cognitive-search"></a>Analysers voor tekstverwerking in Azure Cognitive Search

Een *analyzer* is een onderdeel van de [full text zoekmachine](search-lucene-query-architecture.md) die verantwoordelijk is voor het verwerken van tekst in querystrings en geïndexeerde documenten. Verschillende analysers manipuleren tekst op verschillende manieren, afhankelijk van het scenario. Taalanalysers verwerken tekst met behulp van taalkundige regels om de zoekkwaliteit te verbeteren, terwijl andere analysators meer basistaken uitvoeren, zoals het converteren van tekens naar kleine letters, bijvoorbeeld. 

Taalanalysers worden het vaakst gebruikt en er is standaardtaalanalyzer toegewezen aan elk doorzoekbaar veld in een Azure Cognitive Search-index. De volgende taaltransformaties zijn typisch tijdens tekstanalyse:

+ Niet-essentiële woorden (stopwoorden) en interpunctie worden verwijderd.
+ Zinnen en afbreekbau woorden worden opgesplitst in onderdelen.
+ Hoofdletters zijn lager.
+ Woorden worden gereduceerd tot wortelvormen, zodat een overeenkomst kan worden gevonden, ongeacht de tijd.

Taalanalysers zetten een tekstinvoer om in primitieve of hoofdvormen die efficiënt zijn voor het opslaan en ophalen van informatie. Conversie vindt plaats tijdens het indexeren, wanneer de index wordt gebouwd, en vervolgens opnieuw tijdens het zoeken wanneer de index wordt gelezen. U krijgt meer kans om de zoekresultaten te krijgen die u verwacht als u dezelfde analyzer voor beide bewerkingen gebruikt.

## <a name="default-analyzer"></a>Standaardanalyzer  

Azure Cognitive Search gebruikt de [Apache Lucene Standard analyzer (standaard lucene)](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) als standaard, die tekst opsplitst in elementen volgens de regels van de ["Unicode Text Segmentation".](https://unicode.org/reports/tr29/) Bovendien converteert de standaardanalyzer alle tekens naar hun kleine lettersvorm. Zowel geïndexeerde documenten als zoektermen doorlopen de analyse tijdens indexering en queryverwerking.  

Het wordt automatisch gebruikt op elk doorzoekbaar veld. U de standaardstandaard per veld overschrijven. Alternatieve analysers kunnen een [taalanalyzer,](index-add-language-analyzers.md) [aangepaste analyzer](index-add-custom-analyzers.md)of een vooraf gedefinieerde analyzer zijn uit de [lijst met beschikbare analysers.](index-add-custom-analyzers.md#AnalyzerTable)


## <a name="types-of-analyzers"></a>Typen analysatoren

In de volgende lijst wordt beschreven welke analysers beschikbaar zijn in Azure Cognitive Search.

| Categorie | Beschrijving |
|----------|-------------|
| [Standaard Lucene analyzer](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | Standaard. Er is geen specificatie of configuratie vereist. Deze analysevoor algemene doeleinden presteert goed voor de meeste talen en scenario's.|
| Vooraf gedefinieerde analysatoren | Aangeboden als een afgewerkt product bedoeld om te worden gebruikt as-is. <br/>Er zijn twee soorten: gespecialiseerd en taal. Wat maakt ze "vooraf gedefinieerd" is dat u ze verwijzen op naam, zonder configuratie of aanpassing. <br/><br/>[Gespecialiseerde (taal-agnostische) analysators](index-add-custom-analyzers.md#AnalyzerTable) worden gebruikt wanneer tekstinvoer gespecialiseerde verwerking of minimale verwerking vereisen. Niet-taal vooraf gedefinieerde analyzers omvatten **Asciifolding**, **Trefwoord**, **Patroon**, **Eenvoudig**, **Stop**, **Whitespace**.<br/><br/>[Taalanalysers](index-add-language-analyzers.md) worden gebruikt wanneer u rijke taalkundige ondersteuning voor individuele talen nodig hebt. Azure Cognitive Search ondersteunt 35 Lucene taalanalysers en 50 Microsoft natural language processing analyzers. |
|[Analysevoorzieningen aanpassen](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | Verwijst naar een door de gebruiker gedefinieerde configuratie van een combinatie van bestaande elementen, bestaande uit één tokenizer (vereist) en optionele filters (char of token).|

Een paar vooraf gedefinieerde analysers, zoals **Pattern** of **Stop,** ondersteunen een beperkte set configuratieopties. Om deze opties in te stellen, maakt u effectief een aangepaste analyzer, bestaande uit de vooraf gedefinieerde analyzer en een van de alternatieve opties die zijn gedocumenteerd in [Predefined Analyzer Reference.](index-add-custom-analyzers.md#AnalyzerTable) Zoals bij elke aangepaste configuratie, geef uw nieuwe configuratie een naam, zoals *myPatternAnalyzer* om deze te onderscheiden van de Lucene Pattern analyzer.

## <a name="how-to-specify-analyzers"></a>Analysers opgeven

1. (alleen voor aangepaste analysers) Maak een benoemde **analyzersectie** in de indexdefinitie. Zie [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) en ook [Aangepaste analysers toevoegen voor](index-add-custom-analyzers.md)meer informatie.

2. Stel op een [velddefinitie](https://docs.microsoft.com/rest/api/searchservice/create-index) in de index de eigenschap van de **analyzer** van `"analyzer" = "keyword"`het veld in op de naam van een doelanalyzer (bijvoorbeeld . Geldige waarden bevatten de naam van een vooraf gedefinieerde analyzer, taalanalyzer of aangepaste analyzer die ook in het indexschema is gedefinieerd. Plan om analyzer toe te wijs in de indexdefinitiefase voordat de index in de service wordt gemaakt.

3. Optioneel u in plaats van één **eigenschap analyzer** verschillende analyseapparaten instellen voor indexering en query's met behulp van de veldparameters **indexAnalyzer** en **SearchAnalyzer.** U zou verschillende analysers gebruiken voor het voorbereiden en ophalen van gegevens als een van deze activiteiten een specifieke transformatie vereiste die niet door de andere nodig was.

> [!NOTE]
> Het is niet mogelijk om een andere [taalanalyzer](index-add-language-analyzers.md) te gebruiken op indexeringstijd dan op querytijd voor een veld. Die mogelijkheid is gereserveerd voor [aangepaste analysers.](index-add-custom-analyzers.md) Als u daarom de eigenschappen **searchAnalyzer** of **indexAnalyzer** probeert in te stellen op de naam van een taalanalyzer, geeft de REST-API een foutreactie. U moet in plaats daarvan de eigenschap **analyzer** gebruiken.

Het toewijzen van **analyzer** of **indexAnalyzer** aan een veld dat al fysiek is gemaakt, is niet toegestaan. Als dit onduidelijk is, bekijkt u de volgende tabel voor een uitsplitsing van welke acties een herbouw vereisen en waarom.
 
 | Scenario | Impact | Stappen |
 |----------|--------|-------|
 | Een nieuw veld toevoegen | Minimale | Als het veld nog niet in het schema staat, is er geen veldrevisie te maken omdat het veld nog geen fysieke aanwezigheid in uw index heeft. U [Update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) gebruiken om een nieuw veld toe te voegen aan een bestaande index en samen te [voegen Of uploaden](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) om het in te vullen.|
 | Voeg een **analyzer** of **indexAnalyzer** toe aan een bestaand geïndexeerd veld. | [Herbouwen](search-howto-reindex.md) | De omgekeerde index voor dat veld moet vanaf de grond worden opnieuw gemaakt en de inhoud voor die velden moet opnieuw worden geïndexeerd. <br/> <br/>Voor indexen onder actieve ontwikkeling [moet u](https://docs.microsoft.com/rest/api/searchservice/delete-index) de index verwijderen en [maken](https://docs.microsoft.com/rest/api/searchservice/create-index) om de nieuwe velddefinitie op te pikken. <br/> <br/>Voor indexen in de productie u een revisie uitstellen door een nieuw veld te maken om de herziene definitie te bieden en deze te gebruiken in plaats van de oude. Gebruik [Update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) om het nieuwe veld op te nemen en [Samen voeg Upload samen](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) om het te vullen. Later, als onderdeel van geplande indexonderhoud, u de index opschonen om verouderde velden te verwijderen. |

## <a name="when-to-add-analyzers"></a>Wanneer moeten analyseapparaten worden toegevoegd

De beste tijd om analyzers toe te voegen en toe te wijzen is tijdens actieve ontwikkeling, wanneer het laten vallen en opnieuw maken van indexen routine is.

Als een indexdefinitie stolt, u nieuwe analyseconstructies toevoegen aan een index, maar u moet de vlag **allowIndexDowntime** doorgeven aan [Update-index](https://docs.microsoft.com/rest/api/searchservice/update-index) als u deze fout wilt voorkomen:

*"Indexupdate is niet toegestaan omdat het downtime zou veroorzaken. Als u nieuwe analysers, tokenizers, tokenfilters of tekenfilters aan een bestaande index wilt toevoegen, stelt u de queryparameter 'allowIndexDowntime' in op 'true' in het verzoek voor het bijwerken van de index. Houd er rekening mee dat de index met deze bewerking ten minste enkele seconden offline wordt gezet, waardoor uw indexerings- en queryaanvragen mislukken. De prestaties en schrijfbeschikbaarheid van de index kunnen enkele minuten na de update van de index of langer voor zeer grote indexen worden aangetast."*

Hetzelfde geldt bij het toewijzen van een analyzer aan een veld. Een analyzer is een integraal onderdeel van de definitie van het veld, dus u deze alleen toevoegen wanneer het veld wordt gemaakt. Als u analysers aan bestaande velden wilt toevoegen, moet u de index [laten vallen en opnieuw opbouwen](search-howto-reindex.md) of een nieuw veld toevoegen met de gewenste analyzer.

Zoals opgemerkt, een uitzondering is de **searchAnalyzer** variant. Van de drie manieren om analysers op te geven **(analyzer**, **indexAnalyzer**, **searchAnalyzer),** kan alleen het kenmerk **searchAnalyzer** op een bestaand veld worden gewijzigd.

## <a name="recommendations-for-working-with-analyzers"></a>Aanbevelingen voor het werken met analysers

Deze sectie geeft advies over hoe te werken met analysers.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>Eén analyzer voor read-write, tenzij u specifieke vereisten hebt

Met Azure Cognitive Search u verschillende analysers opgeven voor indexering en zoeken via aanvullende **parameters voor indexAnalyzer** en **searchAnalyzer.** Als deze niet is opgegeven, wordt de analyzer-set met de eigenschap **analyzer** gebruikt voor zowel indexeren als zoeken. Als `analyzer` dit niet is opgegeven, wordt de standaard Lucene analyzer gebruikt.

Een algemene regel is om dezelfde analyzer te gebruiken voor zowel indexeren als query's, tenzij specifieke vereisten anders dicteren. Zorg ervoor dat u grondig te testen. Wanneer tekstverwerking verschilt op zoek- en indexeringstijd, loopt u het risico van mismatch tussen querytermen en geïndexeerde termen wanneer de configuratie van de zoek- en indexeringsanalysator niet is uitgelijnd.

### <a name="test-during-active-development"></a>Test tijdens actieve ontwikkeling

Voor het overschrijven van de standaardanalyzer is een indexreconstructie vereist. Bepaal indien mogelijk welke analysers u tijdens actieve ontwikkeling wilt gebruiken, voordat u een index in productie rolt.

### <a name="inspect-tokenized-terms"></a>Tokenized-termen inspecteren

Als een zoekopdracht de verwachte resultaten niet kan retourneren, is het meest waarschijnlijke scenario tokenverschillen tussen term-ingangen op de query en tokenized-termen in de index. Als de tokens niet hetzelfde zijn, worden overeenkomsten niet gematerialiseren. Om tokenizer-uitvoer te inspecteren, raden we u aan de [API analyseren](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) als onderzoekstool te gebruiken. De respons bestaat uit tokens, zoals gegenereerd door een specifieke analyzer.

<a name="examples"></a>

## <a name="rest-examples"></a>REST voorbeelden

De onderstaande voorbeelden tonen analysedefinities voor een paar belangrijke scenario's.

+ [Voorbeeld van aangepaste analyzer](#Custom-analyzer-example)
+ [Analyzers toewijzen aan een veldvoorbeeld](#Per-field-analyzer-assignment-example)
+ [Mixen van analysers voor indexering en zoeken](#Mixing-analyzers-for-indexing-and-search-operations)
+ [Voorbeeld van taalanalyse](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>Voorbeeld van aangepaste analyzer

In dit voorbeeld wordt een analysedefinitie met aangepaste opties geïllustreerd. Aangepaste opties voor charfilters, tokenizers en tokenfilters worden afzonderlijk opgegeven als benoemde constructen en vervolgens verwezen in de definitie van de analyzer. Vooraf gedefinieerde elementen worden gebruikt zoals-is en gewoon verwezen door naam.

Wandelen door dit voorbeeld:

* Analyzers zijn een eigenschap van de veldklasse voor een doorzoekbaar veld.
* Een aangepaste analyzer maakt deel uit van een indexdefinitie. Het kan licht worden aangepast (bijvoorbeeld het aanpassen van een enkele optie in één filter) of op meerdere plaatsen worden aangepast.
* In dit geval is de aangepaste analyzer "my_analyzer", die op zijn beurt een aangepaste standaard tokenizer "my_standard_tokenizer" en twee tokenfilters gebruikt: kleine letters en aangepaste asciifolding-filter "my_asciifolding".
* Het definieert ook 2 aangepaste char filters "map_dash" en "remove_whitespace". De eerste vervangt alle streepjes door underscores, terwijl de tweede alle spaties verwijdert. Spaties moeten worden gecodeerd in de toewijzingsregels. De char filters worden toegepast vóór tokenisatie en hebben invloed op de resulterende tokens (de standaard tokenizer breekt op dashboard en spaties, maar niet op underscore).

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

### <a name="per-field-analyzer-assignment-example"></a>Voorbeeld van een opdracht per veldanalyseer

De standaardanalyzer is de standaard. Stel dat u de standaardinstelling wilt vervangen door een andere vooraf gedefinieerde analyzer, zoals de patroonanalyzer. Als u geen aangepaste opties instelt, hoeft u deze alleen op naam op te geven in de velddefinitie.

Het element "analyzer" overschrijft de standaardanalyzer op veld-voor-veldbasis. Er is geen globale overschrijving. In dit `text1` voorbeeld wordt de `text2`patroonanalyzer gebruikt en gebruikt deze , die geen analyzer opgeeft, de standaardinstelling.

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

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>Analysers mengen voor indexering en zoekbewerkingen

De API's bevatten extra indexkenmerken voor het opgeven van verschillende analyseapparaten voor indexering en zoeken. De kenmerken **searchAnalyzer** en **indexAnalyzer** moeten als een paar worden opgegeven, ter vervanging van het kenmerk single **analyzer.**


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

### <a name="language-analyzer-example"></a>Voorbeeld van taalanalyse

Velden met tekenreeksen in verschillende talen kunnen een taalanalyzer gebruiken, terwijl andere velden de standaardbehouden (of een andere vooraf gedefinieerde of aangepaste analyzer gebruiken). Als u een taalanalyzer gebruikt, moet deze worden gebruikt voor zowel indexering als zoekbewerkingen. Velden die een taalanalyzer gebruiken, kunnen geen verschillende analysers hebben voor indexering en zoeken.

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

## <a name="c-examples"></a>C# voorbeelden

Als u de .NET SDK-codevoorbeelden gebruikt, u deze voorbeelden toevoegen om analysators te gebruiken of te configureren.

+ [Een ingebouwde analyzer toewijzen](#Assign-a-language-analyzer)
+ [Een analyzer configureren](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>Een taalanalyzer toewijzen

Elke analyzer die wordt gebruikt als-is, zonder configuratie, wordt opgegeven op een velddefinitie. Er is geen vereiste voor het maken van een analyzer construct. 

In dit voorbeeld worden Microsoft English- en Franse analysators aan beschrijvingsvelden toegedeeld. Het is een fragment uit een grotere definitie van de hotels-index, waardoor de klasse Hotel wordt gemaakt in het hotels.cs bestand van het [Voorbeeld van DotNetHowTo.](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)

Call [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet), het opgeven van het [type AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) met een tekstanalyzer die wordt ondersteund in Azure Cognitive Search.

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

### <a name="define-a-custom-analyzer"></a>Een aangepaste analyzer definiëren

Wanneer aanpassing of configuratie vereist is, moet u een analyzer-constructie toevoegen aan een index. Zodra u deze hebt gedefinieerd, u de velddefinitie toevoegen zoals in het vorige voorbeeld is aangetoond.

Een [object CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) maken. Zie [CustomAnalyzerTests.cs voor](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Microsoft.Azure.Search/tests/Tests/CustomAnalyzerTests.cs)meer voorbeelden.

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

+ Bekijk onze uitgebreide uitleg over [hoe zoeken in volledige tekst werkt in Azure Cognitive Search.](search-lucene-query-architecture.md) In dit artikel worden voorbeelden gebruikt om gedragingen uit te leggen die op het oppervlak mogelijk contra-intuïtief lijken.

+ Probeer aanvullende querysyntaxis uit de sectie [Voorbeeld van zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) of uit [De syntaxis van eenvoudige query's](query-simple-syntax.md) in Zoekverkenner in de portal.

+ Leer hoe u [taalspecifieke lexicale analysers](index-add-language-analyzers.md)toepast.

+ [Configureer aangepaste analyseapparaten](index-add-custom-analyzers.md) voor minimale verwerking of gespecialiseerde verwerking op afzonderlijke velden.

## <a name="see-also"></a>Zie ook

 [REST API voor documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [Vereenvoudigde querysyntaxis](query-simple-syntax.md) 

 [Volledige Lucene-querysyntaxis](query-lucene-syntax.md) 
 
 [Zoekresultaten verwerken](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
