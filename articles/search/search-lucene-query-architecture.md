---
title: Full text query en indexering motor architectuur (Lucene)
titleSuffix: Azure Cognitive Search
description: Onderzoekt lucene queryverwerking en documentretrievalconcepten voor zoeken in volledige tekst, gerelateerd aan Azure Cognitive Search.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d46d0309b3d2ffb638016e88ba022e49009eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282937"
---
# <a name="how-full-text-search-works-in-azure-cognitive-search"></a>Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search

Dit artikel is bedoeld voor ontwikkelaars die een beter inzicht nodig hebben in hoe full text search in Azure Cognitive Search werkt. Voor tekstquery’s worden in de meeste scenario’s in Azure Cognitive Search soepel de verwachte resultaten geretourneerd, maar zo nu en dan krijgt u mogelijk een resultaat dat niet helemaal klopt. In dergelijke situaties helpt het iets af te weten van de vier stadia van Lucene-queryuitvoering (query’s parseren, lexicale analyse, documentovereenkomsten, scoren). Dit kan u helpen specifieke wijzigingen in de queryparameters of indexconfiguratie te identificeren, die leiden tot het verwachte resultaat. 

> [!Note] 
> Azure Cognitive Search gebruikt Lucene voor full text search, maar lucene-integratie is niet uitputtend. We stellen lucene-functionaliteit selectief bloot en breiden deze uit om de scenario's mogelijk te maken die belangrijk zijn voor Azure Cognitive Search. 

## <a name="architecture-overview-and-diagram"></a>Architectuuroverzicht en diagram

Het verwerken van een zoekopdracht met volledige tekst begint met het ontzeggen van de querytekst om zoektermen te extraheren. De zoekmachine gebruikt een index om documenten op te halen met overeenkomende termen. Individuele querytermen worden soms opgesplitst en opnieuw samengesteld in nieuwe vormen om een breder net te werpen over wat als een potentiële overeenkomst kan worden beschouwd. Een resultaatset wordt vervolgens gesorteerd op een relevantiescore die is toegewezen aan elk afzonderlijk overeenkomend document. Degenen bovenaan de gerangschikte lijst worden teruggestuurd naar de oproeptoepassing.

Aangepast, queryuitvoering heeft vier fasen: 

1. Queryontring 
2. Lexicale analyse 
3. Ophalen van documenten 
4. Scoren 

Het onderstaande diagram illustreert de componenten die worden gebruikt om een zoekaanvraag te verwerken. 

 ![Lucene-queryarchitectuurdiagram in Azure Cognitive Search][1]


| Belangrijkste onderdelen | Functionele beschrijving | 
|----------------|------------------------|
|**Queryparsers** | Afzonderlijke querytermen van queryoperatoren en maak de querystructuur (een querystructuur) die naar de zoekmachine moet worden verzonden. |
|**Analyses** | Lexicale analyse uitvoeren op queryvoorwaarden. Dit proces kan bestaan uit het transformeren, verwijderen of uitbreiden van querytermen. |
|**Index** | Een efficiënte gegevensstructuur die wordt gebruikt om doorzoekbare termen op te slaan en te ordenen die zijn geëxtraheerd uit geïndexeerde documenten. |
|**Zoekmachine** | Hiermee worden overeenkomende documenten opgehaald en scores op basis van de inhoud van de omgekeerde index. |

## <a name="anatomy-of-a-search-request"></a>Anatomie van een zoekaanvraag

Een zoekaanvraag is een volledige specificatie van wat moet worden geretourneerd in een resultaatset. In eenvoudigste vorm, het is een lege query met geen criteria van welke aard dan ook. Een realistischer voorbeeld bevat parameters, verschillende querytermen, misschien afgestemd op bepaalde velden, met mogelijk een filterexpressie en bestelregels.  

Het volgende voorbeeld is een zoekverzoek dat u met behulp van de [REST-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)naar Azure Cognitive Search verzenden.  

~~~~
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "Spacious, air-condition* +\"Ocean view\"",
    "searchFields": "description, title",
    "searchMode": "any",
    "filter": "price ge 60 and price lt 300",
    "orderby": "geo.distance(location, geography'POINT(-159.476235 22.227659)')", 
    "queryType": "full" 
}
~~~~

Voor dit verzoek doet de zoekmachine het volgende:

