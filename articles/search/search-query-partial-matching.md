---
title: Patronen en speciale tekens matchen
titleSuffix: Azure Cognitive Search
description: Gebruik wildcard- en voorvoegquery's die overeenkomen met algemene of gedeeltelijke termen in een Azure Cognitive Search-queryaanvraag. Moeilijk te matchen patronen met speciale tekens kunnen worden opgelost met behulp van volledige querysyntaxis en aangepaste analysers.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: f78ba5b351a3da46d7b8b3780cf00772c4f3b2ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289308"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Overeenkomen met patronen en speciale tekens (streepjes)

Voor query's die`-, *, (, ), /, \, =`speciale tekens bevatten ( ) of voor querypatronen op basis van gedeeltelijke termen binnen een grotere term, zijn doorgaans aanvullende configuratiestappen nodig om ervoor te zorgen dat de index de verwachte inhoud in de juiste indeling bevat. 

Standaard wordt een telefoonnummer `+1 (425) 703-6214` als tokengemaakt `"425"` `"703"`als `"6214"` `"1"`, , , . Zoals u zich kunt `"3-62"`voorstellen, zal zoeken op , gedeeltelijke termen die een streepje bevatten, mislukken omdat die inhoud niet echt bestaat in de index. 

Wanneer u op gedeeltelijke tekenreeksen of speciale tekens moet zoeken, u de standaardanalyzer overschrijven met een aangepaste analyzer die werkt onder eenvoudigere tokenisatieregels, waarbij hele termen worden behouden, die nodig zijn wanneer querytekenreeksen delen van een term of speciale Tekens. Een stap terug, de aanpak ziet er als volgt uit:

+ Kies een vooraf gedefinieerde analyzer of definieer een aangepaste analyzer die de gewenste uitvoer produceert
+ De analyzer toewijzen aan het veld
+ De index en test samenstellen

In dit artikel u deze taken uitvoeren. De hier beschreven aanpak is nuttig in andere scenario's: wildcard- en reguliere expressiequery's hebben ook hele termen nodig als basis voor patroonmatching. 

> [!TIP]
> Het evalueren van analyers is een iteratief proces dat frequente indexreconstructies vereist. U deze stap eenvoudiger maken door Postman, de REST API's voor [Index maken,](https://docs.microsoft.com/rest/api/searchservice/create-index) [Index maken,](https://docs.microsoft.com/rest/api/searchservice/delete-index)[Documenten laden](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)en [Documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents). Voor Het laden van documenten moet de aanvraaginstantie een kleine representatieve gegevensset bevatten die u wilt testen (bijvoorbeeld een veld met telefoonnummers of productcodes). Met deze API's in dezelfde Postman-collectie u deze stappen snel doorlopen.

## <a name="choosing-an-analyzer"></a>Een analyzer kiezen

Bij het kiezen van een analyzer die langetermijntokens produceert, zijn de volgende analysers veelvoorkomende keuzes:

| Analyzer | Gedrag |
|----------|-----------|
| [Trefwoord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | Inhoud van het hele veld wordt tokenized als één term. |
| [Whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Scheidt alleen op witte ruimten. Termen met streepjes of andere tekens worden behandeld als één token. |
| [aangepaste analyzer](index-add-custom-analyzers.md) | (aanbevolen) Met het maken van een aangepaste analyzer u zowel de tokenizer als het tokenfilter opgeven. De vorige analysesystemen moeten worden gebruikt zoals het is. Met een aangepaste analyzer u kiezen welke tokenizers en tokenfilters u wilt gebruiken. <br><br>Een aanbevolen combinatie is de [keyword tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) met een [kleintokenfilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). Op zichzelf heeft de vooraf gedefinieerde [trefwoordanalyzer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) geen hoofdletters, waardoor query's kunnen mislukken. Een aangepaste analyzer geeft u een mechanisme voor het toevoegen van het kleine tokenfilter. |

Als u een web-API-testtool zoals Postman gebruikt, u de [TEST Analyzer REST-aanroep](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) toevoegen om tokenized output te inspecteren. Gezien een bestaande index en een veld met streepjes of gedeeltelijke termen, u verschillende analysers proberen over specifieke termen om te zien welke tokens worden uitgestoten.  

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
> Houd er rekening mee dat queryparsers vaak lagere termen in een zoekexpressie gebruiken bij het bouwen van de querystructuur. Als u een analyzer gebruikt die geen tekstingangen in kleine letters gebruikt en u geen verwachte resultaten krijgt, kan dit de reden zijn. De oplossing is om een lwower-case token filter toe te voegen.

## <a name="analyzer-definitions"></a>Analyseerdefinities
 
Of u nu analysers evalueert of verder gaat met een specifieke configuratie, u moet de analyzer op de velddefinitie opgeven en eventueel de analyzer zelf configureren als u geen ingebouwde analyzer gebruikt. Wanneer u analysers verwisselt, moet u de index meestal opnieuw opbouwen (neerzetten, opnieuw maken en opnieuw laden). 

### <a name="use-built-in-analyzers"></a>Ingebouwde analysers gebruiken

Ingebouwde of vooraf gedefinieerde analysatoren kunnen op `analyzer` naam worden opgegeven op een eigenschap van een velddefinitie, zonder dat er extra configuratie in de index vereist is. In het volgende voorbeeld wordt `whitespace` uitgelegd hoe u de analyzer op een veld zou instellen.

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
Zie Lijst met [vooraf gedefinieerde analysers voor](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)meer informatie over alle beschikbare ingebouwde analysers. 

### <a name="use-custom-analyzers"></a>Aangepaste analysers gebruiken

Als u een [aangepaste analyzer](index-add-custom-analyzers.md)gebruikt, definieert u deze in de index met een door de gebruiker gedefinieerde combinatie van tokenizer, tokenfilter, met mogelijke configuratie-instellingen. Verwijs vervolgens naar een velddefinitie, net zoals een ingebouwde analyzer zou zijn.

Wanneer het doel tokenisatie voor de hele termijn is, wordt een aangepaste analyzer aanbevolen die bestaat uit een **trefwoordtokenizer** en **een kleintokenfilter.**

+ De trefwoordtokenizer maakt één token voor de volledige inhoud van een veld.
+ Het tokenfilter voor kleine letters transformeert hoofdletters in kleine letters. Queryparsers hebben doorgaans kleine hoofdletters voor hoofdletters. Lowercasing homogeniseert de ingangen met de tokenized termen.

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

### <a name="duplicate-fields-for-different-scenarios"></a>Velden dupliceren voor verschillende scenario's

Een andere optie maakt gebruik van de opdracht voor analyseper veld om te optimaliseren voor verschillende scenario's. In het bijzonder u featureCode en featureCodeRegex definiëren om regelmatig zoeken in volledige tekst op de eerste en geavanceerde patroonmatching op de tweede te ondersteunen.

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

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe analysers zowel bijdragen aan queryproblemen als queryproblemen oplossen. Als volgende stap, neem een kijkje op analyzer impact op indexering en query verwerking. Overweeg in het bijzonder de API Tekst analyseren te gebruiken om tokenized output terug te sturen, zodat u precies zien wat een analyzer voor uw index maakt.

+ [Taalanalyse](search-language-support.md)
+ [Analysers voor tekstverwerking in Azure Cognitive Search](search-analyzers.md)
+ [Tekst-API (REST) analyseren](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Hoe zoeken in volledige tekst werkt (queryarchitectuur)](search-lucene-query-architecture.md)
