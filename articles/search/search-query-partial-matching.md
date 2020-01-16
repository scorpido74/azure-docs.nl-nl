---
title: Patronen en speciale tekens vergelijken
titleSuffix: Azure Cognitive Search
description: Gebruik joker tekens en voorvoegsel query's die overeenkomen met hele of gedeeltelijke voor waarden in een Azure Cognitive Search query-aanvraag. U kunt aan de hand van de volledige query syntaxis en aangepaste analyse functies gebruikmaken van de meest overeenkomende patronen die speciale tekens bevatten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ec1422d03cce78bdd8206f6687a78b63ddf989dc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989616"
---
# <a name="match-on-patterns-and-special-characters-dashes"></a>Zoeken op patronen en speciale tekens (streepjes)

Voor query's die speciale tekens bevatten (`-, *, (, ), /, \, =`) of voor query patronen op basis van gedeeltelijke termen binnen een grotere termijn, zijn er meestal extra configuratie stappen nodig om ervoor te zorgen dat de index de verwachte inhoud bevat, in de juiste indeling. 

Een telefoon nummer zoals `+1 (425) 703-6214` wordt standaard getokend als `"1"`, `"425"`, `"703"``"6214"`. Zoals u kunt Voorst Ellen, mislukt het zoeken op `"3-62"`, omdat de inhoud niet echt in de index voor komt. 

Als u wilt zoeken op gedeeltelijke teken reeksen of speciale tekens, kunt u de standaard-Analyzer vervangen door een aangepaste analyse functie die onder eenvoudigere regels voor tokens wordt uitgevoerd, waarbij hele voor waarden worden behouden, wat nodig is wanneer query reeksen delen van een term bevatten of speciale aantal. Als u een stap terug neemt, ziet de benadering er als volgt uit:

+ Een vooraf gedefinieerde analyse functie kiezen of een aangepaste Analyzer definiëren die de gewenste uitvoer produceert
+ De analyse functie toewijzen aan het veld
+ De index en test maken

Dit artikel begeleidt u bij deze taken. De hier beschreven methode is nuttig in andere scenario's: met Joker tekens en reguliere expressie query's hebt u ook volledige voor waarden nodig als basis voor het vergelijken van patronen. 

> [!TIP]
> Het evalueren van analyers is een iteratief proces dat veelvuldig opnieuw opgebouwde index vereist. U kunt deze stap eenvoudiger maken met behulp van Postman, de REST-Api's voor het maken van een [index](https://docs.microsoft.com/rest/api/searchservice/create-index), het verwijderen van een [index](https://docs.microsoft.com/rest/api/searchservice/delete-index), het[laden van documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)en het zoeken van [documenten](https://docs.microsoft.com/rest/api/searchservice/search-documents). Voor het laden van documenten moet de hoofd tekst van de aanvraag een kleine representatieve gegevensset bevatten die u wilt testen (bijvoorbeeld een veld met telefoon nummers of product codes). Met deze Api's in dezelfde postman-verzameling kunt u deze stappen snel door lopen.

## <a name="choosing-an-analyzer"></a>Een analyse functie kiezen

Bij het kiezen van een analyse functie die volledige-termijn tokens produceert, zijn de volgende analyse functies algemene keuzes:

| Procedures | Mogelijkheden |
|----------|-----------|
| [keyword](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | De inhoud van het hele veld wordt als één periode getokend. |
| [whitespace](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Scheidt alleen op spaties. Voor waarden die streepjes of andere tekens bevatten, worden beschouwd als één token. |
| [aangepaste analyse functie](index-add-custom-analyzers.md) | aanbevelingen Als u een aangepaste analyse functie maakt, kunt u zowel het tokenizer als het token filter opgeven. De vorige analyse functies moeten worden gebruikt als-is. Met een aangepaste analyse kunt u kiezen welke tokenizers-en Token filters u wilt gebruiken. <br><br>Een aanbevolen combi natie is het [tref woord tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) met een [token filter met een lagere Case](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). De vooraf gedefinieerde [trefwoord analyse](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) biedt geen kleine letters in hoofd letters, wat kan leiden tot het mislukken van query's. Een aangepaste analyse functie biedt u een mechanisme voor het toevoegen van het filter voor het laagste Case-token. |

Als u een web-API-test programma zoals postman gebruikt, kunt u de [rest-aanroep van Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) toevoegen om de getokende uitvoer te controleren. Als u een bestaande index en een veld met streepjes of gedeeltelijke voor waarden hebt opgegeven, kunt u verschillende analyse functies op basis van specifieke termen uitproberen om te zien welke tokens worden verzonden.  

1. Raadpleeg de Standard Analyzer om te zien hoe de termen standaard worden getokend.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. Evalueer het antwoord om te zien hoe de tekst binnen de index wordt getokend. U ziet dat elke term in kleine letters en uitsplitsing is.

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
1. Wijzig de aanvraag om de `whitespace` of `keyword` Analyzer te gebruiken:

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. Het antwoord bestaat nu uit één token, hoofd letters, met streepjes die als onderdeel van de teken reeks zijn bewaard. Als u op een patroon of gedeeltelijke term wilt zoeken, heeft de query-engine nu de basis voor het zoeken naar een overeenkomst.


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
> Houd er rekening mee dat query parsers vaak kleine letters in een zoek expressie bij het samen stellen van de query-structuur. Als u een analyse functie gebruikt die geen kleine letters bevat, en u niet de verwachte resultaten krijgt, kan dit de reden zijn. De oplossing is het toevoegen van een lwower-Case filter.

## <a name="analyzer-definitions"></a>Analyse definities
 
Of u analyse functies wilt evalueren of vooruit wilt gaan met een specifieke configuratie, u moet de analyse op de veld definitie opgeven en mogelijk de Analyzer zelf configureren als u geen gebruik maakt van een ingebouwde analyse functie. Bij het wisselen van analyse functies moet u doorgaans de index opnieuw samen stellen (verwijderen, opnieuw maken en opnieuw laden). 

### <a name="use-built-in-analyzers"></a>Ingebouwde analyse functies gebruiken

Ingebouwde of vooraf gedefinieerde analyse functies kunnen worden opgegeven met de naam van een `analyzer` eigenschap van een veld definitie, zonder dat er aanvullende configuratie vereist is in de index. In het volgende voor beeld ziet u hoe u de `whitespace` Analyzer kunt instellen voor een veld.

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
Zie [vooraf gedefinieerde analyse lijsten](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)voor meer informatie over alle beschik bare ingebouwde analyse functies. 

### <a name="use-custom-analyzers"></a>Aangepaste analyse functies gebruiken

Als u een [aangepaste Analyzer](index-add-custom-analyzers.md)gebruikt, definieert u deze in de index met een door de gebruiker gedefinieerde combi natie van tokenizer, tokenfilter, met mogelijke configuratie-instellingen. Vervolgens verwijzen we naar de definitie van een veld, net zoals u een ingebouwde Analyzer zou hebben.

Wanneer het doel van de volledige-termijn tokening is, wordt een aangepaste analyse functie aanbevolen die bestaat uit een **sleutel woord tokenizer** en een filter voor een **laag hoofdletter gebruik** .

+ Het tref woord tokenizer maakt één token voor de volledige inhoud van een veld.
+ Met het token filter met kleine letters worden hoofd letters getransformeerd in tekst met een kleine letter. Query-parsers bevatten doorgaans kleine letters tekst invoer. Lowercasing homogenizes de invoer met de tokens-termen.

In het volgende voor beeld ziet u een aangepaste analyse functie die het tref woord tokenizer en een token filter voor kleine letters bevat.

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
]

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
> `keyword_v2` het tokenizer-en `lowercase`-token filter zijn bekend bij het systeem en met behulp van de standaard configuraties. Daarom kunt u deze op naam noemen zonder ze eerst te definiëren.

## <a name="tips-and-best-practices"></a>Tips en best practices

### <a name="tune-query-performance"></a>Queryprestaties afstemmen

Als u de aanbevolen configuratie implementeert die het keyword_v2 tokenizer-en het kleine-case-token filter bevat, ziet u mogelijk een afname in de query prestaties vanwege de extra token filter verwerking voor bestaande tokens in uw index. 

In het volgende voor beeld wordt een [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) toegevoegd om het voor voegsel sneller te laten overeenkomen. Er worden extra tokens gegenereerd voor de 2-25-teken combinaties die tekens bevatten: (niet alleen MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/VK, MSFT/SQL). Zoals u kunt Voorst Ellen, resulteert de extra tokening in een grotere index.

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
]

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Verschillende analyse functies gebruiken voor het verwerken van indexen en query's

Analyse functies worden aangeroepen tijdens het indexeren en tijdens de uitvoering van query's. Het is gebruikelijk om dezelfde Analyzer voor beide te gebruiken, maar u kunt voor elke werk belasting aangepaste analyse functies configureren. Analyzer-onderdrukkingen worden opgegeven in de [index definitie](https://docs.microsoft.com/rest/api/searchservice/create-index) in een `analyzers` sectie en vervolgens naar specifieke velden wordt verwezen. 

Wanneer aangepaste analyse alleen is vereist tijdens het indexeren, kunt u de aangepaste Analyzer Toep assen om alleen de standaard-lucene Analyzer (of een andere analyse) voor query's te indexeren en te blijven gebruiken.

Als u een Role-specifieke analyse wilt opgeven, kunt u voor elk veld eigenschappen instellen voor elke functie, `indexAnalyzer` en `searchAnalyzer` instellen in plaats van de standaard eigenschap `analyzer`.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

### <a name="duplicate-fields-for-different-scenarios"></a>Dubbele velden voor verschillende scenario's

Een andere optie maakt gebruik van de analyse toewijzing per veld om te optimaliseren voor verschillende scenario's. U kunt met name "featureCode" en "featureCodeRegex" definiëren voor het ondersteunen van normale Zoek opdrachten in volledige tekst op het eerste en geavanceerde patroon vergelijking op de tweede.

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

In dit artikel wordt uitgelegd hoe analyse functies beide bijdragen om problemen op te vragen en query problemen op te lossen. Ga als volgt te werk om de analyse-impact op indexering en query verwerking nader te bekijken. In het bijzonder kunt u overwegen de analyse tekst-API te gebruiken voor het retour neren van een tokenve uitvoer zodat u precies ziet wat een Analyzer is voor uw index.

+ [Taalanalyse](search-language-support.md)
+ [Analyse functies voor tekst verwerking in azure Cognitive Search](search-analyzers.md)
+ [Tekst-API analyseren (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Hoe zoeken in volledige tekst werkt (query architectuur)](search-lucene-query-architecture.md)