1. Filtert documenten waar de prijs ten minste $ 60 en minder dan $ 300 is.
2. Hiermee wordt de query uitgevoerd. In dit voorbeeld bestaat de zoekopdracht uit `"Spacious, air-condition* +\"Ocean view\""` zinnen en termen: (gebruikers voeren meestal geen interpunctie in, maar door deze in het voorbeeld op te nemen, kunnen we uitleggen hoe analysers ermee omgaan). Voor deze query scant de zoekmachine de beschrijvings- en titelvelden die zijn opgegeven voor `searchFields` documenten die "Ocean view" bevatten, en bovendien op de term "ruim", of op termen die beginnen met het voorvoegsel "airconditioning". De `searchMode` parameter wordt gebruikt om op elke term (standaard) of allemaal te matchen, voor gevallen waarin een term niet expliciet vereist is (`+`).
3. Bestelt de resulterende set hotels op de nabijheid van een bepaalde locatie voor geografie en wordt vervolgens teruggestuurd naar de oproeptoepassing. 

Het merendeel van dit artikel gaat over `"Spacious, air-condition* +\"Ocean view\""`de verwerking van de *zoekopdracht:*. Filteren en bestellen vallen buiten het bereik. Zie de [referentiedocumentatie voor de zoek-API](https://docs.microsoft.com/rest/api/searchservice/search-documents)voor meer informatie .

<a name="stage1"></a>
## <a name="stage-1-query-parsing"></a>Fase 1: Ontwering van query's 

Zoals opgemerkt, is de querytekenreeks de eerste regel van de aanvraag: 

~~~~
 "search": "Spacious, air-condition* +\"Ocean view\"", 
~~~~

De queryparser scheidt operatoren `*` `+` (zoals en in het voorbeeld) van zoektermen en deconstrueert de zoekopdracht in *subquery's* van een ondersteund type: 

+ *term query* voor zelfstandige termen (zoals ruim)
+ *woordgroep query* voor geciteerde termen (zoals oceaanweergave)
+ *voorvoegselquery* voor termen gevolgd `*` door een voorvoegseloperator (zoals airconditioning)

Zie De syntaxis van [Lucene query voor](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) een volledige lijst met ondersteunde querytypen

Operators die aan een subquery zijn gekoppeld, bepalen of de query "moet zijn" of "moet worden" voldaan om een document als een overeenkomst te laten worden beschouwd. Bijvoorbeeld, `+"Ocean view"` is "must" te `+` wijten aan de exploitant. 

De queryparser herstructureert de subquery's in een *querystructuur* (een interne structuur die de query vertegenwoordigt) doorgeeft aan de zoekmachine. In de eerste fase van queryparsing ziet de querystructuur er als volgt uit.  

 ![Booleaanse query zoekmodus elke][2]

### <a name="supported-parsers-simple-and-full-lucene"></a>Ondersteunde parsers: Simple en Full Lucene 

 Azure Cognitive Search legt twee `simple` verschillende querytalen `full`bloot( standaard) en . Door de `queryType` parameter in te stellen met uw zoekaanvraag, vertelt u de queryparser welke querytaal u kiest, zodat deze weet hoe u de operatoren en syntaxis moet interpreteren. De [eenvoudige querytaal](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) is intuïtief en robuust, vaak geschikt om gebruikersinvoer te interpreteren zoals dit is zonder verwerking aan de clientzijde. Het ondersteunt query operators bekend van web zoekmachines. De [volledige Lucene-querytaal](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search), `queryType=full`die u krijgt door in te stellen , breidt de standaardeenvoudige querytaal uit door ondersteuning toe te voegen voor meer operatoren en querytypen zoals wildcard-, fuzzy-, regex- en veldscopequery's. Een reguliere expressie die in De syntaxis van eenvoudige query wordt verzonden, wordt bijvoorbeeld geïnterpreteerd als een querytekenreeks en niet als expressie. In het voorbeeldverzoek in dit artikel wordt de querytaal Volledig Lucene gebruikt.

### <a name="impact-of-searchmode-on-the-parser"></a>Impact van searchMode op de parser 

Een andere parameter voor zoekaanvragen die `searchMode` van invloed is op parsing is de parameter. Hiermee wordt de standaardoperator voor Booleaanse query's ingesteld: elke (standaard) of alle query's.  

Wanneer `searchMode=any`, wat de standaardis, de ruimtescheidingtussen ruime en`||`airconditioning IS OF ( ) waardoor de voorbeeldquerytekst gelijk is aan: 

~~~~
Spacious,||air-condition*+"Ocean view" 
~~~~

Expliciete operatoren, `+` `+"Ocean view"`zoals in, zijn ondubbelzinnig in booleaanse queryconstructie (de term *moet* overeenkomen). Minder voor de hand liggende is hoe de resterende termen te interpreteren: ruime en airconditioning. Moet de zoekmachine vinden wedstrijden op de oceaan uitzicht *en* ruime *en* airconditioning? Of moet het vinden uitzicht op de oceaan plus *een* van de resterende termen? 

Standaard (`searchMode=any`), de zoekmachine neemt de bredere interpretatie. Beide velden *moeten* worden afgestemd, als gevolg van "of" semantiek. De eerste querystructuur die eerder is geïllustreerd, met de twee 'moeten'-bewerkingen, toont de standaardinstelling.  

Stel dat we `searchMode=all`nu ingesteld . In dit geval wordt de ruimte geïnterpreteerd als een "en" bewerking. Elk van de overige termen moet beide in het document aanwezig zijn om als match in aanmerking te komen. De resulterende voorbeeldquery wordt als volgt geïnterpreteerd: 

~~~~
+Spacious,+air-condition*+"Ocean view"
~~~~

Een gewijzigde querystructuur voor deze query is als volgt, waarbij een overeenkomend document het snijpunt is van alle drie de subquery's: 

 ![Booleaanse query zoekmodus alle][3]

> [!Note] 
> Het `searchMode=any` kiezen `searchMode=all` over is een beslissing die het best kan worden genomen door representatieve query's uit te voeren. Gebruikers die waarschijnlijk operators opnemen (vaak bij het zoeken in `searchMode=all` documentopslag) kunnen resultaten intuïtiever vinden als ze booleaanse queryconstructies informeert. Zie Syntaxis van `searchMode` de query [eenvoudig](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)voor meer informatie over het samenspel tussen en operatoren.

<a name="stage2"></a>
## <a name="stage-2-lexical-analysis"></a>Fase 2: Lexicale analyse 

Lexicale analysators verwerken *termquery's* en *woordgroepquery's* nadat de querystructuur is gestructureerd. Een analyzer accepteert de tekstingangen die de parser aan haar heeft gegeven, verwerkt de tekst en stuurt vervolgens tokenized termen terug die in de querystructuur moeten worden opgenomen. 

De meest voorkomende vorm van lexicale analyse is *taalkundige analyse* die querytermen transformeert op basis van regels die specifiek zijn voor een bepaalde taal: 

* Een queryterm reduceren tot de hoofdvorm van een woord 
* Niet-essentiële woorden verwijderen (stopwoorden, zoals 'de' of 'en' in het Engels) 
* Een samengesteld woord opsplitsen in onderdelen 
* Onderste behuizing een hoofdletterwoord 

Al deze bewerkingen hebben de neiging om verschillen tussen de tekstinvoer van de gebruiker en de termen die in de index zijn opgeslagen, te wissen. Dergelijke bewerkingen gaan verder dan tekstverwerking en vereisen diepgaande kennis van de taal zelf. Om deze laag van taalbewustzijn toe te voegen, ondersteunt Azure Cognitive Search een lange lijst met [taalanalysers](https://docs.microsoft.com/rest/api/searchservice/language-support) van zowel Lucene als Microsoft.

> [!Note]
> Analysevereisten kunnen variëren van minimaal tot uitgebreid, afhankelijk van uw scenario. U de complexiteit van lexicale analyse regelen door een van de vooraf gedefinieerde analysatoren te selecteren of door uw eigen [aangepaste analyzer te](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search)maken. Analysators zijn scoped to doorzoekbare velden en worden opgegeven als onderdeel van een velddefinitie. Hierdoor u de lexicale analyse per veld variëren. Niet gespecificeerd, de *standaard* Lucene analyzer wordt gebruikt.

In ons voorbeeld heeft de oorspronkelijke querystructuur voorafgaand aan de analyse de term 'Ruim', met een hoofdletter 'S' en een komma die de queryparser interpreteert als onderdeel van de queryterm (een komma wordt niet beschouwd als een operator van querytalen).  

Wanneer de standaardanalyzer de term verwerkt, zal het kleine letters "ocean view" en "ruim", en verwijder de komma karakter. De gewijzigde querystructuur ziet er als volgt uit: 

 ![Booleaanse query met geanalyseerde termen][4]

### <a name="testing-analyzer-behaviors"></a>Het testen van analysegedrag 

Het gedrag van een analyzer kan worden getest met behulp van de [Api Analyseren.](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) Geef de tekst op die u wilt analyseren om te zien welke termen de analyzer zal genereren. Als u bijvoorbeeld wilt zien hoe de standaardanalyzer de tekst 'airconditioning' zou verwerken, u de volgende aanvraag indienen:

~~~~
{
    "text": "air-condition",
    "analyzer": "standard"
}
~~~~

De standaardanalyzer breekt de invoertekst in de volgende twee tokens en annoteert deze met kenmerken zoals begin- en eindcompensaties (gebruikt voor hithighlighting) en hun positie (gebruikt voor woordgroepmatching):

~~~~
{
  "tokens": [
    {
      "token": "air",
      "startOffset": 0,
      "endOffset": 3,
      "position": 0
    },
    {
      "token": "condition",
      "startOffset": 4,
      "endOffset": 13,
      "position": 1
    }
  ]
}
~~~~

<a name="exceptions"></a>

### <a name="exceptions-to-lexical-analysis"></a>Uitzonderingen op lexicale analyse 

Lexicale analyse is alleen van toepassing op querytypen waarvoor volledige termen nodig zijn: een termquery of een woordgroepquery. Het is niet van toepassing op querytypen met onvolledige termen - voorvoegquery, wildcardquery, regexquery - of op een vage query. Deze querytypen, waaronder de voorvoegselquery met term `air-condition*` in ons voorbeeld, worden rechtstreeks aan de querystructuur toegevoegd en omzeilen de analysefase. De enige transformatie die wordt uitgevoerd op queryvoorwaarden van deze typen, is een lagere behuizing.

<a name="stage3"></a>

## <a name="stage-3-document-retrieval"></a>Fase 3: Ophalen van documenten 

Het ophalen van documenten verwijst naar het vinden van documenten met overeenkomende termen in de index. Deze fase wordt het best begrepen door middel van een voorbeeld. Laten we beginnen met een hotels-index met het volgende eenvoudige schema: 

~~~~
{
    "name": "hotels",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "title", "type": "Edm.String", "searchable": true },
        { "name": "description", "type": "Edm.String", "searchable": true }
    ] 
} 
~~~~

