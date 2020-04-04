---
title: Gedeeltelijke termen, patronen en speciale tekens
titleSuffix: Azure Cognitive Search
description: Gebruik wildcard-, regex- en voorvoegquery's die overeenkomen met algemene of gedeeltelijke termen in een Azure Cognitive Search-queryaanvraag. Moeilijk te matchen patronen met speciale tekens kunnen worden opgelost met behulp van volledige querysyntaxis en aangepaste analysers.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 3e0e0291ff855b4502224466e17696a4fe668c2a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655989"
---
# <a name="partial-term-search-in-azure-cognitive-search-queries-wildcard-regex-fuzzy-search-patterns"></a>Zoeken naar gedeeltelijke termen in Azure Cognitive Search-query's (wildcard, regex, fuzzy search, patronen)

Een *gedeeltelijke term zoekopdracht* verwijst naar query's die bestaan uit termfragmenten, zoals de eerste, laatste of binnendelen van een tekenreeks, of een patroon bestaande uit een combinatie van fragmenten, vaak gescheiden door speciale tekens zoals streepjes of schuine strepen. Veelgebruikte use-cases omvatten query's voor delen van een telefoonnummer, URL, personen of productcodes of samengestelde woorden.

Gedeeltelijke zoekopdracht kan problematisch zijn omdat de index zelf termen meestal niet opslaat op een manier die bevorderlijk is voor gedeeltelijke tekenreeks- en patroonmatching. Tijdens de tekstanalysefase van indexering worden speciale tekens verwijderd, samengestelde tekenreeksen en samengestelde tekenreeksen opgesplitst, waardoor patroonquery's mislukken wanneer er geen overeenkomst wordt gevonden. Een telefoonnummer zoals `+1 (425) 703-6214`(tokenized als `"1"` `"425"`, `"703"` `"6214"`, , ) wordt `"3-62"` bijvoorbeeld niet weergegeven in een query omdat die inhoud niet echt in de index bestaat. 

De oplossing is om intacte versies van deze tekenreeksen op te slaan in de index, zodat u gedeeltelijke zoekscenario's ondersteunen. Het maken van een extra veld voor een intacte tekenreeks, plus het gebruik van een content-preserving analyzer, is de basis van de oplossing.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Wat is gedeeltelijke zoekopdracht in Azure Cognitive Search

In Azure Cognitive Search is gedeeltelijk zoeken beschikbaar in de volgende formulieren:

