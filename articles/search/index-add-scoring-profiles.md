---
title: De zoekpositie verhogen met scoreprofielen
titleSuffix: Azure Cognitive Search
description: Verhoog de score voor zoekresultaten voor Azure Cognitive Search-resultaten door scoreprofielen toe te voegen.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/28/2019
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
ms.openlocfilehash: 516637b812afece1966006ce6d894dd1e32e6293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245458"
---
# <a name="add-scoring-profiles-to-an-azure-cognitive-search-index"></a>Scoreprofielen toevoegen aan een Azure Cognitive Search-index

  Scoren verwijst naar de berekening van een *zoekscore* voor elk item dat wordt geretourneerd in zoekresultaten. De score is een indicatie van de relevantie van een item in de context van de huidige zoekbewerking. Hoe hoger de score, hoe relevanter het item. In zoekresultaten worden items gerangschikt van hoog naar laag, op basis van de zoekscores die voor elk item zijn berekend.  

 Azure Cognitive Search gebruikt standaardscores om een eerste score te berekenen, maar u de berekening aanpassen via een *scoreprofiel*. Scoreprofielen geven u meer controle over de rangschikking van items in zoekresultaten. U bijvoorbeeld items promoten op basis van hun inkomstenpotentieel, nieuwere artikelen promoten of mogelijk artikelen stimuleren die te lang in voorraad zijn geweest.  

 Een scoreprofiel maakt deel uit van de indexdefinitie, samengesteld uit gewogen velden, functies en parameters.  

 Om u een idee te geven van hoe een scoreprofiel eruit ziet, toont het volgende voorbeeld een eenvoudig profiel met de naam 'geo'. Deze verhoogt items met de zoekterm in het veld **hotelNaam.** Het maakt `distance` ook gebruik van de functie om items die binnen tien kilometer van de huidige locatie gunst. Als iemand zoekt op de term 'inn', en 'inn' toevallig deel uitmaakt van de hotelnaam, worden documenten met hotels met 'inn' binnen een straal van 10 km van de huidige locatie hoger in de zoekresultaten weergegeven.  


```json
"scoringProfiles": [
  {  
    "name":"geo",
    "text": {  
      "weights": {  
        "hotelName": 5
      }                              
    },
    "functions": [
      {  
        "type": "distance",
        "boost": 5,
        "fieldName": "location",
        "interpolation": "logarithmic",
        "distance": {
          "referencePointParameter": "currentLocation",
          "boostingDistance": 10
        }                        
      }                                      
    ]                     
  }            
]
```  


 Als u dit scoreprofiel wilt gebruiken, wordt uw query geformuleerd om het profiel op de querytekenreeks op te geven. In de onderstaande query `scoringProfile=geo` ziet u de queryparameter in de aanvraag.  

