---
title: Aangepaste analysers toevoegen aan tekenreeksvelden
titleSuffix: Azure Cognitive Search
description: Teksttokenisators en tekenfilters configureren die worden gebruikt in query's met volledige tekst in Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 9bf0fb1a33a98031a78155a3956ac6d6abe33029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113627"
---
# <a name="add-custom-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Aangepaste analysers toevoegen aan tekenreeksvelden in een Azure Cognitive Search-index

Een *aangepaste analyzer* is een specifiek type [tekstanalyzer](search-analyzers.md) dat bestaat uit een door de gebruiker gedefinieerde combinatie van bestaande tokenizer en optionele filters. Door tokenizers en filters op nieuwe manieren te combineren, u tekstverwerking in de zoekmachine aanpassen om specifieke resultaten te bereiken. U bijvoorbeeld een aangepaste analyzer maken met een *charfilter* om HTML-markeringen te verwijderen voordat tekstinvoer wordt getokeniseerd.

 U meerdere aangepaste analysers definiëren om de combinatie van filters te variëren, maar elk veld kan slechts één analyzer gebruiken voor indexeringsanalyse en één voor zoekanalyse. Zie Voorbeeld van [aangepaste analyzer](search-analyzers.md#Custom-analyzer-example)voor een illustratie van hoe een klantanalyzer eruit ziet.

## <a name="overview"></a>Overzicht

 De rol van een [full-text zoekmachine,](search-lucene-query-architecture.md)in eenvoudige termen, is het verwerken en opslaan van documenten op een manier die het mogelijk maakt efficiënt query's en ophalen. Op een hoog niveau komt het allemaal neer op het extraheren van belangrijke woorden uit documenten, ze in een index plaatsen en vervolgens de index gebruiken om documenten te vinden die overeenkomen met woorden van een bepaalde query. Het proces van het extraheren van woorden uit documenten en zoekopdrachten wordt *lexicale analyse*genoemd. Componenten die lexicale analyse uitvoeren, worden *analysators*genoemd.

 In Azure Cognitive Search u kiezen uit een set vooraf gedefinieerde taalagnostische analysatoren in de tabel [Analyzers](#AnalyzerTable) of taalspecifieke analysers die worden vermeld in [Taalanalysers &#40;Azure Cognitive Search-service REST API&#41;](index-add-language-analyzers.md). Je hebt ook een optie om je eigen aangepaste analysers te definiëren.  

 Met een aangepaste analyzer u de controle over het proces van het omzetten van tekst in indexeerbare en doorzoekbare tokens overnemen. Het is een door de gebruiker gedefinieerde configuratie die bestaat uit één vooraf gedefinieerde tokenizer, een of meer tokenfilters en een of meer char-filters. De tokenizer is verantwoordelijk voor het breken van tekst in tokens en de tokenfilters voor het wijzigen van tokens die worden uitgezonden door de tokenizer. Char filters worden toegepast voor het voorbereiden van invoertekst voordat deze wordt verwerkt door de tokenizer. Char-filter kan bijvoorbeeld bepaalde tekens of symbolen vervangen.

 Populaire scenario's ingeschakeld door aangepaste analysers zijn:  

- Fonetische zoektocht. Voeg een fonetisch filter toe om zoeken mogelijk te maken op basis van hoe een woord klinkt, niet hoe het wordt gespeld.  

- Lexicale analyse uitschakelen. Gebruik de trefwoordanalyzer om doorzoekbare velden te maken die niet zijn geanalyseerd.  

- Snel voorvoegsel/achtervoegsel zoeken. Voeg het tokenfilter Edge N-gram toe om voorvoegsels van woorden te indexeren om snelle voorvoegselmatching in te schakelen. Combineer het met het reverse token filter om achtervoegsel matching te doen.  

- Aangepaste tokenization. Gebruik bijvoorbeeld de Whitespace-tokenizer om zinnen in tokens op te splitsen met witruimte als scheidingsteken  

- ASCII vouwen. Voeg het standaard ASCII vouwfilter toe om diacritics zoals ö of ê in zoektermen te normaliseren.  

  Deze pagina bevat een lijst met ondersteunde analysers, tokenizers, tokenfilters en charfilters. U ook een beschrijving vinden van wijzigingen in de indexdefinitie met een gebruiksvoorbeeld. Zie [Analysepakketsamenvatting (Lucene)](https://lucene.apache.org/core/6_0_0/core/org/apache/lucene/codecs/lucene60/package-summary.html)voor meer achtergrondinformatie over de onderliggende technologie die wordt gebruikt in de Azure Cognitive Search-implementatie. Zie [Analysers toevoegen in Azure Cognitive Search](search-analyzers.md#examples)voor voorbeelden van analyseconfiguraties.

## <a name="validation-rules"></a>Validatieregels  
 Namen van analysers, tokenizers, tokenfilters en charfilters moeten uniek zijn en kunnen niet hetzelfde zijn als een van de vooraf gedefinieerde analyzers, tokenizers, tokenfilters of charfilters. Zie de [eigenschappenreferentie](#PropertyReference) voor namen die al in gebruik zijn.

## <a name="create-custom-analyzers"></a>Aangepaste analysers maken
 U aangepaste analysers definiëren op het moment van indexcreatie. De syntaxis voor het opgeven van een aangepaste analyzer wordt in deze sectie beschreven. U zich ook vertrouwd maken met de syntaxis door voorbeelddefinities te bekijken in [Analysers toevoegen in Azure Cognitive Search](search-analyzers.md#examples).  

 Een analysedefinitie bevat een naam, een type, een of meer charfilters, maximaal één tokenizer en een of meer tokenfilters voor verwerking na tokenisatie. Char-filers worden toegepast vóór tokenisatie. Tokenfilters en charfilters worden van links naar rechts toegepast.

 Het `tokenizer_name` is de naam van `token_filter_name_1` `token_filter_name_2` een tokenizer, en `char_filter_name_1` zijn `char_filter_name_2` de namen van tokenfilters, en en zijn de namen van char filters (zie de [Tokenizers](#Tokenizers), [Token filters](#TokenFilters) en Char filters tabellen voor geldige waarden).

De definitie van de analysator maakt deel uit van de grotere index. Zie [Index-API maken](https://docs.microsoft.com/rest/api/searchservice/create-index) voor informatie over de rest van de index.

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  Aangepaste analysers die u maakt, worden niet weergegeven in de Azure-portal. De enige manier om een aangepaste analyzer toe te voegen is door middel van code die aanzet tot de API bij het definiëren van een index.  

 Binnen een indexdefinitie u deze sectie overal in het hoofd van een indexaanvraag maken plaatsen, maar meestal gaat het aan het einde:  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

Definities voor charfilters, tokenizers en tokenfilters worden alleen aan de index toegevoegd als u aangepaste opties instelt. Als u een bestaand filter of tokenizer as-is wilt gebruiken, geeft u deze op naam op in de definitie van de analysator.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>Aangepaste analysers testen

U de **testanalyzer-bewerking** in de [REST-API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) gebruiken om te zien hoe een analyzer bepaalde tekst in tokens breekt.

**Aanvraag**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**Reactie**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>Aangepaste analysers bijwerken

Zodra een analyzer, een tokenizer, een tokenfilter of een charfilter is gedefinieerd, kan deze niet worden gewijzigd. Nieuwe kunnen alleen aan een bestaande `allowIndexDowntime` index worden toegevoegd als de vlag is ingesteld op true in de aanvraag voor indexupdate:

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

Deze bewerking haalt uw index gedurende ten minste enkele seconden offline, waardoor uw indexerings- en queryaanvragen mislukken. Prestaties en schrijfbeschikbaarheid van de index kunnen enkele minuten worden aangetast nadat de index is bijgewerkt, of langer voor zeer grote indexen, maar deze effecten zijn tijdelijk en uiteindelijk op te lossen op hun eigen.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>Analyseer-verwijzing

In de onderstaande tabellen worden de configuratie-eigenschappen voor de analysators, tokenizers, tokenfilters en het gedeelte charfilter van een indexdefinitie weergegeven. De structuur van een analyzer, tokenizer of filter in uw index bestaat uit deze kenmerken. Zie de [eigenschappenreferentie](#PropertyReference)voor informatie over waardetoewijzing .

### <a name="analyzers"></a>Analyses

Voor analysators zijn indexkenmerken afhankelijk van de vraag of u vooraf gedefinieerde of aangepaste analysatoren gebruikt.

#### <a name="predefined-analyzers"></a>Vooraf gedefinieerde analysatoren

|||  
|-|-|  
|Name|Het mag alleen letters, cijfers, spaties, streepjes of underscores bevatten, kan alleen beginnen en eindigen met alfanumerieke tekens en is beperkt tot 128 tekens.|  
|Type|Analyzer type uit de lijst met ondersteunde analyzers. Zie de **analyzer_type** kolom in de tabel [Analyseers](#AnalyzerTable) hieronder.|  
|Opties|Moet geldige opties zijn van een vooraf gedefinieerde analyzer die wordt vermeld in de [onderstaande tabel Analyzers.](#AnalyzerTable)|  

#### <a name="custom-analyzers"></a>Aangepaste analysesystemen

|||  
|-|-|  
|Name|Het mag alleen letters, cijfers, spaties, streepjes of underscores bevatten, kan alleen beginnen en eindigen met alfanumerieke tekens en is beperkt tot 128 tekens.|  
|Type|Moet "#Microsoft.Azure.Search.CustomAnalyzer" zijn.|  
|CharFilters (CharFilters)|Stel in op een van de vooraf gedefinieerde charfilters die worden vermeld in de tabel [Char-filters](#char-filters-reference) of een aangepast char-filter dat is opgegeven in de indexdefinitie.|  
|Tokenizer (Tokenizer)|Vereist. Stel in op een van de vooraf gedefinieerde tokenizers die worden vermeld in de [onderstaande tokenizers-tabel](#Tokenizers) of een aangepaste tokenizer die is opgegeven in de indexdefinitie.|  
|Tokenfilters|Stel in op een van de vooraf gedefinieerde tokenfilters die worden weergegeven in de tabel [Tokenfilters](#TokenFilters) of een aangepast tokenfilter dat is opgegeven in de indexdefinitie.|  

> [!NOTE]
> Het is vereist dat u uw aangepaste analyzer configureert om geen tokens te produceren die langer zijn dan 300 tekens. Indexeren mislukt voor documenten met dergelijke tokens. Als u ze wilt bijsnijden of negeren, gebruikt u respectievelijk het **TruncateTokenFilter** en het **LengthTokenFilter.**  Controleer [**Tokenfilters**](#TokenFilters) op referentie.

<a name="CharFilter"></a>

### <a name="char-filters"></a>Char-filters

 Een char filter wordt gebruikt om invoertekst voor te bereiden voordat deze wordt verwerkt door de tokenizer. Ze kunnen bijvoorbeeld bepaalde tekens of symbolen vervangen. U meerdere charfilters in een aangepaste analyzer hebben. Char filters worden uitgevoerd in de volgorde waarin ze worden vermeld.  

|||  
|-|-|  
|Name|Het mag alleen letters, cijfers, spaties, streepjes of underscores bevatten, kan alleen beginnen en eindigen met alfanumerieke tekens en is beperkt tot 128 tekens.|  
|Type|Type Char-filter uit de lijst met ondersteunde charfilters. Zie **char_filter_type** kolom in de tabel [Char Filters](#char-filters-reference) hieronder.|  
|Opties|Moet geldige opties van een bepaald [type Char-filters](#char-filters-reference) zijn.|  

### <a name="tokenizers"></a>Tokenisators

 Een tokenizer verdeelt continue tekst in een reeks tokens, zoals het breken van een zin in woorden.  

 U precies één tokenizer per aangepaste analyzer opgeven. Als u meer dan één tokenizer nodig hebt, u meerdere aangepaste analysers maken en deze veld voor veld toewijzen aan uw indexschema.  
Een aangepaste analyzer kan een vooraf gedefinieerde tokenizer gebruiken met standaard- of aangepaste opties.  

|||  
|-|-|  
|Name|Het mag alleen letters, cijfers, spaties, streepjes of underscores bevatten, kan alleen beginnen en eindigen met alfanumerieke tekens en is beperkt tot 128 tekens.|  
|Type|Tokenizer-naam uit de lijst met ondersteunde tokenizers. Zie **tokenizer_type** kolom in de tabel [Tokenizers](#Tokenizers) hieronder.|  
|Opties|Moet geldige opties van een bepaald tokenizertype zijn dat wordt vermeld in de [onderstaande tokenizers-tabel.](#Tokenizers)|  

### <a name="token-filters"></a>Tokenfilters

 Een tokenfilter wordt gebruikt om de tokens die door een tokenizer worden gegenereerd, eruit te filteren of te wijzigen. U bijvoorbeeld een kleine lettersfilter opgeven waarmee alle tekens worden omgezet in kleine letters.   
U meerdere tokenfilters in een aangepaste analyzer hebben. Tokenfilters worden uitgevoerd in de volgorde waarin ze worden weergegeven.  

|||  
|-|-|  
|Name|Het mag alleen letters, cijfers, spaties, streepjes of underscores bevatten, kan alleen beginnen en eindigen met alfanumerieke tekens en is beperkt tot 128 tekens.|  
|Type|Tokenfilternaam uit de lijst met ondersteunde tokenfilters. Zie **token_filter_type** kolom in de tabel [Tokenfilters](#TokenFilters) hieronder.|  
|Opties|Moet [Token filters](#TokenFilters) van een bepaald token filtertype.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>Eigenschappenreferentie

In deze sectie worden de geldige waarden opgegeven voor kenmerken die zijn opgegeven in de definitie van een aangepaste analyzer, tokenizer, charfilter of tokenfilter in uw index. Analyzers, tokenizers en filters die worden geïmplementeerd met Apache Lucene hebben koppelingen naar de Lucene API-documentatie.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>Referentie voor vooraf gedefinieerde analysators

|**analyzer_name**|**analyzer_type**  <sup>1.</sup>|**Beschrijving en opties**|  
|-|-|-|  
|[Trefwoord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (type is alleen van toepassing wanneer er opties beschikbaar zijn) |Behandelt de volledige inhoud van een veld als één token. Dit is handig voor gegevens zoals postcodes, id's en sommige productnamen.|  
|[Patroon](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|Scheidt tekst flexibel in termen via een regulier expressiepatroon.<br /><br /> **Opties**<br /><br /> kleine letters (type: bool) - Bepaalt of termen worden verlaagd. De standaardinstelling is waar.<br /><br /> [patroon](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (type: tekenreeks) - Een regelmatig expressiepatroon dat overeenkomt met tokenscheidingen. De standaardinstelling is \w+.<br /><br /> [vlaggen](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (type: tekenreeks) - Vlaggetjes met reguliere expressie. De standaardinstelling is een lege tekenreeks. Toegestane waarden: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (type: tekenreeksarray) - Een lijst met stopwoorden. De standaardinstelling is een lege lijst.|  
|[Eenvoudige](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn) |Verdeelt tekst bij niet-letters en zet ze om in kleine letters. |  
|[Standaard](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(Ook wel aangeduid als standard.lucene)|StandardAnalyzer (StandardAnalyzer)|Standaard Lucene analyzer, samengesteld uit de standaard tokenizer, kleine letters filter, en stop filter.<br /><br /> **Opties**<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte. De standaardinstelling is 255. Tokens langer dan de maximale lengte worden gesplitst. De maximale tokenlengte die kan worden gebruikt, is 300 tekens.<br /><br /> stopwords (type: tekenreeksarray) - Een lijst met stopwoorden. De standaardinstelling is een lege lijst.|  
|standardasciifolding.lucene|(type is alleen van toepassing wanneer er opties beschikbaar zijn) |Standaard analyzer met Ascii vouwfilter. |  
|[Stoppen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|Verdeelt tekst bij niet-letters, past de kleine letters en stopword token filters.<br /><br /> **Opties**<br /><br /> stopwords (type: tekenreeksarray) - Een lijst met stopwoorden. De standaardinstelling is een vooraf gedefinieerde lijst voor Engels. |  
|[Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn) |Een analyzer die de whitespace tokenizer gebruikt. Tokens die langer zijn dan 255 tekens worden gesplitst.|  

 <sup>1</sup> Analyzer-typen zijn altijd vooraf vastgelegd in code met "#Microsoft.Azure.Search" zodat "PatternAnalyzer" daadwerkelijk zou worden opgegeven als "#Microsoft.Azure.Search.PatternAnalyzer". We hebben het voorvoegsel verwijderd voor beknoptheid, maar het voorvoegsel is vereist in uw code. 
 
De analyzer_type is alleen beschikbaar voor analysers die kunnen worden aangepast. Als er geen opties zijn, zoals het geval is met de trefwoordanalyzer, is er geen gekoppeld #Microsoft.Azure.Search-type.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>Referentie voor charfilters

In de onderstaande tabel zijn de tekenfilters die met Apache Lucene worden geïmplementeerd gekoppeld aan de Lucene API-documentatie.

|**char_filter_name**|**char_filter_type** <sup>1.</sup>|**Beschrijving en opties**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Een char filter dat probeert te strippen HTML-constructies.|  
|[mapping](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|Een char-filter dat toewijzingen toepast die zijn gedefinieerd met de optie toewijzingen. Matching is hebzuchtig (langste patroon matching op een bepaald punt wint). Vervanging is toegestaan om de lege string.<br /><br /> **Opties**<br /><br /> toewijzingen (type: tekenreeksarray) - Een lijst met toewijzingen van de volgende indeling: "a=>b" (alle exemplaren van het teken "a" worden vervangen door teken "b"). Vereist.|  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatroonReplaceCharFilter|Een charfilter dat tekens in de invoertekenreeks vervangt. Het maakt gebruik van een reguliere expressie om tekensequenties te identificeren om te behouden en een vervangend patroon om tekens te identificeren om te vervangen. Bijvoorbeeld invoertekst = "aa bb aa bb", pattern="(aa)\\\s+(bb)" replacement="$1#$2", resultaat = "aa#bb aa#bb".<br /><br /> **Opties**<br /><br /> patroon (type: tekenreeks) - Vereist.<br /><br /> vervanging (type: tekenreeks) - Vereist.|  

 <sup>1</sup> Char-filtertypen zijn altijd vooraf bevestigd in code met "#Microsoft.Azure.Search" zodanig dat "MappingCharFilter" daadwerkelijk zou worden opgegeven als "#Microsoft.Azure.Search.MappingCharFilter. We hebben het voorvoegsel verwijderd om de breedte van de tabel te verkleinen, maar vergeet niet om het in uw code op te nemen. Merk op dat char_filter_type alleen is beschikbaar voor filters die kunnen worden aangepast. Als er geen opties zijn, zoals bij html_strip, is er geen gekoppeld #Microsoft.Azure.Search-type.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>Tokenizers-referentie

In de onderstaande tabel zijn de tokenizers die worden geïmplementeerd met Apache Lucene gekoppeld aan de Lucene API-documentatie.

|**tokenizer_name**|**tokenizer_type** <sup>1.</sup>|**Beschrijving en opties**|  
|-|-|-|  
|[Klassieke](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer (ClassicTokenizer)|Grammaticagebaseerde tokenizer die geschikt is voor het verwerken van de meeste Europese documenten.<br /><br /> **Opties**<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte. Standaard: 255, maximaal: 300. Tokens langer dan de maximale lengte worden gesplitst.|  
|[edgeNGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|Tokenizes de invoer van een rand in n-gram van bepaalde grootte(en).<br /><br /> **Opties**<br /><br /> minGram (type: int) - Standaard: 1, maximum: 300.<br /><br /> maxGram (type: int) - Standaard: 2, maximum: 300. Moet groter zijn dan minGram.<br /><br /> tokenChars (type: string array) - Tekenklassen om in de tokens te bewaren. Toegestane waarden: <br />"letter", "cijfer", "witruimte", "interpunctie", "symbool". Standaard instellingen voor een lege array - houdt alle tekens. |  
|[keyword_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|Zendt de volledige invoer uit als één token.<br /><br /> **Opties**<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte. Standaard: 256, maximaal: 300. Tokens langer dan de maximale lengte worden gesplitst.|  
|[Brief](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Verdeelt tekst bij niet-letters. Tokens die langer zijn dan 255 tekens worden gesplitst.|  
|[Kleine letters](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Verdeelt tekst bij niet-letters en zet ze om in kleine letters. Tokens die langer zijn dan 255 tekens worden gesplitst.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| Verdeelt tekst met taalspecifieke regels.<br /><br /> **Opties**<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte, standaard: 255, maximum: 300. Tokens langer dan de maximale lengte worden gesplitst. Tokens langer dan 300 tekens worden eerst opgesplitst in tokens van lengte 300 en vervolgens wordt elk van deze tokens gesplitst op basis van de maxTokenLengte-set.<br /><br />isSearchTokenizer (type: bool) - Stel in op true als deze wordt gebruikt als zoektokenizer, ingesteld op false indien gebruikt als indexeringtokenizer. <br /><br /> taal (type: tekenreeks) - Taal te gebruiken, standaard "Engels". Toegestane waarden zijn:<br />"bangla", "Bulgaars", "Catalisal", "chineesVereenvoudigd", "chineesTraditioneel", "Kroatisch", "Tsjechisch", "Deens", "Engels", "Frans", "Duits", "Grieks", "gujarati", "hindi", "iJslands", "Indonesisch", "Italiaans", "Japans", "kannada", " Koreaans", "maleis", "malayalam", "marathi", "norwegianBokmaal", "polish", "portugees", "portugees Braziliaans", "portugees", "Roemeens", "Russisch", "servischCyrillisch", "servischLatijn", "Sloveens", "Spaans", "Zweeds", "tamil", "telugu", "Thai", " Oekraïens", "urdu", "Vietnamees" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| Verdeelt tekst met taalspecifieke regels en reduceert woorden tot hun basisvormen<br /><br /> **Opties**<br /><br />maxTokenLengte (type: int) - De maximale tokenlengte, standaard: 255, maximum: 300. Tokens langer dan de maximale lengte worden gesplitst. Tokens langer dan 300 tekens worden eerst opgesplitst in tokens van lengte 300 en vervolgens wordt elk van deze tokens gesplitst op basis van de maxTokenLengte-set.<br /><br /> isSearchTokenizer (type: bool) - Stel in op true als deze wordt gebruikt als zoektokenizer, ingesteld op false indien gebruikt als indexeringtokenizer.<br /><br /> taal (type: tekenreeks) - Taal te gebruiken, standaard "Engels". Toegestane waarden zijn:<br />"Arabisch", "bangla", "Bulgaars", "Catalisal", "Kroatisch", "Tsjechisch", "Deens", "Engels", "Ests", "Fins", "Frans", "Grieks", "gujarati", "hebreeuws", "hindi", "Hongaars", "IJslands", "Indonesisch", "Italiaans", "kannada", " Lets", "Litouws", "maleis", "malayalam", "marathi", "norwegianBokmaal", "polish", "portugees Braziliaans", "punjabi", "russisch", "servischCyrillisch", "servischLatijn", "slowaaks", "Sloveens", "Spaans", "Zweeds", " tamil", "telugu", "Turks", "Oekraïens", "urdu" |
|[nGram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|Tokenizes de invoer in n-gram men van de gegeven grootte(en).<br /><br /> **Opties**<br /><br /> minGram (type: int) - Standaard: 1, maximum: 300.<br /><br /> maxGram (type: int) - Standaard: 2, maximum: 300. Moet groter zijn dan minGram. <br /><br /> tokenChars (type: string array) - Tekenklassen om in de tokens te bewaren. Toegestane waarden: "letter", "cijfer", "witruimte", "interpunctie", "symbool". Standaard instellingen voor een lege array - houdt alle tekens. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|Tokenizer voor padachtige hiërarchieën.<br /><br /> **Opties**<br /><br /> delimiter (type: tekenreeks) - Standaard: '/.<br /><br /> vervanging (type: tekenreeks) - Als u deze optie instelt, vervangt u het tekensteken van de scheiding. Standaard hetzelfde als de waarde van de scheidingsteken.<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte. Standaard: 300, maximaal: 300. Paden langer dan maxTokenLengte worden genegeerd.<br /><br /> reverse (type: bool) - Als het waar is, genereert token in omgekeerde volgorde. Standaard: onwaar.<br /><br /> overslaan (type: bool) - Eerste tokens om over te slaan. De standaardwaarde is 0.|  
|[Patroon](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|Deze tokenizer maakt gebruik van regex patroon matching om verschillende tokens te construeren.<br /><br /> **Opties**<br /><br /> [patroon](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html) (type: tekenreeks) - Normaal expressiepatroon. De standaardinstelling is \W+. <br /><br /> [vlaggen](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (type: tekenreeks) - Vlaggetjes met reguliere expressie. De standaardinstelling is een lege tekenreeks. Toegestane waarden: CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> groep (type: int) - Welke groep te extraheren in tokens. De standaardinstelling is -1 (gesplitst).|
|[standard_v2](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|Hiermee wordt tekst afgebroken volgens de [regels voor Unicode-tekstsegmentatie](https://unicode.org/reports/tr29/).<br /><br /> **Opties**<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte. Standaard: 255, maximaal: 300. Tokens langer dan de maximale lengte worden gesplitst.|  
|[uax_url_email](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|Tokenizes url's en e-mails als één token.<br /><br /> **Opties**<br /><br /> maxTokenLengte (type: int) - De maximale tokenlengte. Standaard: 255, maximaal: 300. Tokens langer dan de maximale lengte worden gesplitst.|  
|[Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn) |Verdeelt tekst in witruimte. Tokens die langer zijn dan 255 tekens worden gesplitst.|  

 <sup>1</sup> Tokenizer-typen zijn altijd vooraf bevestigd in code met "#Microsoft.Azure.Search" zodanig dat "ClassicTokenizer" daadwerkelijk zou worden opgegeven als "#Microsoft.Azure.Search.ClassicTokenizer". We hebben het voorvoegsel verwijderd om de breedte van de tabel te verkleinen, maar vergeet niet om het in uw code op te nemen. Merk op dat tokenizer_type alleen is voorzien voor tokenizers die kunnen worden aangepast. Als er geen opties zijn, zoals het geval is met de lettertokenizer, is er geen gekoppeld#Microsoft.Azure.Zoektype.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>Naslaginformatie over tokenfilters

In de onderstaande tabel zijn de tokenfilters die met Apache Lucene worden geïmplementeerd gekoppeld aan de Lucene API-documentatie.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**Beschrijving en opties**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Een tokenfilter dat de Arabische normalisator toepast om de orthografie te normaliseren.|  
|[Apostrof](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Strips alle tekens na een apostrof (met inbegrip van de apostrof zelf). |  
|[asciifolding asciifolding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|Converteert alfabetische, numerieke en symbolische Unicode-tekens die niet in de eerste 127 ASCII-tekens (het Unicode-blok 'BasisLatijn' staan) in hun ASCII-equivalenten, als er een bestaat.<br /><br /> **Opties**<br /><br /> preserveOriginal (type: bool) - Als het waar is, wordt het oorspronkelijke token bewaard. De standaardinstelling is onwaar.|  
|[cjk_bigram](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|Vormt bigrams van CJK-termen die worden gegenereerd vanuit StandardTokenizer.<br /><br /> **Opties**<br /><br /> ignoreScripts (type: tekenreeksarray) - Scripts die u moet negeren. Toegestane waarden zijn: "han", "hiragana", "katakana", "hangul". De standaardinstelling is een lege lijst.<br /><br /> outputUnigrammen (type: bool) - Stel in als je altijd zowel unigrammen als bigrammen wilt output. De standaardinstelling is onwaar.|  
|[cjk_width](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Normaliseert CJK breedteverschillen. Vouwt ascii-varianten over de volledige breedte in de gelijkwaardige latijnse en halve breedte Katakana-varianten in de equivalente kana. |  
|[Klassieke](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Verwijdert de Engelse bezitters, en stippen uit afkortingen. |  
|[common_grams](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|Construct bigrams voor vaak voorkomende termen tijdens het indexeren. Enkele termen zijn nog steeds geïndexeerd ook, met bigrammen bedekt.<br /><br /> **Opties**<br /><br /> commonWords (type: tekenreeksarray) - De set algemene woorden. De standaardinstelling is een lege lijst. Vereist.<br /><br /> ignoreCase (type: bool) - Als het waar is, is matching geval ongevoelig. De standaardinstelling is onwaar.<br /><br /> queryMode (type: bool) - Genereert bigrammen verwijdert vervolgens veelvoorkomende woorden en enkele termen, gevolgd door een gemeenschappelijk woord. De standaardinstelling is onwaar.|  
|[dictionary_decompounder](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|WoordenboekDecompounderTokenFilter|Ontleedt samengestelde woorden gevonden in vele Germaanse talen.<br /><br /> **Opties**<br /><br /> wordList (type: tekenreeksarray) - De lijst met woorden waartegen moet worden aangekoppeld. De standaardinstelling is een lege lijst. Vereist.<br /><br /> minWordSize (type: int) - Alleen woorden langer dan dit worden verwerkt. De standaardinstelling is 5.<br /><br /> minSubwordSize (type: int) - Alleen subwoorden langer dan dit worden uitgevoerd. De standaardinstelling is 2.<br /><br /> maxSubwordSize (type: int) - Alleen subwoorden korter dan deze worden uitgevoerd. De standaardwaarde is 15.<br /><br /> onlyLongestMatch (type: bool) - Voeg alleen de langste overeenkomende subwoord aan de uitvoer. De standaardinstelling is onwaar.|  
|[edgeNGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgenGramTokenFilterV2|Genereert n-gram men van de opgegeven grootte(en) vanaf de voorkant of de achterkant van een invoertoken.<br /><br /> **Opties**<br /><br /> minGram (type: int) - Standaard: 1, maximum: 300.<br /><br /> maxGram (type: int) - Standaard: 2, maximaal 300. Moet groter zijn dan minGram.<br /><br /> kant (type: tekenreeks) - Hiermee geeft u aan uit welke kant van de invoer de n-gram moet worden gegenereerd. Toegestane waarden: "front", "terug" |  
|[elision](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|Verwijdert elisions. 'l'avion' (het vlak) wordt bijvoorbeeld omgezet in 'avion' (vlak).<br /><br /> **Opties**<br /><br /> artikelen (type: tekenreeksarray) - Een reeks artikelen die u wilt verwijderen. De standaardinstelling is een lege lijst. Als er geen lijst met artikelen is ingesteld, worden standaard alle Franse artikelen verwijderd.|  
|[german_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Normaliseert Duitse karakters volgens de heuristiek van het [German2 sneeuwbalalgoritme.](https://snowballstem.org/algorithms/german2/stemmer.html)|  
|[hindi_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Normaliseert tekst in het Hindi om enkele verschillen in spellingvariaties te verwijderen. |  
|[indic_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalisatieTokenFilter|Normaliseert de Unicode-weergave van tekst in Indiase talen.
|[Houden](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|Een tokenfilter dat alleen tokens met tekst in een opgegeven lijst met woorden bewaart.<br /><br /> **Opties**<br /><br /> keepWords (type: tekenreeksarray) - Een lijst met woorden die u wilt behouden. De standaardinstelling is een lege lijst. Vereist.<br /><br /> keepWordsCase (type: bool) - Als waar, kleine letters alle woorden eerst. De standaardinstelling is onwaar.|  
|[keyword_marker](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|Hiermee markeert u termen als trefwoorden.<br /><br /> **Opties**<br /><br /> trefwoorden (type: tekenreeksarray) - Een lijst met woorden die u als trefwoorden wilt markeren. De standaardinstelling is een lege lijst. Vereist.<br /><br /> ignoreCase (type: bool) - Als het waar is, worden eerst alle woorden in kleine letters. De standaardinstelling is onwaar.|  
|[keyword_repeat](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Zendt elk binnenkomend token twee keer uit als trefwoord en één keer als niet-trefwoord. |  
|[kstem kstem kstem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Een high-performance kstem filter voor Engels. |  
|[Lengte](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengteTokenFilter|Hiermee verwijdert u woorden die te lang of te kort zijn.<br /><br /> **Opties**<br /><br /> min (type: int) - Het minimumaantal. Standaard: 0, maximum: 300.<br /><br /> max (type: int) - Het maximumaantal. Standaard: 300, maximaal: 300.|  
|[Beperken](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|Beperkt het aantal tokens tijdens het indexeren.<br /><br /> **Opties**<br /><br /> maxTokenCount (type: int) - Maximum aantal tokens om te produceren. De standaardwaarde is 1.<br /><br /> consumeErenAllTokens (type: bool) - Of alle tokens van de invoer moeten worden verbruikt, zelfs als maxTokenCount is bereikt. De standaardinstelling is onwaar.|  
|[Kleine letters](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Normaliseert tokentekst naar kleine letters. |  
|[nGram_v2](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|Genereert n-gram men van de gegeven grootte(en).<br /><br /> **Opties**<br /><br /> minGram (type: int) - Standaard: 1, maximum: 300.<br /><br /> maxGram (type: int) - Standaard: 2, maximaal 300. Moet groter zijn dan minGram.|  
|[pattern_capture](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Gebruikt Java regexes om meerdere tokens uit te zenden, één voor elke groep vastleggen in een of meer patronen.<br /><br /> **Opties**<br /><br /> patronen (type: tekenreeksarray) - Een lijst met patronen die overeenkomen met elk token. Vereist.<br /><br /> preserveOriginal (type: bool) - Stel het oorspronkelijke token in om het oorspronkelijke token terug te geven, zelfs als een van de patronen overeenkomt, standaard: true: true |  
|[pattern_replace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|Een tokenfilter dat een patroon toepast op elk token in de stream, waarbij overeenkomenmet gebeurtenissen wordt vervangen door de opgegeven vervangingstekenreeks.<br /><br /> **Opties**<br /><br /> patroon (type: tekenreeks) - Vereist.<br /><br /> vervanging (type: tekenreeks) - Vereist.|  
|[persian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn) |Past normalisatie voor Perzisch. |  
|[Fonetische](https://lucene.apache.org/core/6_6_1/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|FoneticTokenFilter|Maak tokens voor fonetische overeenkomsten.<br /><br /> **Opties**<br /><br /> encoder (type: string) - Fonetische encoder te gebruiken. Toegestane waarden zijn: "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse". Standaard: "metaphone". Standaard is metafoon.<br /><br /> Zie [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html) voor meer informatie.<br /><br /> vervangen (type: bool) - True als gecodeerde tokens originele tokens moeten vervangen, false als ze als synoniemen moeten worden toegevoegd. De standaardinstelling is waar.|  
|[porter_stem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Hiermee transformeert u de tokenstream volgens het [Porter-stemmingsalgoritme.](https://tartarus.org/~martin/PorterStemmer/) |  
|[Omgekeerde](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Hiermee keert u de tekenreeks om. |  
|[scandinavian_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Normaliseert het gebruik van de verwisselbare Scandinavische karakters. |  
|[scandinavian_folding](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Vouwt Scandinavische personages åÅäæÄÆ->a en öÖøØ->o. Het discrimineert ook tegen het gebruik van dubbele klinkers aa, ae, ao, oe en oo, waardoor alleen de eerste. |  
|[Grind](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter ShingleTokenFilter|Hiermee maakt u combinaties van tokens als één token.<br /><br /> **Opties**<br /><br /> maxShingleSize (type: int) - Standaard ingesteld op 2.<br /><br /> minShingleSize (type: int) - Standaard naar 2.<br /><br /> outputUnigrammen (type: bool) - indien waar, bevat de uitvoerstroom de invoertokens (unigrammen) en shingles. De standaardinstelling is waar.<br /><br /> outputUnigramsIfNoShingles (type: bool) - Indien waar, overschrijven het gedrag van outputUnigrams ==vals voor die momenten waarop er geen gordelroos beschikbaar zijn. De standaardinstelling is onwaar.<br /><br /> tokenSeparator (type: tekenreeks) - De tekenreeks die u moet gebruiken bij het samenvoegen van aangrenzende tokens om een shingle te vormen. De standaardinstelling is " ".<br /><br /> filterToken (type: tekenreeks) - De tekenreeks die moet worden ingevoegd voor elke positie waarop er geen token is. De standaardinstelling is "_".|  
|[Snowball](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|Snowball Token Filter.<br /><br /> **Opties**<br /><br /> taal (type: string) - Toegestane waarden zijn: "Armeens", "Baskisch", "Catalaans", "Deens", "Engels", "Fins", "Frans", "Duits", "Duits", "Hongaars", "Italiaans", "kp", "lovins", "Noors", "porter", "Portugees", "Roemeens", " Russisch", "Spaans", "Zweeds", "Turks"|  
|[sorani_normalization](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalisatieTokenFilter|Normaliseert de Unicode-weergave van Sorani-tekst.<br /><br /> **Opties**<br /><br /> Geen.|  
|stemmer|StemmerTokenFilter|Taalspecifiek terugstemmingsfilter.<br /><br /> **Opties**<br /><br /> taal (type: tekenreeks) - Toegestane waarden zijn: <br /> -   ["Arabisch"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["Armeense"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["Baskische"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["Braziliaanse"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />- "Bulgaars"<br />-   ["Catalaanse"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["Tsjechische"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["Deens"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["Nederlands"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["Engels"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lichtEngels"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimaalEngels"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["bezitterigEngels"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["Porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["Fins"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />- "lichtFins"<br />-   ["Frans"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lichtFrans"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimaalFrans"](https://dl.acm.org/citation.cfm?id=318984)<br />- "Galicische"<br />- "minimalGalician"<br />-   ["Duits"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["German2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />- "minimalGerman"<br />-   ["Grieks"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />- "hindi"<br />-   ["Hongaars"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["Indonesische"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["Ierse"](https://snowballstem.org/otherapps/oregan/)<br />-   ["Italiaans"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["Letse"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["Noors"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["Portugees"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortugees"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimaalPortugees"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["PortugeseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["Roemeense"](https://snowballstem.org/otherapps/romanian/)<br />-   ["Russisch"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lichtRussisch"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["Spaans"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpaans"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["Zweeds"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />- "lightSwedish"<br />-   ["Turks"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|Alle woorden van woordenboek-Stemmed worden gemarkeerd als trefwoorden, waardoor de keten niet kan worden afgesloten. Moet worden geplaatst voor een stemming filters.<br /><br /> **Opties**<br /><br /> regels (type: tekenreeksarray) - Stemmingsregels in de volgende indeling "woord => stam" bijvoorbeeld "ran => run". De standaardinstelling is een lege lijst.  Vereist.|  
|[stopwords](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|Hiermee verwijdert u stopwoorden uit een tokenstream. Standaard gebruikt het filter een vooraf gedefinieerde stopwoordenlijst voor Engels.<br /><br /> **Opties**<br /><br /> stopwords (type: tekenreeksarray) - Een lijst met stopwoorden. Kan niet worden opgegeven als een stopwordsList is opgegeven.<br /><br /> stopwordsList (type: tekenreeks) - Een vooraf gedefinieerde lijst met stopwoorden. Kan niet worden opgegeven als stopwords is opgegeven. Toegestane waarden zijn:"Arabisch", "Armeens", "Baskisch", "Braziliaans", "Bulgaars", "Catalisch", "Deens", "Engels", "Engels", "Frans", "Galicisch", "Duits", "Grieks", "hindi", "Hongaars", "Indonesisch", "Iers", "Italiaans", "Lets" ", "Noors", "Perzisch", "Portugees", "Roemeens", "Russisch", "sorani", "Spaans", "Zweeds", "Thai", "Turks", standaard: "Engels". Kan niet worden opgegeven als stopwords is opgegeven. <br /><br /> ignoreCase (type: bool) - Als het waar is, worden alle woorden eerst in de kleine letters gevallen. De standaardinstelling is onwaar.<br /><br /> removeTrailing (type: bool) - Als dit waar is, negeert u de laatste zoekterm als het een stopwoord is. De standaardinstelling is waar.
|[Synoniem](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynoniemTokenFilter|Komt overeen met synoniemen van één of meerdere woorden in een tokenstream.<br /><br /> **Opties**<br /><br /> synoniemen (type: tekenreeksarray) - Vereist. Lijst met synoniemen in een van de volgende twee indelingen:<br /><br /> -ongelooflijk, ongelooflijk, fantastisch => geweldig - alle termen aan de linkerkant van => symbool worden vervangen door alle termen aan de rechterkant.<br /><br /> -ongelooflijk, ongelooflijk, geweldig, geweldig - een komma-gescheiden lijst van gelijkwaardige woorden. Stel de optie Uitvouwen in om de manier waarop deze lijst wordt geïnterpreteerd te wijzigen.<br /><br /> ignoreCase (type: bool) - Case-folds input voor matching. De standaardinstelling is onwaar.<br /><br /> uitvouwen (type: bool) - Als dit waar is, worden alle woorden in de lijst met synoniemen (als => notatie niet wordt gebruikt) aan elkaar toegewezen. <br />De volgende lijst: ongelooflijk, ongelooflijk, fantastisch, geweldig is gelijk aan: ongelooflijk, ongelooflijk, fantastisch, geweldig => ongelooflijk, ongelooflijk, fantastisch, geweldig<br /><br />- Indien vals, de volgende lijst: ongelooflijk, ongelooflijk, fantastisch, geweldig zijn gelijk aan: ongelooflijk, ongelooflijk, fantastisch, geweldig => ongelooflijk.|  
|[Trim](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Trims leiden en slepen witruimte van tokens. |  
|[Afkappen](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|AfgekaptTokenfilter|De termen worden ineengetal in een specifieke lengte.<br /><br /> **Opties**<br /><br /> lengte (type: int) - Standaard: 300, maximum: 300. Vereist.|  
|[Unieke](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniekTokenFilter|Hiermee filtert u tokens uit met dezelfde tekst als het vorige token.<br /><br /> **Opties**<br /><br /> onlyOnSamePosition (type: bool) - Als u deze set instelt, verwijdert u duplicaten alleen op dezelfde positie. De standaardinstelling is waar.|  
|[Hoofdletters](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(type is alleen van toepassing wanneer er opties beschikbaar zijn)  |Normaliseert tokentekst naar hoofdletters. |  
|[word_delimiter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|Splitst woorden op in subwoorden en voert optionele transformaties uit op subwoordgroepen.<br /><br /> **Opties**<br /><br /> generateWordParts (type: bool) - Hiermee worden delen van woorden gegenereerd, bijvoorbeeld "AzureSearch" wordt "Azure" "Search". De standaardinstelling is waar.<br /><br /> genererenAantalonderdelen (type: bool) - Hiermee worden getalsubwoorden gegenereerd. De standaardinstelling is waar.<br /><br /> catenateWords (type: bool) - Hiermee wordt maximale oplage van woordonderdelen vercatenated, bijvoorbeeld "Azure-Search" wordt "AzureSearch". De standaardinstelling is onwaar.<br /><br /> catenateNumbers (type: bool) - Zorgt ervoor dat maximale runs van getalonderdelen worden gecateneerd, bijvoorbeeld "1-2" wordt "12". De standaardinstelling is onwaar.<br /><br /> catenateAll (type: bool) - Hiermee worden alle subwoordonderdelen vercateneerd, bijvoorbeeld "Azure-Search-1" wordt "AzureSearch1". De standaardinstelling is onwaar.<br /><br /> splitOnCaseChange (type: bool) - Als true, splitst woorden op caseChange, bijvoorbeeld "AzureSearch" wordt "Azure" "Zoeken". De standaardinstelling is waar.<br /><br /> preserveOriginal - Zorgt ervoor dat originele woorden behouden blijven en worden toegevoegd aan de subwoordlijst. De standaardinstelling is onwaar.<br /><br /> splitOnNumerics (type: bool) - Als dit waar is, worden getallen opgesplitst, bijvoorbeeld "Azure1Search" wordt "Azure" "1" "Zoeken". De standaardinstelling is waar.<br /><br /> stamEnglishPossessive (type: bool) - Zorgt ervoor dat trailing "'s" worden verwijderd voor elk subwoord. De standaardinstelling is waar.<br /><br /> protectedWords (type: string array) - Tokens om te beschermen tegen afgebakende. De standaardinstelling is een lege lijst.|  

 <sup>1</sup> Tokenfiltertypen zijn altijd vooraf bevestigd in code met "#Microsoft.Azure.Search" zodanig dat "ArabicNormalizationTokenFilter" daadwerkelijk zou worden opgegeven als "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter".  We hebben het voorvoegsel verwijderd om de breedte van de tabel te verkleinen, maar vergeet niet om het in uw code op te nemen.  


## <a name="see-also"></a>Zie ook  
 [Azure Cognitive Search REST API's](https://docs.microsoft.com/rest/api/searchservice/)   
 [Analysers in Azure Cognitive Search > voorbeelden](search-analyzers.md#examples)    
 [Index &#40;Azure Cognitive Search REST&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