Ga er verder van uit dat deze index de volgende vier documenten bevat: 

~~~~
{
    "value": [
        {
            "id": "1",
            "title": "Hotel Atman",
            "description": "Spacious rooms, ocean view, walking distance to the beach."
        },
        {
            "id": "2",
            "title": "Beach Resort",
            "description": "Located on the north shore of the island of Kauaʻi. Ocean view."
        },
        {
            "id": "3",
            "title": "Playa Hotel",
            "description": "Comfortable, air-conditioned rooms with ocean view."
        },
        {
            "id": "4",
            "title": "Ocean Retreat",
            "description": "Quiet and secluded"
        }
    ]
}
~~~~

**Hoe termen worden geïndexeerd**

Om het ophalen te begrijpen, helpt het om een paar basisprincipes over indexeren te kennen. De opslageenheid is een omgekeerde index, één voor elk doorzoekbaar veld. Binnen een omgekeerde index is een gesorteerde lijst van alle termen van alle documenten. Elke term wordt toegewezen aan de lijst van documenten waarin het voorkomt, zoals blijkt uit het onderstaande voorbeeld.

Om de termen in een omgekeerde index te produceren, voert de zoekmachine lexicale analyses uit over de inhoud van documenten, vergelijkbaar met wat er gebeurt tijdens queryverwerking:

1. *Tekstingangen* worden doorgegeven aan een analyzer, lager-cased, ontdaan van interpunctie, enzovoort, afhankelijk van de configuratie van de analyzer. 
2. *Tokens* zijn de output van tekstanalyse.
3. *Termen* worden toegevoegd aan de index.

Het is gebruikelijk, maar niet vereist, om dezelfde analysers te gebruiken voor zoek- en indexeringsbewerkingen, zodat querytermen meer lijken op termen in de index.

> [!Note]
> Met Azure Cognitive Search u verschillende analysers `indexAnalyzer` `searchAnalyzer` opgeven voor indexering en zoeken via aanvullende en veldparameters. Als deze niet is opgegeven, `analyzer` wordt de analyzer-set met de eigenschap gebruikt voor zowel indexeren als zoeken.  

**Omgekeerde index bijvoorbeeld documenten**

Terugkerend naar ons voorbeeld, voor het **titelveld,** ziet de omgekeerde index er als volgt uit:

| Termijn | Documentlijst |
|------|---------------|
| Atman | 1 |
| Strand | 2 |
| Hotel | 1, 3 |
| Oceaan | 4  |
| Playa | 3 |
| Resort | 3 |
| Terugtrekken | 4 |

In het titelveld verschijnt alleen *het hotel* in twee documenten: 1, 3.