+ [Prefix zoeken,](query-simple-syntax.md#prefix-search) `search=cap*`zoals , matching op "Cap'n Jack's Waterfront Inn" of "Gacc Capital". U de syntaxis van de query gebruiken voor zoeken naar voorvoegsel.
+ [Zoeken met jokertekens](query-lucene-syntax.md#bkmk_wildcard) of [reguliere expressies](query-lucene-syntax.md#bkmk_regex) die zoeken naar een patroon of delen van een ingesloten tekenreeks, inclusief het achtervoegsel. Bijvoorbeeld, gezien de term "alfanumerieke", zou u`search=/.*numeric.*/`gebruik maken van een wildcard zoeken ( ) voor een achtervoegsel query overeenkomen met die term. Wildcard en reguliere expressies vereisen de volledige Lucene-syntaxis.

Wanneer een van de bovenstaande querytypen nodig is in uw clienttoepassing, voert u de stappen in dit artikel uit om ervoor te zorgen dat de benodigde inhoud in uw index aanwezig is.

## <a name="solving-partial-search-problems"></a>Problemen met gedeeltelijke zoekopdrachtoplossen

Wanneer u op patronen of speciale tekens moet zoeken, u de standaardanalyzer overschrijven met een aangepaste analyzer die werkt onder eenvoudigere tokenisatieregels, waarbij de hele tekenreeks behouden blijft. Een stap terug, de aanpak ziet er als volgt uit:

+ Een veld definiëren om een intacte versie van de tekenreeks op te slaan (ervan uitgaande dat u geanalyseerde en niet-geanalyseerde tekst wilt)
+ Kies een vooraf gedefinieerde analyzer of definieer een aangepaste analyzer om een intacte tekenreeks uit te geven
+ De analyzer toewijzen aan het veld
+ De index bouwen en testen

> [!TIP]
> Het evalueren van analysers is een iteratief proces waarvoor frequente indexreconstructies nodig zijn. U deze stap eenvoudiger maken door Postman, de REST API's voor [Index maken,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Index maken,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Documenten laden](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)en [Documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents). Voor Het laden van documenten moet de aanvraaginstantie een kleine representatieve gegevensset bevatten die u wilt testen (bijvoorbeeld een veld met telefoonnummers of productcodes). Met deze API's in dezelfde Postman-collectie u deze stappen snel doorlopen.

## <a name="duplicate-fields-for-different-scenarios"></a>Velden dupliceren voor verschillende scenario's

Analysers worden per veld toegewezen, wat betekent dat u velden in uw index maken om te optimaliseren voor verschillende scenario's. In het bijzonder u featureCode en featureCodeRegex definiëren om regelmatig zoeken in volledige tekst op de eerste en geavanceerde patroonmatching op de tweede te ondersteunen.

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_customanalyzer"
},
```

## <a name="choose-an-analyzer"></a>Kies een analyzer

Bij het kiezen van een analyzer die langetermijntokens produceert, zijn de volgende analysers veelvoorkomende keuzes:

| Analyzer | Gedrag |
|----------|-----------|
| [Trefwoord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Inhoud van het hele veld wordt tokenized als één term. |
| [Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Scheidt alleen op witte ruimten. Termen met streepjes of andere tekens worden behandeld als één token. |
| [aangepaste analyzer](index-add-custom-analyzers.md) | (aanbevolen) Met het maken van een aangepaste analyzer u zowel de tokenizer als het tokenfilter opgeven. De vorige analysesystemen moeten worden gebruikt zoals het is. Met een aangepaste analyzer u kiezen welke tokenizers en tokenfilters u wilt gebruiken. <br><br>Een aanbevolen combinatie is de [keyword tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) met een [kleintokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Op zichzelf heeft de vooraf gedefinieerde [trefwoordanalyzer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) geen hoofdletters, waardoor query's kunnen mislukken. Een aangepaste analyzer geeft u een mechanisme voor het toevoegen van het kleine tokenfilter. |

Als u een web-API-testtool zoals Postman gebruikt, u de [TEST Analyzer REST-aanroep](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) toevoegen om tokenized output te inspecteren.

U moet een bestaande index hebben om mee te werken. Gezien een bestaande index en een veld met streepjes of gedeeltelijke termen, u verschillende analysers proberen over specifieke termen om te zien welke tokens worden uitgestoten.  

1. Controleer de standaardanalyzer om te zien hoe termen standaard worden tokenized.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Evalueer het antwoord om te zien hoe de tekst wordt tokenized in de index. Merk op hoe elke term is kleine letters en opgesplitst.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. Wijzig het verzoek `whitespace` om `keyword` de of analyzer te gebruiken:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Nu bestaat het antwoord uit één token, hoofdletters, met streepjes behouden als onderdeel van de tekenreeks. Als u moet zoeken op een patroon of een gedeeltelijke term, heeft de queryengine nu de basis voor het vinden van een overeenkomst.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> Houd er rekening mee dat queryparsers vaak lagere termen in een zoekexpressie gebruiken bij het bouwen van de querystructuur. Als u een analyzer gebruikt die geen tekstingangen in kleine letters gebruikt en u geen verwachte resultaten krijgt, kan dit de reden zijn. De oplossing is om een kleintokenfilter toe te voegen, zoals beschreven in de sectie 'Aangepaste analysers gebruiken' hieronder.

## <a name="configure-an-analyzer"></a>Een analyzer configureren
 
Of u nu analysers evalueert of verder gaat met een specifieke configuratie, u moet de analyzer op de velddefinitie opgeven en eventueel de analyzer zelf configureren als u geen ingebouwde analyzer gebruikt. Wanneer u analysers verwisselt, moet u de index meestal opnieuw opbouwen (neerzetten, opnieuw maken en opnieuw laden). 

### <a name="use-built-in-analyzers"></a>Ingebouwde analysers gebruiken

Ingebouwde of vooraf gedefinieerde analysatoren kunnen op `analyzer` naam worden opgegeven op een eigenschap van een velddefinitie, zonder dat er extra configuratie in de index vereist is. In het volgende voorbeeld wordt `whitespace` uitgelegd hoe u de analyzer op een veld zou instellen. Zie Lijst met vooraf gedefinieerde analysers voor meer informatie over beschikbare ingebouwde [analysers.](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference) 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>Aangepaste analysers gebruiken

Als u een [aangepaste analyzer](index-add-custom-analyzers.md)gebruikt, definieert u deze in de index met een door de gebruiker gedefinieerde combinatie van tokenizer, tokenfilter, met mogelijke configuratie-instellingen. Verwijs vervolgens naar een velddefinitie, net zoals een ingebouwde analyzer zou zijn.

Wanneer het doel tokenisatie voor de hele termijn is, wordt een aangepaste analyzer aanbevolen die bestaat uit een **trefwoordtokenizer** en **een kleintokenfilter.**

+ De trefwoordtokenizer maakt één token voor de volledige inhoud van een veld.
+ Het tokenfilter voor kleine letters transformeert hoofdletters in kleine letters. Queryparsers hebben doorgaans kleine hoofdletters voor hoofdletters. De onderste behuizing homogeniseert de ingangen met de tokenized termen.

In het volgende voorbeeld wordt een aangepaste analyzer geïllustreerd die de trefwoordtokenizer en een kleine tokenfilter biedt.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> De `keyword_v2` tokenizer `lowercase` en token filter zijn bekend bij het systeem en met behulp van hun standaard configuraties, dat is waarom je ze verwijzen op naam zonder ze eerst te definiëren.

## <a name="build-and-test"></a>Bouwen en testen

Zodra u een index hebt gedefinieerd met analysators en velddefinities die uw scenario ondersteunen, laadt u documenten met representatieve tekenreeksen, zodat u gedeeltelijke tekenreeksquery's testen. 

De vorige delen legden de logica uit. In deze sectie wordt elke API doorlopen die u moet aanroepen bij het testen van uw oplossing. Zoals eerder opgemerkt, als u een interactieve webtesttool zoals Postman gebruikt, u deze taken snel doorlopen.

+ [Als u Index verwijderen](https://docs.microsoft.com/rest/api/searchservice/delete-index) een bestaande index met dezelfde naam verwijdert, zodat u deze opnieuw maken.

+ [Met Index maken,](https://docs.microsoft.com/rest/api/searchservice/create-index) wordt de indexstructuur van uw zoekservice gemaakt, inclusief analysedefinities en -velden met een analyzer-specificatie.

+ [Load Documents](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) importeert documenten met dezelfde structuur als uw index en doorzoekbare inhoud. Na deze stap is uw index klaar om te bevragen of te testen.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) werd geïntroduceerd in [Kies een analyzer](#choose-an-analyzer). Test enkele tekenreeksen in uw index met behulp van verschillende analysers om te begrijpen hoe termen worden tokenized.

+ [In zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents) wordt uitgelegd hoe u een queryaanvraag maken, met behulp van [eenvoudige syntaxis](query-simple-syntax.md) of [volledige lucene-syntaxis](query-lucene-syntax.md) voor wildcard en reguliere expressies.

## <a name="tips-and-best-practices"></a>Tips en best practices

### <a name="tune-query-performance"></a>Queryprestaties afstemmen

Als u de aanbevolen configuratie implementeert die de keyword_v2 tokenizer en het kleine tokenfilter bevat, ziet u mogelijk een afname in queryprestaties als gevolg van de extra tokenfilterverwerking ten opzichte van bestaande tokens in uw index. 

In het volgende voorbeeld wordt een [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) toegevoegd om voorvoegselovereenkomsten sneller te maken. Er worden extra tokens gegenereerd voor in 2-25 tekencombinaties die tekens bevatten: (niet alleen MS, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). Zoals u zich voorstellen, resulteert de extra tokenization in een grotere index.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Verschillende analyseapparaten gebruiken voor indexering en queryverwerking

Analyzers worden aangeroepen tijdens het indexeren en tijdens queryuitvoering. Het is gebruikelijk om dezelfde analyzer voor beide te gebruiken, maar u aangepaste analysers configureren voor elke werkbelasting. Overschrijft van de analysator wordt `analyzers` opgegeven in de [indexdefinitie](https://docs.microsoft.com/rest/api/searchservice/create-index) in een sectie en vervolgens verwezen naar specifieke velden. 

Wanneer aangepaste analyse alleen vereist is tijdens het indexeren, u de aangepaste analyzer toepassen op alleen indexering en de standaard Lucene analyzer (of een andere analyzer) blijven gebruiken voor query's.

Als u rolspecifieke analyses wilt opgeven, u eigenschappen `indexAnalyzer` `searchAnalyzer` instellen op `analyzer` het veld voor elk veld, instellen en in plaats van de standaardeigenschap.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe analysers zowel bijdragen aan queryproblemen als queryproblemen oplossen. Als volgende stap, neem een kijkje op analyzer impact op indexering en query verwerking. Overweeg in het bijzonder de API Tekst analyseren te gebruiken om tokenized output terug te sturen, zodat u precies zien wat een analyzer voor uw index maakt.

+ [Taalanalyse](search-language-support.md)
+ [Analysers voor tekstverwerking in Azure Cognitive Search](search-analyzers.md)
+ [Tekst-API (REST) analyseren](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Hoe zoeken in volledige tekst werkt (queryarchitectuur)](search-lucene-query-architecture.md)