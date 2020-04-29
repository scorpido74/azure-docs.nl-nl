---
title: Gedeeltelijke termen, patronen en speciale tekens
titleSuffix: Azure Cognitive Search
description: Gebruik joker tekens, regex en voorvoegsel query's die overeenkomen met hele of gedeeltelijke voor waarden in een Azure Cognitive Search query-aanvraag. U kunt aan de hand van de volledige query syntaxis en aangepaste analyse functies gebruikmaken van de meest overeenkomende patronen die speciale tekens bevatten.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 5a05f2973ac17460250fb3e80eb7bc0da9849940
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81262873"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>Zoeken op gedeeltelijke termen en patronen met speciale tekens (Joker teken, regex, patronen)

Een *gedeeltelijke zoek opdracht* verwijst naar query's die bestaan uit termen fragmenten, in plaats van een hele term, u hebt alleen het begin, het midden of het einde van de term (ook wel voor voegsel-, infix-of achtervoegsel query's genoemd). Een *patroon* kan een combi natie van fragmenten zijn, vaak met speciale tekens, zoals streepjes of slashes die deel uitmaken van de query teken reeks. Veelvoorkomend gebruik: cases bevatten query's voor delen van een telefoon nummer, URL, persoon of product code of samengestelde woorden.

Gedeeltelijke Zoek opdrachten en patronen kunnen problematisch zijn als de index geen termen in de verwachte indeling heeft. Tijdens de [lexicale analyse fase](search-lucene-query-architecture.md#stage-2-lexical-analysis) van het indexeren (uitgaande van de standaard-Analyzer) worden speciale tekens verwijderd, samengestelde en samengestelde teken reeksen worden opgesplitst en wordt witruimte verwijderd; het enige wat kan leiden tot het mislukken van patroon query's wanneer er geen overeenkomst wordt gevonden. Bijvoorbeeld: een telefoon nummer `+1 (425) 703-6214` zoals (tokend as `"1"`, `"425"`, `"703"`, `"6214"`) wordt niet weer gegeven in een `"3-62"` query omdat die inhoud niet echt in de index voor komt. 

De oplossing is het aanroepen van een Analyzer waarbij een volledige teken reeks wordt bewaard, inclusief spaties en speciale tekens, zodat u op gedeeltelijke termen en patronen kunt zoeken. Het maken van een aanvullend veld voor een ingrijpende teken reeks, plus het gebruik van een analyse functie voor content behoud, vormt de basis van de oplossing.

> [!TIP]
> Bekend met de Api's postman en REST? [Down load de verzameling query voorbeelden voor het](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/full-syntax-examples) opvragen van gedeeltelijke voor waarden en speciale tekens die in dit artikel worden beschreven.

## <a name="what-is-partial-search-in-azure-cognitive-search"></a>Wat is een gedeeltelijke zoek opdracht in azure Cognitive Search

In azure Cognitive Search zijn gedeeltelijke zoek acties en patronen beschikbaar in de volgende formulieren:

+ [Zoek opdracht voor voegsel](query-simple-syntax.md#prefix-search), `search=cap*`zoals, overeenkomt met ' Cap'n Jack afgebakend Inn ' of ' gacc Capital '. U kunt de eenvoudige query syntaxis of de volledige lucene-query syntaxis gebruiken voor het zoeken naar voor voegsels.

+ [Zoek opdrachten met Joker tekens](query-lucene-syntax.md#bkmk_wildcard) of [reguliere expressies](query-lucene-syntax.md#bkmk_regex) die naar een patroon of delen van een Inge sloten teken reeks zoeken. Joker tekens en reguliere expressies vereisen de volledige lucene-syntaxis. Achtervoegsel-en index query's worden geformuleerd als een reguliere expressie.

  Enkele voor beelden van zoek opdrachten op de gedeeltelijke term zijn: Voor een suffix query, op basis van de term ' alfanumeriek ', gebruikt u een zoek opdracht`search=/.*numeric.*/`voor joker tekens () om een overeenkomst te vinden. Voor een gedeeltelijke term met inbegrip van interne tekens, zoals een URL-fragment, moet u mogelijk escape tekens toevoegen. In JSON wordt een slash `/` met een back slash `\`met een escape-teken. Als zodanig is `search=/.*microsoft.com\/azure\/.*/` de syntaxis voor het URL-fragment "Microsoft.com/azure/".

Zoals is vermeld, is voor alle bovenstaande onderdelen vereist dat de index teken reeksen bevat in een indeling die wordt gebruikt voor het vergelijken van patronen, wat de standaard-Analyzer niet biedt. Door de stappen in dit artikel te volgen, kunt u ervoor zorgen dat de benodigde inhoud bestaat ter ondersteuning van deze scenario's.

## <a name="solving-partialpattern-search-problems"></a>Problemen met zoeken in een deel/patroon oplossen

Als u wilt zoeken naar fragmenten of patronen of speciale tekens, kunt u de standaard-Analyzer vervangen door een aangepaste analyse functie die onder eenvoudigere regels voor tokens maakt, waarbij de hele teken reeks behouden blijft. Als u een stap terug neemt, ziet de benadering er als volgt uit:

+ Een veld definiëren voor het opslaan van een onbeschadigde versie van de teken reeks (ervan uitgaande dat u wilt analyseren en niet-geanalyseerde tekst)
+ Een vooraf gedefinieerde analyse functie kiezen of een aangepaste Analyzer definiëren voor het uitvoeren van een niet-geanalyseerde teken reeks
+ Wijs de aangepaste Analyzer toe aan het veld
+ De index maken en testen

> [!TIP]
> Het evalueren van analyse functies is een iteratief proces dat veelvuldig opnieuw opgebouwde index vereist. U kunt deze stap eenvoudiger maken met behulp van Postman, de REST-Api's voor het maken van een [index](https://docs.microsoft.com/rest/api/searchservice/create-index), het verwijderen van een [index](https://docs.microsoft.com/rest/api/searchservice/delete-index), het[laden van documenten](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)en het zoeken van [documenten](https://docs.microsoft.com/rest/api/searchservice/search-documents). Voor het laden van documenten moet de hoofd tekst van de aanvraag een kleine representatieve gegevensset bevatten die u wilt testen (bijvoorbeeld een veld met telefoon nummers of product codes). Met deze Api's in dezelfde postman-verzameling kunt u deze stappen snel door lopen.

## <a name="duplicate-fields-for-different-scenarios"></a>Dubbele velden voor verschillende scenario's

Analyse functies worden per veld toegewezen, wat betekent dat u velden in uw index kunt maken om te optimaliseren voor verschillende scenario's. U kunt met name "featureCode" en "featureCodeRegex" definiëren voor het ondersteunen van normale Zoek opdrachten in volledige tekst op het eerste en geavanceerde patroon vergelijking op de tweede.

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
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>Een analyse functie kiezen

Bij het kiezen van een analyse functie die volledige-termijn tokens produceert, zijn de volgende analyse functies algemene keuzes:

| Procedures | Mogelijkheden |
|----------|-----------|
| [taal analyse](index-add-language-analyzers.md) | Hiermee behoudt u afbreek streepjes in samengestelde woorden of teken reeksen, klinker mutaties en verbale vormen. Als de query patronen streepjes bevatten, kan het gebruik van een taal analyse voldoende zijn. |
| [Zoek](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | De inhoud van het hele veld wordt als één periode getokend. |
| [spaties](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | Scheidt alleen op spaties. Voor waarden die streepjes of andere tekens bevatten, worden beschouwd als één token. |
| [aangepaste analyse functie](index-add-custom-analyzers.md) | aanbevelingen Als u een aangepaste analyse functie maakt, kunt u zowel het tokenizer als het token filter opgeven. De vorige analyse functies moeten worden gebruikt als-is. Met een aangepaste analyse kunt u kiezen welke tokenizers-en Token filters u wilt gebruiken. <br><br>Een aanbevolen combi natie is het [tref woord tokenizer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) met een [token filter met een lagere Case](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html). De vooraf gedefinieerde [trefwoord analyse](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) biedt geen kleine letters in hoofd letters, wat kan leiden tot het mislukken van query's. Een aangepaste analyse functie biedt u een mechanisme voor het toevoegen van het filter voor het laagste Case-token. |

Als u een web-API-test programma zoals postman gebruikt, kunt u de [rest-aanroep van Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) toevoegen om de getokende uitvoer te controleren.

U moet een bestaande index hebben om te kunnen werken. Als u een bestaande index en een veld met streepjes of gedeeltelijke voor waarden hebt opgegeven, kunt u verschillende analyse functies op basis van specifieke termen uitproberen om te zien welke tokens worden verzonden.  

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
1. Wijzig de aanvraag om de `whitespace` or `keyword` Analyzer te gebruiken:

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
> Houd er rekening mee dat query parsers vaak kleine letters in een zoek expressie bij het samen stellen van de query-structuur. Als u een analyse functie gebruikt die geen kleine letters bevat, en u niet de verwachte resultaten krijgt, kan dit de reden zijn. De oplossing is het toevoegen van een token filter met een lagere case, zoals beschreven in de sectie ' aangepaste analyse functies gebruiken ' hieronder.

## <a name="configure-an-analyzer"></a>Een analyse functie configureren
 
Of u analyse functies wilt evalueren of vooruit wilt gaan met een specifieke configuratie, u moet de analyse op de veld definitie opgeven en mogelijk de Analyzer zelf configureren als u geen gebruik maakt van een ingebouwde analyse functie. Bij het wisselen van analyse functies moet u doorgaans de index opnieuw samen stellen (verwijderen, opnieuw maken en opnieuw laden). 

### <a name="use-built-in-analyzers"></a>Ingebouwde analyse functies gebruiken

Ingebouwde of vooraf gedefinieerde analyse functies kunnen worden opgegeven met de naam van een `analyzer` eigenschap van een veld definitie, zonder dat er aanvullende configuratie is vereist in de index. In het volgende voor beeld ziet u hoe u het `whitespace` analyseprogramma op een veld kunt instellen. 

Zie [vooraf gedefinieerde analyse lijsten](https://docs.microsoft.com/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)voor andere scenario's en meer informatie over andere ingebouwde analyse functies. 

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

### <a name="use-custom-analyzers"></a>Aangepaste analyse functies gebruiken

Als u een [aangepaste Analyzer](index-add-custom-analyzers.md)gebruikt, definieert u deze in de index met een door de gebruiker gedefinieerde combi natie van tokenizer, token filter, met mogelijke configuratie-instellingen. Vervolgens verwijzen we naar de definitie van een veld, net zoals u een ingebouwde Analyzer zou hebben.

Wanneer het doel van de volledige-termijn tokening is, wordt een aangepaste analyse functie aanbevolen die bestaat uit een **sleutel woord tokenizer** en een filter voor een **laag hoofdletter gebruik** .

+ Het tref woord tokenizer maakt één token voor de volledige inhoud van een veld.
+ Met het token filter met kleine letters worden hoofd letters getransformeerd in tekst met een kleine letter. Query-parsers bevatten doorgaans kleine letters tekst invoer. Met een lagere behuizing homogenizes u de invoer met de tokens-termen.

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
> Het `keyword_v2` tokenizer- `lowercase` en Token filter zijn bekend bij het systeem en met behulp van de standaard configuraties. Daarom kunt u ernaar verwijzen met de naam zonder dat u ze eerst hoeft te definiëren.

## <a name="build-and-test"></a>Bouwen en testen

Zodra u een index hebt gedefinieerd met analyse functies en veld definities die uw scenario ondersteunen, laadt u documenten met representatieve teken reeksen zodat u gedeeltelijke teken reeks query's kunt testen. 

In de vorige secties is de logica uitgelegd. In deze sectie wordt elke API beschreven die u moet aanroepen bij het testen van uw oplossing. Zoals eerder is vermeld, kunt u deze taken snel door lopen als u een interactief hulp programma voor het testen van een web gebruikt, zoals een bericht.

+ [Delete index](https://docs.microsoft.com/rest/api/searchservice/delete-index) verwijdert een bestaande index met dezelfde naam, zodat u deze opnieuw kunt maken.

+ Met [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) maakt u de index structuur in uw zoek service, met inbegrip van analyse definities en velden met een analyse specificatie.

+ Met [documenten laden](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) worden documenten geïmporteerd die dezelfde structuur hebben als uw index, evenals Doorzoek bare inhoud. Na deze stap is de index gereed om query's uit te proberen of te testen.

+ [Test Analyzer](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) is geïntroduceerd in [een Analyzer kiezen](#choose-an-analyzer). Test enkele van de teken reeksen in uw index met behulp van verschillende analyse functies om te begrijpen hoe de termen worden getokend.

+ In [documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) wordt uitgelegd hoe u een query aanvraag opbouwt met behulp van [eenvoudige syntaxis](query-simple-syntax.md) of de [volledige lucene-syntaxis](query-lucene-syntax.md) voor joker tekens en reguliere expressies.

  Voor gedeeltelijke term query's, zoals het opvragen van ' 3-6214 ' om een overeenkomst op ' + 1 (425) 703-6214 ' te vinden, kunt u de eenvoudige syntaxis gebruiken `search=3-6214&queryType=simple`:.

  Voor infix-en achtervoegsel query's, zoals het opvragen van ' num ' of ' numeriek ' om een overeenkomst te vinden op ' alfanumeriek ', gebruikt u de syntaxis Full lucene en een reguliere expressie:`search=/.*num.*/&queryType=full`

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

### <a name="use-different-analyzers-for-indexing-and-query-processing"></a>Verschillende analyse functies gebruiken voor het verwerken van indexen en query's

Analyse functies worden aangeroepen tijdens het indexeren en tijdens de uitvoering van query's. Het is gebruikelijk om dezelfde Analyzer voor beide te gebruiken, maar u kunt voor elke werk belasting aangepaste analyse functies configureren. Analyzer-onderdrukkingen worden opgegeven in de [index definitie](https://docs.microsoft.com/rest/api/searchservice/create-index) in een `analyzers` sectie en vervolgens naar specifieke velden wordt verwezen. 

Wanneer aangepaste analyse alleen is vereist tijdens het indexeren, kunt u de aangepaste Analyzer Toep assen om alleen de standaard-lucene Analyzer (of een andere analyse) voor query's te indexeren en te blijven gebruiken.

Als u een Role-specifieke analyse wilt opgeven, kunt u eigenschappen instellen voor het veld voor elk `indexAnalyzer` van `searchAnalyzer` de instellingen, in `analyzer` plaats van de standaard eigenschap.

```json
"name": "featureCode",
"indexAnalyzer":"my_customanalyzer",
"searchAnalyzer":"standard",
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe analyse functies beide bijdragen om problemen op te vragen en query problemen op te lossen. Ga als volgt te werk om de analyse-impact op indexering en query verwerking nader te bekijken. In het bijzonder kunt u overwegen de analyse tekst-API te gebruiken voor het retour neren van een tokenve uitvoer zodat u precies ziet wat een Analyzer is voor uw index.

+ [Taalanalyse](search-language-support.md)
+ [Analyse functies voor tekst verwerking in azure Cognitive Search](search-analyzers.md)
+ [Tekst-API analyseren (REST)](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)
+ [Hoe zoeken in volledige tekst werkt (query architectuur)](search-lucene-query-architecture.md)