Voor het **beschrijvingsveld** is de index als volgt:

| Termijn | Documentlijst |
|------|---------------|
| Lucht | 3
| en | 4
| Strand | 1
| Geconditioneerd | 3
| Comfortabele | 3
| afstand | 1
| Eiland | 2
| kauaーi kauaーi | 2
| Gelegen | 2
| noord | 2
| Oceaan | 1, 2, 3
| van | 2
| op |2
| stille | 4
| Kamers  | 1, 3
| Afgelegen | 4
| Kust | 2
| Ruime | 1
| het | 1, 2
| tot | 1
| weergave | 1, 2, 3
| Wandelen | 1
| wordt uitgevoerd met | 3


**Querytermen afstemmen op geïndexeerde termen**

Gezien de omgekeerde indexen hierboven, laten we terugkeren naar de voorbeeldquery en zien hoe overeenkomende documenten worden gevonden voor onze voorbeeldquery. Bedenk dat de uiteindelijke querystructuur er als volgt uitziet: 

 ![Booleaanse query met geanalyseerde termen][4]

Tijdens queryuitvoering worden afzonderlijke query's onafhankelijk uitgevoerd tegen de doorzoekbare velden. 

+ De TermQuery, "ruim", komt overeen met document 1 (Hotel Atman). 

+ De PrefixQuery, "airconditioning*", komt niet overeen met documenten. 

  Dit is een gedrag dat ontwikkelaars soms in verwarring brengt. Hoewel de term airconditioning in het document bestaat, wordt deze door de standaardanalyzer in twee termen opgesplitst. Bedenk dat voorvoegselquery's, die gedeeltelijke termen bevatten, niet worden geanalyseerd. Daarom worden termen met voorvoegsel "airconditioning" opgezocht in de omgekeerde index en niet gevonden.

+ De PhraseQuery, "ocean view", zoekt de termen "oceaan" en "view" op en controleert de nabijheid van termen in het oorspronkelijke document. Documenten 1, 2 en 3 komen overeen met deze query in het beschrijvingsveld. Kennisgeving document 4 heeft de term oceaan in de titel, maar wordt niet beschouwd als een overeenkomst, omdat we op zoek zijn naar de "oceaan view" zin in plaats van individuele woorden. 

> [!Note]
> Een zoekopdracht wordt onafhankelijk uitgevoerd tegen alle doorzoekbare velden in de Azure Cognitive `searchFields` Search-index, tenzij u de velden die zijn ingesteld met de parameter beperkt, zoals geïllustreerd in de voorbeeldzoekaanvraag. Documenten die overeenkomen in een van de geselecteerde velden, worden geretourneerd. 

Over het geheel genomen zijn de documenten die overeenkomen met de documenten die overeenkomen 1, 2, 3. 

## <a name="stage-4-scoring"></a>Fase 4: Scoren  