```  
GET /indexes/hotels/docs?search=inn&scoringProfile=geo&scoringParameter=currentLocation--122.123,44.77233&api-version=2019-05-06 
```  

 Deze zoekopdracht zoekt op de term 'inn' en loopt door op de huidige locatie. Deze query bevat andere parameters, `scoringParameter`zoals . Queryparameters worden beschreven in [zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).  

 Klik [op Voorbeeld](#bkmk_ex) om een gedetailleerder voorbeeld van een scoreprofiel te bekijken.  

## <a name="what-is-default-scoring"></a>Wat is standaardscore?  
 Scoren berekent een zoekscore voor elk item in een gerangschikte geordende resultaatset. Elk item in een zoekresultaatset krijgt een zoekscore toegewezen en wordt vervolgens hoogste tot laagste gerangschikt. Items met de hogere scores worden teruggestuurd naar de toepassing. Standaard wordt de top 50 geretourneerd, maar `$top` u de parameter gebruiken om een kleiner of groter aantal items (tot 1000 in één reactie) terug te sturen.  

De zoekscore wordt berekend op basis van statistische eigenschappen van de gegevens en de query. Azure Cognitive Search vindt documenten die de zoektermen in de querytekenreeks bevatten (sommige of alle, afhankelijk `searchMode`van ), het begunstigen van documenten die veel exemplaren van de zoekterm bevatten. De zoekscore gaat nog hoger als de term zeldzaam is in de gegevensindex, maar gebruikelijk is in het document. De basis voor deze benadering van computerrelevantie staat bekend als [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) of term frequency-inverse document frequentie.  

 Ervan uitgaande dat er geen aangepaste sortering, resultaten worden vervolgens gerangschikt op zoekscore voordat ze worden teruggestuurd naar de aanroepen de toepassing. Als $top niet is opgegeven, worden 50 items met de hoogste zoekscore geretourneerd.  

 Zoekscorewaarden kunnen worden herhaald in een resultaatset. U bijvoorbeeld 10 objecten hebben met een score van 1,2, 20 items met een score van 1,0 en 20 items met een score van 0,5. Wanneer meerdere hits dezelfde zoekscore hebben, wordt het bestellen van dezelfde gescoorde items niet gedefinieerd en is het niet stabiel. Voer de query opnieuw uit en mogelijk ziet u items van positie verschuiven. Gezien twee items met een identieke score, is er geen garantie welke het eerst verschijnt.  

## <a name="when-to-use-custom-scoring"></a>Wanneer aangepaste scores gebruiken  
 U moet een of meer scoreprofielen maken wanneer het standaardpositiegedrag niet ver genoeg gaat om aan uw bedrijfsdoelstellingen te voldoen. U bijvoorbeeld besluiten dat zoekrelevantie de voorkeur moet geven aan nieuw toegevoegde items. U ook een veld hebben dat winstmarge bevat, of een ander veld dat het inkomstenpotentieel aangeeft. Het stimuleren van hits die voordelen opleveren voor uw bedrijf kan een belangrijke factor zijn bij de beslissing om scoreprofielen te gebruiken.  

 Op relevantie gebaseerde bestellingen worden ook geïmplementeerd via scoreprofielen. Houd rekening met pagina's met zoekresultaten die u in het verleden hebt gebruikt en waarmee u sorteren op prijs, datum, beoordeling of relevantie. In Azure Cognitive Search bepalen scoreprofielen de optie 'relevantie'. De definitie van relevantie wordt door u gecontroleerd, gebaseerd op bedrijfsdoelstellingen en het type zoekervaring dat u wilt leveren.  

##  <a name="example"></a><a name="bkmk_ex"></a>Voorbeeld  
 Zoals eerder vermeld, wordt aangepaste score geïmplementeerd via een of meer scoreprofielen die zijn gedefinieerd in een indexschema.  

 In dit voorbeeld wordt het schema van`boostGenre` `newAndHighlyRated`een index met twee scoreprofielen weergegeven ( , ). Elke query op deze index die een profiel als queryparameter bevat, gebruikt het profiel om de resultaatset te scoren.  

```json
{  
  "name": "musicstoreindex",  
  "fields": [  
    { "name": "key", "type": "Edm.String", "key": true },  
    { "name": "albumTitle", "type": "Edm.String" },  
    { "name": "albumUrl", "type": "Edm.String", "filterable": false },  
    { "name": "genre", "type": "Edm.String" },  
    { "name": "genreDescription", "type": "Edm.String", "filterable": false },  
    { "name": "artistName", "type": "Edm.String" },  
    { "name": "orderableOnline", "type": "Edm.Boolean" },  
    { "name": "rating", "type": "Edm.Int32" },  
    { "name": "tags", "type": "Collection(Edm.String)" },  
    { "name": "price", "type": "Edm.Double", "filterable": false },  
    { "name": "margin", "type": "Edm.Int32", "retrievable": false },  
    { "name": "inventory", "type": "Edm.Int32" },  
    { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }  
  ],  
  "scoringProfiles": [  
    {  
      "name": "boostGenre",  
      "text": {  
        "weights": {  
          "albumTitle": 1.5,  
          "genre": 5,  
          "artistName": 2  
        }  
      }  
    },  
    {  
      "name": "newAndHighlyRated",  
      "functions": [  
        {  
          "type": "freshness",  
          "fieldName": "lastUpdated",  
          "boost": 10,  
          "interpolation": "quadratic",  
          "freshness": {  
            "boostingDuration": "P365D"  
          }  
        },  
        {
          "type": "magnitude",  
          "fieldName": "rating",  
          "boost": 10,  
          "interpolation": "linear",  
          "magnitude": {  
            "boostingRangeStart": 1,  
            "boostingRangeEnd": 5,  
            "constantBoostBeyondRange": false  
          }  
        }  
      ]  
    }  
  ],  
  "suggesters": [  
    {  
      "name": "sg",  
      "searchMode": "analyzingInfixMatching",  
      "sourceFields": [ "albumTitle", "artistName" ]  
    }  
  ]   
}  
```  

## <a name="workflow"></a>Werkstroom  
 Als u aangepast scoregedrag wilt implementeren, voegt u een scoreprofiel toe aan het schema dat de index definieert. U maximaal 100 scoreprofielen binnen een index hebben (zie [Servicelimieten),](search-limits-quotas-capacity.md)maar u slechts één profiel op tijd opgeven in een bepaalde query.  

 Begin met de [sjabloon](#bkmk_template) in dit onderwerp.  

 Geef een naam op. Scoreprofielen zijn optioneel, maar als u er een toevoegt, is de naam vereist. Zorg ervoor dat u de naamgevingsconventies voor velden volgt (begint met een brief, vermijdt speciale tekens en gereserveerde woorden). Zie [Naamgevingsregels &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules) voor de volledige lijst.  

 De body van het scoreprofiel is opgebouwd uit gewogen velden en functies.  

|||  
|-|-|  
|**Gewichten**|Geef naam-waardeparen op die een relatief gewicht aan een veld toewijzen. In het [voorbeeld](#bkmk_ex)worden de velden AlbumTitle, genre en artistName respectievelijk 1,5, 5 en 2 geboost. Waarom is genre zo veel hoger geboost dan de anderen? Als er wordt gezocht naar gegevens die enigszins homogeen zijn (zoals `musicstoreindex`het geval is met 'genre' in de), moet u mogelijk een grotere variantie in de relatieve gewichten. Bijvoorbeeld, in `musicstoreindex`de , 'rock' verschijnt als zowel een genre en in identiek geformuleerde genre beschrijvingen. Als je wilt dat genrebeschrijving opweegt tegen genrebeschrijving, heeft het genreveld een veel hoger relatief gewicht nodig.|  
|**Functies**|Wordt gebruikt wanneer aanvullende berekeningen nodig zijn voor specifieke contexten. Geldige waarden `freshness` `magnitude`zijn `distance`, `tag`, en . Elke functie heeft parameters die uniek zijn voor het.<br /><br /> -   `freshness`moet worden gebruikt wanneer u wilt stimuleren door hoe nieuw of oud een item is. Deze functie kan alleen `datetime` worden gebruikt met velden (edm. DataTimeOffset). Let `boostingDuration` op: het kenmerk `freshness` wordt alleen met de functie gebruikt.<br />-   `magnitude`moet worden gebruikt wanneer u wilt stimuleren op basis van hoe hoog of laag een numerieke waarde is. Scenario's die vragen om deze functie omvatten het verhogen van de winstmarge, de hoogste prijs, de laagste prijs of een aantal downloads. Deze functie kan alleen worden gebruikt met dubbele en gehele velden.<br />     Voor `magnitude` de functie u het bereik omkeren, hoog naar laag, als u het omgekeerde patroon wilt (bijvoorbeeld om goedkopere artikelen meer te stimuleren dan duurdere items). Gezien een scala aan prijzen van $ 100 `boostingRangeStart` tot $ `boostingRangeEnd` 1, zou je ingesteld op 100 en op 1 om de goedkopere items te stimuleren.<br />-   `distance`moet worden gebruikt wanneer u wilt stimuleren door nabijheid of geografische locatie. Deze functie kan alleen `Edm.GeographyPoint` worden gebruikt met velden.<br />-   `tag`moet worden gebruikt wanneer u wilt stimuleren door tags in het algemeen tussen documenten en zoekopdrachten. Deze functie kan alleen `Edm.String` `Collection(Edm.String)` worden gebruikt met en velden.<br /><br /> **Regels voor het gebruik van functies**<br /><br /> Functietype`freshness`( `magnitude` `distance`, `tag` , moet kleine letters zijn.<br /><br /> Functies kunnen geen null- of lege waarden bevatten. Als u veldnaam opneemt, moet u deze op iets instellen.<br /><br /> Functies kunnen alleen worden toegepast op filterbare velden. Zie [Indexering &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) voor meer informatie over filterbare velden.<br /><br /> Functies kunnen alleen worden toegepast op velden die zijn gedefinieerd in de veldenverzameling van een index.|  

 Nadat de index is gedefinieerd, u de index samenstellen door het indexschema te uploaden, gevolgd door documenten. Zie [Index maken &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index) en Documenten [toevoegen, bijwerken of verwijderen &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) voor instructies over deze bewerkingen. Zodra de index is gebouwd, moet u een functioneel scoreprofiel hebben dat werkt met uw zoekgegevens.  

##  <a name="template"></a><a name="bkmk_template"></a>Sjabloon  
 In deze sectie worden de syntaxis en sjabloon voor scoringsprofielen weergegeven. Raadpleeg [de verwijzing naar indexkenmerken](#bkmk_indexref) in de volgende sectie voor beschrijvingen van de kenmerken.  

```  
. . .   
"scoringProfiles": [  
  {   
    "name": "name of scoring profile",   
    "text": (optional, only applies to searchable fields) {   
      "weights": {   
        "searchable_field_name": relative_weight_value (positive #'s),   
        ...   
      }   
    },   
    "functions": (optional) [  
      {   
        "type": "magnitude | freshness | distance | tag",   
        "boost": # (positive number used as multiplier for raw score != 1),   
        "fieldName": "...",   
        "interpolation": "constant | linear (default) | quadratic | logarithmic",   

        "magnitude": {
          "boostingRangeStart": #,   
          "boostingRangeEnd": #,   
          "constantBoostBeyondRange": true | false (default)
        }  

        // ( - or -)  

        "freshness": {
          "boostingDuration": "..." (value representing timespan over which boosting occurs)   
        }  

        // ( - or -)  

        "distance": {
          "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)   
          "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)   
        }   

        // ( - or -)  

        "tag": {
          "tagsParameter":  "..."(parameter to be passed in queries to specify a list of tags to compare against target field)   
        }
      }
    ],   
    "functionAggregation": (optional, applies only when functions are specified) "sum (default) | average | minimum | maximum | firstMatching"   
  }   
],   
"defaultScoringProfile": (optional) "...",   
. . .  
```  

##  <a name="index-attributes-reference"></a><a name="bkmk_indexref"></a>Verwijzing naar indexkenmerken  

> [!NOTE]  
>  Een scoringsfunctie kan alleen worden toegepast op velden die kunnen worden gefilterd.  

|Kenmerk|Beschrijving|  
|---------------|-----------------|  
|`name`|Vereist. Dit is de naam van het scoreprofiel. Het volgt dezelfde naamgevingsconventies van een veld. Het moet beginnen met een letter, mag geen stippen, dubbele punten of @ symbolen bevatten en kan niet beginnen met de zinsnede 'azureSearch' (hoofdlettergevoelig).|  
|`text`|Bevat de eigenschap gewichten.|  
|`weights`|Optioneel. Bevat naam-waardeparen die elk een veldnaam en relatief gewicht opgeven. Het relatieve gewicht moet een positief geheel getal of floating-point getal zijn.<br /><br /> Gewichten worden gebruikt om het belang van het ene doorzoekbare veld ten opzichte van het andere aan te geven.|  
|`functions`|Optioneel. Een scoringsfunctie kan alleen worden toegepast op velden die kunnen worden gefilterd.|  
|`type`|Vereist voor het scoren van functies. Geeft het type functie aan dat moet worden gebruikt. Geldige waarden zijn magnitude, versheid, afstand en tag. U meer dan één functie in elk scoreprofiel opnemen. De functienaam moet een kleine letters zijn.|  
|`boost`|Vereist voor het scoren van functies. Een positief getal dat wordt gebruikt als multiplier voor ruwe score. Het kan niet gelijk zijn aan 1.|  
|`fieldname`|Vereist voor het scoren van functies. Een scorefunctie kan alleen worden toegepast op velden die deel uitmaken van de veldverzameling van de index en die kunnen worden gefilterd. Bovendien voert elk functietype extra beperkingen in (versheid wordt gebruikt met datumtijdvelden, magnitude met gehele of dubbele velden en afstand met locatievelden). U slechts één veld per functiedefinitie opgeven. Als u bijvoorbeeld twee keer magnitude wilt gebruiken in hetzelfde profiel, moet u twee definities grootte, een voor elk veld.|  
|`interpolation`|Vereist voor het scoren van functies. Hiermee definieert u de helling waarvoor de scoreboost toeneemt van het begin van het bereik tot het einde van het bereik. Geldige waarden zijn lineair (standaard), Constant, Quadratic en Logathmic. Zie [Interpolaties instellen](#bkmk_interpolation) voor meer informatie.|  
|`magnitude`|De magnitudescoringsfunctie wordt gebruikt om ranglijsten te wijzigen op basis van het waardenbereik voor een numeriek veld. Enkele van de meest voorkomende gebruiksvoorbeelden hiervan zijn:<br /><br /> -   **Sterrenbeoordelingen:** Wijzig de score op basis van de waarde binnen het veld 'Sterrenbeoordeling'. Wanneer twee items relevant zijn, wordt het item met de hogere beoordeling eerst weergegeven.<br />-   **Marge:** Wanneer twee documenten relevant zijn, kan een detailhandelaar documenten willen stimuleren die eerst hogere marges hebben.<br />-   **Klik tellingen:** Voor toepassingen die klikacties naar producten of pagina's bijhouden, u grootte gebruiken om items te stimuleren die de neiging hebben om het meeste verkeer te genereren.<br />-   **Aantal downloadaantallen:** Voor toepassingen die downloads bijhouden, u met de magnitudefunctie items met de meeste downloads stimuleren.|  
|`magnitude`&#124;`boostingRangeStart`|Hiermee stelt u de beginwaarde in van het bereik waarover de grootte wordt gescoord. De waarde moet een geheel getal of zwevend-puntgetal zijn. Voor sterbeoordelingen van 1 tot en met 4 zou dit 1 zijn. Voor marges boven de 50% zou dit 50 zijn.|  
|`magnitude`&#124;`boostingRangeEnd`|Hiermee stelt u de eindwaarde in van het bereik waarover de grootte wordt gescoord. De waarde moet een geheel getal of zwevend-puntgetal zijn. Voor sterbeoordelingen van 1 tot en met 4 zou dit 4 zijn.|  
|`magnitude`&#124;`constantBoostBeyondRange`|Geldige waarden zijn waar of onwaar (standaard). Wanneer deze is ingesteld op true, blijft de volledige boost van toepassing op documenten die een waarde hebben voor het doelveld dat hoger is dan de bovenkant van het bereik. Als deze functie niet is fout, wordt de boost van deze functie niet toegepast op documenten met een waarde voor het doelveld dat buiten het bereik valt.|  
|`freshness`|De functie versheidsscore wordt gebruikt om rankingscores `DateTimeOffset` voor items te wijzigen op basis van waarden in velden. Een artikel met een recentere datum kan bijvoorbeeld hoger worden gerangschikt dan oudere items.<br /><br /> Het is ook mogelijk om items zoals agenda-afspraken te rangschikken met toekomstige datums, zodat items dichter bij het heden hoger kunnen worden gerangschikt dan items verder in de toekomst.<br /><br /> In de huidige servicerelease wordt één uiteinde van het bereik vastgesteld op de huidige tijd. Het andere uiteinde is een tijd `boostingDuration`in het verleden op basis van de . Gebruik een negatief om een aantal keren `boostingDuration`in de toekomst een boost te geven.<br /><br /> De snelheid waarmee de verhoging verandert van een maximum- en minimumbereik wordt bepaald door de Interpolatie die wordt toegepast op het scoreprofiel (zie de figuur hieronder). Als u de toegepaste boostfactor wilt omkeren, kiest u een boostfactor van minder dan 1.|  
|`freshness`&#124;`boostingDuration`|Hiermee stelt u een vervaldatum in waarna het stimuleren voor een bepaald document wordt gestopt. Zie [Stimuleren instellenDuur](#bkmk_boostdur) in de volgende sectie voor syntaxis en voorbeelden.|  
|`distance`|De functie afstandsscore wordt gebruikt om de score van documenten te beïnvloeden op basis van hoe dicht of ver ze zijn ten opzichte van een referentiegeografische locatie. De referentielocatie wordt als onderdeel van de query `scoringParameterquery` in een parameter (met de tekenreeksoptie) als argument lon, lat gegeven.|  
|`distance`&#124;`referencePointParameter`|Een parameter die moet worden doorgegeven in query's die moeten worden gebruikt als referentielocatie. `scoringParameter`is een queryparameter. Zie [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) voor beschrijvingen van queryparameters.|  
|`distance`&#124;`boostingDistance`|Een getal dat de afstand in kilometers aangeeft vanaf de referentielocatie waar het boostbereik eindigt.|  
|`tag`|De tagscoringsfunctie wordt gebruikt om de score van documenten te beïnvloeden op basis van tags in documenten en zoekopdrachten. Documenten met tags die met de zoekopdracht gemeen hebben, worden geboost. De tags voor de zoekopdracht worden in elk zoekverzoek `scoringParameterquery` als scoringsparameter geleverd (met de tekenreeksoptie).|  
|`tag`&#124;`tagsParameter`|Een parameter die moet worden doorgegeven in query's om tags voor een bepaalde aanvraag op te geven. `scoringParameter`is een queryparameter. Zie [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) voor beschrijvingen van queryparameters.|  
|`functionAggregation`|Optioneel. Geldt alleen wanneer functies zijn opgegeven. Geldige waarden zijn: som (standaard), gemiddeld, minimum, maximum en firstMatching. Een zoekscore is een enkele waarde die wordt berekend op basis van meerdere variabelen, waaronder meerdere functies. Dit kenmerk geeft aan hoe de boosts van alle functies worden gecombineerd tot één aggregaatboost die vervolgens wordt toegepast op de score van het basisdocument. De basisscore is gebaseerd op de [tf-idf-waarde](http://www.tfidf.com/) berekend op basis van het document en de zoekopdracht.|  
|`defaultScoringProfile`|Bij het uitvoeren van een zoekaanvraag, als er geen scoreprofiel is opgegeven, wordt standaardscore gebruikt (alleen[tf-idf).](http://www.tfidf.com/)<br /><br /> Hier kan een standaardprofielnaam worden ingesteld, waardoor Azure Cognitive Search dat profiel gebruikt wanneer er geen specifiek profiel wordt opgegeven in de zoekaanvraag.|  

##  <a name="set-interpolations"></a><a name="bkmk_interpolation"></a>Interpolaties instellen  
 Met interpolaties u de vorm instellen van de helling die wordt gebruikt om te scoren. Omdat de score hoog naar laag is, neemt de helling altijd af, maar de interpolatie bepaalt de curve van de neerwaartse helling. De volgende interpolaties kunnen worden gebruikt:  

|||  
|-|-|  
|`linear`|Voor artikelen die zich binnen het max- en min-bereik bevinden, wordt de boost die op het artikel wordt toegepast, in een voortdurend afnemend bedrag uitgevoerd. Lineair is de standaardinterpolatie voor een scoreprofiel.|  
|`constant`|Voor items die zich binnen het begin- en eindbereik bevinden, wordt een constante boost toegepast op de rangresultaten.|  
|`quadratic`|In vergelijking met een lineaire interpolatie die een voortdurend afnemende boost heeft, zal Quadratic in eerste instantie in een kleiner tempo afnemen en vervolgens als het het eindbereik nadert, neemt het af met een veel hoger interval. Deze interpolatieoptie is niet toegestaan in tagscoringsfuncties.|  
|`logarithmic`|In vergelijking met een lineaire interpolatie die een voortdurend afnemende boost heeft, zal Logarithmic in eerste instantie in een hoger tempo afnemen en vervolgens als het het eindbereik nadert, neemt het af met een veel kleiner interval. Deze interpolatieoptie is niet toegestaan in tagscoringsfuncties.|  

 ![Constant, lineair, kwadratisch, log10 lijnen op grafiek](media/scoring-profiles/azuresearch_scorefunctioninterpolationgrapht.png "AzureSearch_ScoreFunctionInterpolationGrapht")  

##  <a name="set-boostingduration"></a><a name="bkmk_boostdur"></a>Boosting-duur instellen  
 `boostingDuration`is een kenmerk `freshness` van de functie. U gebruikt het om een vervaldatum in te stellen waarna het stimuleren voor een bepaald document stopt. Als u bijvoorbeeld een productlijn of merk wilt promoten voor een promotieperiode van 10 dagen, geeft u de periode van 10 dagen op als 'P10D' voor die documenten.  

 `boostingDuration`moet worden opgemaakt als een XSD-waarde "dayTimeDuration" (een beperkte subset van een ISO 8601-duurwaarde). Het patroon hiervoor is: "P[nD][T[nH][nM][nS]]".  

 In de volgende tabel vindt u een aantal voorbeelden.  

|Duur|stimulerenDuur|  
|--------------|----------------------|  
|1 dag|"P1D"|  
|2 dagen en 12 uur|"P2DT12H"|  
|15 minuten|"PT15M"|  
|30 dagen, 5 uur, 10 minuten en 6,334 seconden|"P30DT5H10M6.334S"|  

 Zie [XML-schema: Gegevenstypen (W3.org website)](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)voor meer voorbeelden.  

## <a name="see-also"></a>Zie ook  
 [Azure Cognitive Search REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Index &#40;Azure Cognitive Search REST&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