Aan elk document in een set zoekresultaten wordt een relevantiescore toegewezen. De functie van de relevantiescore is om hoger te rangschikken die documenten die het beste antwoord op een vraag van de gebruiker, zoals uitgedrukt door de zoekopdracht. De score wordt berekend op basis van statistische eigenschappen van termen die overeenkomen. De kern van de scoreformule is [TF/IDF (term frequency-inverse document frequency)](https://en.wikipedia.org/wiki/Tf%E2%80%93idf). In query's met zeldzame en algemene termen bevordert TF/IDF resultaten met de zeldzame term. Bijvoorbeeld, in een hypothetische index met alle Wikipedia-artikelen, uit documenten die overeenkomen met de query *van de president*, documenten overeenkomen op *president* worden beschouwd als relevanter dan documenten overeenkomen op *de*.


### <a name="scoring-example"></a>Voorbeeld van scoren

Herinner de drie documenten terug die overeenkomen met onze voorbeeldquery:
~~~~
search=Spacious, air-condition* +"Ocean view"  
~~~~
~~~~
{
  "value": [
    {
      "@search.score": 0.25610128,
      "id": "1",
      "title": "Hotel Atman",
      "description": "Spacious rooms, ocean view, walking distance to the beach."
    },
    {
      "@search.score": 0.08951007,
      "id": "3",
      "title": "Playa Hotel",
      "description": "Comfortable, air-conditioned rooms with ocean view."
    },
    {
      "@search.score": 0.05967338,
      "id": "2",
      "title": "Ocean Resort",
      "description": "Located on a cliff on the north shore of the island of Kauai. Ocean view."
    }
  ]
}
~~~~

Document 1 kwam het beste overeen met de query omdat zowel de term *ruim* als de vereiste zin *oceaanweergave* in het beschrijvingsveld voorkomen. De volgende twee documenten komen alleen overeen met de zinsnede *oceaanweergave.* Het is misschien verrassend dat de relevantiescore voor document 2 en 3 anders is, ook al kwamen ze op dezelfde manier overeen met de query. Het is omdat de scoring formule heeft meer componenten dan alleen TF / IDF. In dit geval kreeg document 3 een iets hogere score toegewezen omdat de beschrijving korter is. Lees meer over [de praktische scoringsformule van Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/search/similarities/TFIDFSimilarity.html) om te begrijpen hoe veldlengte en andere factoren de relevantiescore kunnen beïnvloeden.

Sommige querytypen (wildcard, voorvoegsel, regex) dragen altijd een constante score bij aan de totale documentscore. Hierdoor kunnen overeenkomsten gevonden door middel van query-uitbreiding worden opgenomen in de resultaten, maar zonder dat de ranking. 

Een voorbeeld illustreert waarom dit belangrijk is. Wildcard-zoekopdrachten, inclusief voorvoegselzoekopdrachten, zijn per definitie dubbelzinnig omdat de invoer een gedeeltelijke tekenreeks is met potentiële overeenkomsten op een zeer groot aantal uiteenlopende termen (overweeg een invoer van "tour*", met overeenkomsten die worden gevonden op "tours", "tourettes" en " tourmaline"). Gezien de aard van deze resultaten, is er geen manier om redelijkerwijs af te leiden welke termen waardevoller zijn dan andere. Daarom negeren we termfrequenties bij het scoren van resultaten in query's van typen wildcard, voorvoegsel en regex. In een meerdelige zoekaanvraag met gedeeltelijke en volledige termen worden de resultaten van de gedeeltelijke invoer opgenomen met een constante score om vooringenomenheid naar mogelijk onverwachte overeenkomsten te voorkomen.

### <a name="score-tuning"></a>Scoretuning

Er zijn twee manieren om relevantiescores in Azure Cognitive Search af te stemmen:

1. **Scoreprofielen** promoten documenten in de gerangschikte lijst met resultaten op basis van een reeks regels. In ons voorbeeld kunnen we documenten die overeenkomen met documenten die overeenkomen in het titelveld relevanter vinden dan documenten die overeenkomen met het beschrijvingsveld. Bovendien, als onze index had een prijs veld voor elk hotel, konden we bevorderen documenten met een lagere prijs. Meer informatie over het [toevoegen van scoreprofielen aan een zoekindex.](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
2. **Term boosting** (alleen beschikbaar in de syntaxis van de `^` query Volledig Lucene) biedt een stimulerende operator die kan worden toegepast op een deel van de querystructuur. In ons voorbeeld, in plaats van te zoeken op het voorvoegsel *airconditioning*\*, kan men zoeken naar de exacte term *airconditioning* of het voorvoegsel, maar documenten die overeenkomen met de exacte term worden hoger gerangschikt door het toepassen van boost op de term query: * air-condition ^2|| airconditioning**. Meer informatie over [term boosting](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search#bkmk_termboost).


### <a name="scoring-in-a-distributed-index"></a>Scoren in een gedistribueerde index

Alle indexen in Azure Cognitive Search worden automatisch opgesplitst in meerdere shards, waardoor we de index snel over meerdere knooppunten kunnen verdelen tijdens het opschalen of schalen van de service. Wanneer een zoekverzoek wordt uitgegeven, wordt deze onafhankelijk van elkaar uitgegeven tegen elke scherf. De resultaten van elke scherf worden vervolgens samengevoegd en geordend op score (als er geen andere volgorde wordt gedefinieerd). Het is belangrijk om te weten dat de scorefunctie de termfrequentie van de query opvraagt tegen de omgekeerde documentfrequentie in alle documenten binnen de shard, niet over alle scherven!

Dit betekent dat een relevantiescore voor identieke documenten anders *kan* zijn als ze zich op verschillende scherven bevinden. Gelukkig, dergelijke verschillen hebben de neiging om te verdwijnen als het aantal documenten in de index groeit als gevolg van meer gelijkmatige termijn verdeling. Het is niet mogelijk om aan te nemen op welke shard een bepaald document zal worden geplaatst. Ervan uitgaande dat een documentsleutel niet verandert, wordt deze echter altijd aan dezelfde shard toegewezen.

In het algemeen is documentscore niet het beste kenmerk voor het bestellen van documenten als de stabiliteit van de bestelling belangrijk is. Bijvoorbeeld, gezien twee documenten met een identieke score, is er geen garantie welke het eerst wordt weergegeven in de volgende uitvoeringen van dezelfde query. Documentscore mag alleen een algemeen gevoel van relevantie van documenten geven ten opzichte van andere documenten in de resultatenset.

## <a name="conclusion"></a>Conclusie

Het succes van internet zoekmachines heeft verwachtingen gewekt voor full text search over prive-gegevens. Voor bijna elke vorm van zoekervaring verwachten we nu dat de engine onze intentie begrijpt, zelfs wanneer termen verkeerd zijn gespeld of onvolledig. We kunnen zelfs overeenkomsten verwachten op basis van bijna gelijkwaardige termen of synoniemen die we nooit daadwerkelijk hebben opgegeven.

Vanuit technisch oogpunt is full text search zeer complex, wat een uitgekiende taalkundige analyse en een systematische benadering van verwerking vereist op manieren die querytermen destilleren, uitbreiden en transformeren om een relevant resultaat te leveren. Gezien de inherente complexiteit, zijn er een heleboel factoren die de uitkomst van een query kunnen beïnvloeden. Om deze reden, het investeren van de tijd om de mechanica van full text search te begrijpen biedt tastbare voordelen bij het proberen om te werken door middel van onverwachte resultaten.  

In dit artikel wordt gezocht naar volledige tekst onderzocht in de context van Azure Cognitive Search. We hopen dat het u voldoende achtergrondinformatie geeft om mogelijke oorzaken en oplossingen voor het oplossen van veelvoorkomende queryproblemen te herkennen. 

## <a name="next-steps"></a>Volgende stappen

+ Bouw de voorbeeldindex, probeer verschillende query's uit en bekijk de resultaten. Zie Een index in de portal voor instructies [uitvoeren en opvragen.](search-get-started-portal.md#query-index)

+ Probeer aanvullende querysyntaxis uit de sectie [Voorbeeld van zoekdocumenten](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) of uit [De syntaxis van eenvoudige query's](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) in Zoekverkenner in de portal.

+ Controleer [scoreprofielen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) als u de rangschikking in uw zoektoepassing wilt afstemmen.

+ Leer hoe u [taalspecifieke lexicale analysers](https://docs.microsoft.com/rest/api/searchservice/language-support)toepast.

+ [Configureer aangepaste analyseapparaten](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search) voor minimale verwerking of gespecialiseerde verwerking op specifieke velden.

## <a name="see-also"></a>Zie ook

[REST API voor documenten zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

[Vereenvoudigde querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

[Volledige Lucene-querysyntaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 

[Zoekresultaten verwerken](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
[2]: ./media/search-lucene-query-architecture/azSearch-queryparsing-should2.png
[3]: ./media/search-lucene-query-architecture/azSearch-queryparsing-must2.png
[4]: ./media/search-lucene-query-architecture/azSearch-queryparsing-spacious2.png
