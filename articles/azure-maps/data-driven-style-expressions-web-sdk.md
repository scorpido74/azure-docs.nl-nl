---
title: Expressies in gegevensgestuurde stijl in de Azure Maps Web SDK | Microsoft Azure Maps
description: In dit artikel vindt u meer informatie over het gebruik van gegevensgestuurde stijlexpressies in de Microsoft Azure Maps Web SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: d6009a655adcc26ebef31588eff2332a05f3a001
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804721"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Gegevensgestuurde stijlexpressies (Web SDK)

Met expressies u bedrijfslogica toepassen op stylingopties die de eigenschappen observeren die in elke vorm in een gegevensbron zijn gedefinieerd. Expressies kunnen gegevens filteren in een gegevensbron of een laag. Expressies kunnen bestaan uit voorwaardelijke logica, zoals if-statements. En ze kunnen worden gebruikt om gegevens te manipuleren met behulp van: snaaroperatoren, logische operatoren en wiskundige operatoren.

Gegevensgestuurde stijlen verminderen de hoeveelheid code die nodig is om bedrijfslogica rond styling te implementeren. Bij gebruik met lagen worden expressies geëvalueerd op rendertijd op een afzonderlijke thread. Deze functionaliteit biedt betere prestaties in vergelijking met het evalueren van bedrijfslogica op de ui-thread.

Deze video biedt een overzicht van gegevensgestuurde styling in de Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Expressies worden weergegeven als JSON-arrays. Het eerste element van een expressie in de array is een tekenreeks die de naam van de expressieoperator opgeeft. Bijvoorbeeld '+' of 'case'. De volgende elementen (indien aanwezig) zijn de argumenten voor de expressie. Elk argument is ofwel een letterlijke waarde (een `null`tekenreeks, getal, booleaan of ), of een andere expressiearray. De volgende pseudocode definieert de basisstructuur van een expressie. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

De Azure Maps Web SDK ondersteunt vele soorten expressies. Expressies kunnen alleen of in combinatie met andere expressies worden gebruikt.

| Type expressies | Beschrijving |
|---------------------|-------------|
| [Geaggregeerde expressie](#aggregate-expression) | Een expressie die een berekening definieert die wordt verwerkt over `clusterProperties` een `DataSource`set gegevens en kan worden gebruikt met de optie een . |
| [Booleaanse expressies](#boolean-expressions) | Booleaanse expressies bieden een reeks booleaanse operatoren expressies voor het evalueren van booleaanse vergelijkingen. |
| [Kleurexpressies](#color-expressions) | Kleurexpressies maken het eenvoudiger om kleurwaarden te maken en te manipuleren. |
| [Voorwaardelijke expressies](#conditional-expressions) | Voorwaardelijke expressies bieden logische bewerkingen die zijn als if-statements. |
| [Gegevensexpressies](#data-expressions) | Biedt toegang tot de eigenschapsgegevens in een functie. |
| [Interpolaat- en stapexpressies](#interpolate-and-step-expressions) | Interpolaat- en stapexpressies kunnen worden gebruikt om waarden te berekenen langs een geïnterpoleerde curve- of stapfunctie. |
| [Specifieke expressies laag](#layer-specific-expressions) | Speciale expressies die alleen op één laag van toepassing zijn. |
| [Wiskundige expressies](#math-expressions) | Biedt wiskundige operatoren om gegevensgestuurde berekeningen uit te voeren binnen het expressiekader. |
| [Tekenreeksexpressies](#string-operator-expressions) | Tekenreeksexpressies voeren conversiebewerkingen uit op tekenreeksen, zoals het samenvoegen en converteren van de aanvraag. |
| [Expressies typen](#type-expressions) | Tekstexpressies bieden hulpprogramma's voor het testen en converteren van verschillende gegevenstypen, zoals tekenreeksen, getallen en booleaanse waarden. |
| [Variabele bindingsexpressies](#variable-binding-expressions) | Variabele bindingsexpressies slaan de resultaten van een berekening op in een variabele en elders in een expressie meerdere keren zonder de opgeslagen waarde opnieuw te hoeven berekenen. |
| [Zoomexpressie](#zoom-expression) | Hiermee wordt het huidige zoomniveau van de kaart opgehaald op rendertijd. |

Alle voorbeelden in dit document gebruiken de volgende functie om verschillende manieren aan te tonen waarop de verschillende typen expressies kunnen worden gebruikt. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>Gegevensexpressies

Gegevensexpressies bieden toegang tot de eigenschapsgegevens in een functie. 

| Expressie | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['at', number, array]` | object | Hiermee haalt u een item op uit een array. |
| `['geometry-type']` | tekenreeks | Hier wordt het geometrietype van de functie opgetjes: Punt, MultiPoint, LineString, MultiLineString, Polygoon, MultiPolygon. |
| `['get', string]` | waarde | Haalt de waarde van de eigenschap uit de eigenschappen van de huidige functie. Retourneert null als de gevraagde eigenschap ontbreekt. |
| `['get', string, object]` | waarde | Haalt de eigenschapswaarde uit de eigenschappen van het opgegeven object. Retourneert null als de gevraagde eigenschap ontbreekt. |
| `['has', string]` | booleaans | Hiermee bepaalt u of de eigenschappen van een functie de opgegeven eigenschap hebben. |
| `['has', string, object]` | booleaans | Hiermee bepaalt u of de eigenschappen van het object de opgegeven eigenschap hebben. |
| `['id']` | waarde | Krijgt de functie-ID als deze er een heeft. |
| `['length', string | array]` | getal | Hier wordt de lengte van een tekenreeks of een array. |
| `['in', boolean | string | number, array]` | booleaans | Hiermee bepaalt u of er een item bestaat in een array |
| `['in', substring, string]` | booleaans | Hiermee bepaalt u of er een subtekenreeks in een tekenreeks bestaat |

**Voorbeelden**

Eigenschappen van een functie kunnen rechtstreeks in een `get` expressie worden geopend met behulp van een expressie. In dit voorbeeld wordt de waarde 'zoneKleur' van de functie gebruikt om de eigenschap kleur van een bellenlaag op te geven. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Het bovenstaande voorbeeld werkt prima, als alle `zoneColor` puntfuncties de eigenschap hebben. Als ze dat niet doen, zal de kleur waarschijnlijk terugvallen naar "zwart". Als u de terugvalkleur `case` wilt wijzigen, `has` gebruikt u een expressie in combinatie met de expressie om te controleren of de eigenschap bestaat. Als de eigenschap niet bestaat, geeft u een terugvalkleur terug.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Bellen- en symboollagen geven standaard de coördinaten van alle vormen in een gegevensbron weer. Dit gedrag kan de hoekpunten van een veelhoek of een regel markeren. De `filter` optie van de laag kan worden gebruikt om het geometrietype `['geometry-type']` van de functies die worden weergegeven te beperken door een expressie binnen een booleaanse expressie te gebruiken. In het volgende voorbeeld wordt `Point` een bellenlaag beperkt, zodat alleen functies worden weergegeven.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

In het volgende `Point` `MultiPoint` voorbeeld kunnen beide en functies worden weergegeven. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Op dezelfde manier wordt de omtrek van Polygonen weergegeven in lijnlagen. Als u dit gedrag in een lijnlaag `LineString` wilt `MultiLineString` uitschakelen, voegt u een filter toe dat alleen dit toestaat en functies toestaat.  

## <a name="math-expressions"></a>Wiskundige expressies

Wiskundige expressies bieden wiskundige operatoren om gegevensgestuurde berekeningen uit te voeren binnen het expressiekader.

| Expressie | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['+', number, number, …]` | getal | Berekent de som van de opgegeven getallen. |
| `['-', number]` | getal | Trekt 0 af op het opgegeven getal. |
| `['-', number, number]` | getal | Hiermee worden de eerste getallen afgetrokken op het tweede getal. |
| `['*', number, number, …]` | getal | Hiermee worden de opgegeven getallen samenvermenigvuldigd. |
| `['/', number, number]` | getal | Verdeelt het eerste getal door het tweede getal. |
| `['%', number, number]` | getal | Berekent de rest bij het delen van het eerste getal door het tweede getal. |
| `['^', number, number]` | getal | Berekent de waarde van de eerste waarde die is verhoogd tot de kracht van het tweede getal. |
| `['abs', number]` | getal | Berekent de absolute waarde van het opgegeven getal. |
| `['acos', number]` | getal | Berekent de arccosine van het opgegeven getal. |
| `['asin', number]` | getal | Berekent de arcsine van het opgegeven getal. |
| `['atan', number]` | getal | Berekent de arctangens van het opgegeven getal. |
| `['ceil', number]` | getal | Rounds the number up to the next whole integer. |
| `['cos', number]` | getal | Berekent de cos van het opgegeven getal. |
| `['e']` | getal | Geeft als `e`resultaat de wiskundige constante . |
| `['floor', number]` | getal | Rounds the number down to the previous whole integer. |
| `['ln', number]` | getal | Berekent de natuurlijke logaritme van het opgegeven getal. |
| `['ln2']` | getal | Geeft als `ln(2)`resultaat de wiskundige constante . |
| `['log10', number]` | getal | Berekent de base-tien logaritme van het opgegeven getal. |
| `['log2', number]` | getal | Berekent de base-twee logaritme van het opgegeven getal. |
| `['max', number, number, …]` | getal | Berekent het maximumaantal in de opgegeven set getallen. |
| `['min', number, number, …]` | getal | Berekent het minimumaantal in de opgegeven set getallen. |
| `['pi']` | getal | Geeft als `PI`resultaat de wiskundige constante . |
| `['round', number]` | getal | Hiermee wordt het getal afgerond op het dichtstbijzijnde gehele getal. De halve waarden worden van nul afgemaakt. Evalueert `['round', -1.5]` bijvoorbeeld op -2. |
| `['sin', number]` | getal | Berekent de sinin van het opgegeven getal. |
| `['sqrt', number]` | getal | Berekent de vierkantswortel van het opgegeven getal. |
| `['tan', number]` | getal | Berekent de raaklijn van het opgegeven getal. |

## <a name="aggregate-expression"></a>Geaggregeerde expressie

Een geaggregeerde expressie definieert een berekening die wordt verwerkt over `clusterProperties` een `DataSource`set gegevens en kan worden gebruikt met de optie een . De uitvoer van deze expressies moet een getal of een booleaan zijn. 

Een geaggregeerde expressie bevat drie waarden: een operatorwaarde en de initiële waarde en een expressie om een eigenschap op te halen uit elke functie in een gegevens waarop de samengevoegde bewerking moet worden toegepast. Deze expressie heeft de volgende indeling:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: een expressiefunctie die vervolgens wordt toegepast op `mapExpression` alle waarden die zijn berekend door het voor elk punt in het cluster. Ondersteunde exploitanten: 
    - Voor `+`getallen: `*` `max`, ,`min`
    - Voor Booleans: `all`,`any`
- initiële waarde: een initiële waarde waarin de eerste berekende waarde wordt samengevoegd ten opzichte van.
- mapExpression: een expressie die wordt toegepast op elk punt in de gegevensset.

**Voorbeelden**

Als alle functies in een `revenue` gegevensset een eigenschap hebben, wat een getal is. Vervolgens kunnen de totale opbrengst van alle punten in een cluster, die zijn gemaakt op basis van de gegevensset, worden berekend. Deze berekening gebeurt met de volgende geaggregeerde expressie:`['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Booleaanse expressies

Booleaanse expressies bieden een reeks booleaanse operatoren expressies voor het evalueren van booleaanse vergelijkingen.

Bij het vergelijken van waarden wordt de vergelijking strikt getypt. Waarden van verschillende soorten worden altijd als ongelijk beschouwd. Gevallen waarin bekend is dat de typen verschillend zijn op parse-tijd worden als ongeldig beschouwd en een parse-fout veroorzaken. 

| Expressie | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['! ', boolean]` | booleaans | Logische ontkenning. `true` Retourneert als `false`de `false` invoer is `true`, en als de invoer is . |
| `['!= ', value, value]` | booleaans | Retourneert `true` als de invoerwaarden niet gelijk zijn, `false` anders. |
| `['<', value, value]` | booleaans | Retourneert `true` als de eerste invoer `false` strikt lager is dan de tweede, anders. De argumenten moeten beide tekenreeksen of beide getallen zijn. |
| `['<=', value, value]` | booleaans | Retourneert `true` als de eerste invoer lager `false` is dan of gelijk is aan de tweede, anders. De argumenten moeten beide tekenreeksen of beide getallen zijn. |
| `['==', value, value]` | booleaans | Retourneert `true` als de `false` invoerwaarden gelijk zijn, anders. De argumenten moeten beide tekenreeksen of beide getallen zijn. |
| `['>', value, value]` | booleaans | Retourneert `true` als de eerste invoer `false` strikt groter is dan de tweede, anders. De argumenten moeten beide tekenreeksen of beide getallen zijn. |
| `['>=' value, value]` | booleaans | Retourneert `true` als de eerste invoer groter `false` is dan of gelijk is aan de tweede, anders. De argumenten moeten beide tekenreeksen of beide getallen zijn. |
| `['all', boolean, boolean, …]` | booleaans | Retourneert `true` als `true`alle `false` ingangen, anders. |
| `['any', boolean, boolean, …]` | booleaans | Retourneert `true` als een `true` `false` van de ingangen, anders. |

## <a name="conditional-expressions"></a>Voorwaardelijke expressies

Voorwaardelijke expressies bieden logische bewerkingen die zijn als if-statements.

Met de volgende expressies worden voorwaardelijke logische bewerkingen uitgevoerd op de invoergegevens. De `case` expressie biedt bijvoorbeeld logica 'als/dan/anders', terwijl de `match` expressie als een 'switch-statement' is. 

### <a name="case-expression"></a>Caseexpressie

Een `case` expressie is een type voorwaardelijke expressie dat de logica 'als/dan/anders' biedt. Dit type expressie doorloopt een lijst met booleaanse omstandigheden. Het retourneert de uitvoerwaarde van de eerste booleaanse voorwaarde om te evalueren naar true.

De volgende pseudocode definieert `case` de structuur van de expressie. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Voorbeeld**

In het volgende voorbeeld worden verschillende booleaanse omstandigheden `true`doorlopen totdat deze wordt gevonden en die de bijbehorende waarde retourneert. Als er geen booleaanse toestand evalueert tot `true`, wordt een terugvalwaarde geretourneerd. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Expressie overeenkomen

Een `match` expressie is een type voorwaardelijke expressie dat een switch-statement achtige logica biedt. De invoer kan elke `['get', 'entityType']` expressie zijn, zoals die een tekenreeks of een getal retourneert. Elk label moet één letterlijke waarde of een matrix van letterlijke waarden zijn, waarvan de waarden alle tekenreeksen of alle getallen moeten zijn. De invoer komt overeen als een van de waarden in de array overeenkomt. Elk label moet uniek zijn. Als het invoertype niet overeenkomt met het type labels, is het resultaat de terugvalwaarde.

De volgende pseudocode definieert `match` de structuur van de expressie. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Voorbeelden**

In het volgende `entityType` voorbeeld wordt gekeken naar de eigenschap van een functie Punt in een bellenlaag waarin wordt gezocht naar een overeenkomst. Als er een overeenkomst wordt gevonden, wordt die opgegeven waarde geretourneerd of wordt de terugvalwaarde geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

In het volgende voorbeeld wordt een array gebruikt om een set labels weer te geven die allemaal dezelfde waarde moeten retourneren. Deze aanpak is veel efficiënter dan het individueel vermelden van elk label. In dit geval, `entityType` als de eigenschap is "restaurant" of "grocery_store", de kleur "rood" zal worden geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

In het volgende voorbeeld wordt een overeenkomstexpressie gebruikt om een typefilter 'in array' of 'array bevat' uit te voeren. In dit geval filtert de expressie gegevens met een ID-waarde die in een lijst met toegestane id's staat. Bij het gebruik van expressies met filters moet het resultaat een Booleaanse waarde zijn.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Coalesce-expressie

Een `coalesce` expressie doorloopt een set expressies totdat de eerste niet-null-waarde is verkregen en retourneert die waarde. 

De volgende pseudocode definieert `coalesce` de structuur van de expressie. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Voorbeeld**

In het volgende `coalesce` voorbeeld wordt `textField` een expressie gebruikt om de optie van een symboollaag in te stellen. Als `title` de eigenschap ontbreekt in `null`de functie of ingesteld op `subtitle` , zal de `null`expressie dan proberen op zoek naar de eigenschap, als het ontbreekt of , het zal dan terug vallen naar een lege string. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

In het volgende `coalesce` voorbeeld wordt een expressie gebruikt om het eerste beschikbare afbeeldingspictogram op te halen dat beschikbaar is in de kaartsprite uit een lijst met opgegeven afbeeldingsnamen.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Expressies typen

Tekstexpressies bieden hulpprogramma's voor het testen en converteren van verschillende gegevenstypen, zoals tekenreeksen, getallen en booleaanse waarden.

| Expressie | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | arrayobject \| | Geeft als resultaat een letterlijke array- of objectwaarde. Gebruik deze expressie om te voorkomen dat een array of object als expressie wordt geëvalueerd. Dit is nodig wanneer een array of object moet worden geretourneerd door een expressie. |
| `['image', string]` | tekenreeks | Hiermee wordt gecontroleerd of een opgegeven afbeeldings-id is geladen in de sprite van de kaartafbeelding. Als dit het geval is, wordt de ID geretourneerd, anders wordt null geretourneerd. |
| `['to-boolean', value]` | booleaans | Hiermee converteert u de invoerwaarde naar een booleaan. Het resultaat `false` is wanneer de invoer `0` `false`een `null`lege `NaN`tekenreeks is, , , of ; anders `true`zijn . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Hiermee converteert u de invoerwaarde naar een kleur. Als er meerdere waarden worden opgegeven, wordt elke waarde in volgorde geëvalueerd totdat de eerste succesvolle conversie is verkregen. Als geen van de ingangen kan worden geconverteerd, is de expressie een fout. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | getal | Hiermee converteert u de invoerwaarde naar een getal, indien mogelijk. Als de `null` invoer `false`is of , is het resultaat 0. Als de `true`invoer is, is het resultaat 1. Als de invoer een tekenreeks is, wordt deze geconverteerd naar een getal met de tekenreeks [ToNumber-functie](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) van de ECMAScript-taalspecificatie. Als er meerdere waarden worden opgegeven, wordt elke waarde in volgorde geëvalueerd totdat de eerste succesvolle conversie is verkregen. Als geen van de ingangen kan worden geconverteerd, is de expressie een fout. |
| `['to-string', value]` | tekenreeks | Hiermee converteert u de invoerwaarde naar een tekenreeks. Als de `null`invoer is, `""`is het resultaat . Als de invoer een booleaan `"true"` `"false"`is, is het resultaat of . Als de invoer een getal is, wordt deze geconverteerd naar een tekenreeks met de functie [ToString-nummer](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) van de ECMAScript-taalspecificatie. Als de invoer een kleur is, wordt deze `"rgba(r,g,b,a)"`geconverteerd naar CSS RGBA-kleurtekenreeks . Anders wordt de invoer geconverteerd naar een tekenreeks met de [json.stringify-functie](https://tc39.github.io/ecma262/#sec-json.stringify) van de ECMAScript-taalspecificatie. |
| `['typeof', value]` | tekenreeks | Geeft als resultaat een tekenreeks die het type van de opgegeven waarde beschrijft. |

> [!TIP]
> Als er een `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` foutbericht is dat vergelijkbaar is met die in de browserconsole, betekent dit dat er ergens in uw code een expressie is die een array heeft die geen tekenreeks heeft voor de eerste waarde. Als u wilt dat de expressie een array `literal` retourneert, wikkelt u de array met de expressie. In het volgende `offset` voorbeeld wordt de pictogramoptie van een symboollaag ingesteld, die `match` een array met twee getallen moet `entityType` zijn, door een expressie te gebruiken om te kiezen tussen twee verschuivingswaarden op basis van de waarde van de eigenschap van de puntfunctie.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Kleurexpressies

Kleurexpressies maken het eenvoudiger om kleurwaarden te maken en te manipuleren.

| Expressie | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Hiermee maakt u een kleurwaarde van *rode,* `0` *groene*en *blauwe* componenten die moeten variëren tussen en `255`, en een alfacomponent van `1`. Als een component buiten bereik is, is de expressie een fout. |
| `['rgba', number, number, number, number]` | color | Hiermee maakt u een kleurwaarde van *rode,* `255` *groene,* *blauwe* componenten die moeten variëren tussen `0` en , en een alfacomponent binnen een bereik van `0` en `1`. Als een component buiten bereik is, is de expressie een fout. |
| `['to-rgba']` | \[nummer, nummer, nummer, getal\] | Geeft als resultaat een array met vier elementen met *de rode,* *groene,* *blauwe*en *alfacomponenten* van de invoerkleur in die volgorde. |

**Voorbeeld**

In het volgende voorbeeld wordt een RGB-kleurwaarde gemaakt met een *rode* waarde van `255`en `temperature` *groene* en *blauwe* waarden die worden berekend door te vermenigvuldigen `2.5` met de waarde van de eigenschap. Als de temperatuur verandert, zal de kleur veranderen in verschillende tinten *rood*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Tekenreeksexpressies

Tekenreeksexpressies voeren conversiebewerkingen uit op tekenreeksen, zoals het samenvoegen en converteren van de aanvraag. 

| Expressie | Retourtype | Beschrijving |
|------------|-------------|-------------|
| `['concat', string, string, …]` | tekenreeks | Concatenates meerdere snaren samen. Elke waarde moet een tekenreeks zijn. Gebruik `to-string` de tekstexpressie om indien nodig andere waardetypen om te zetten in tekenreeks. |
| `['downcase', string]` | tekenreeks | Hiermee converteert u de opgegeven tekenreeks naar kleine letters. |
| `['upcase', string]` | tekenreeks | Hiermee converteert u de opgegeven tekenreeks naar hoofdletters. |

**Voorbeeld**

In het volgende `temperature` voorbeeld wordt de eigenschap van de puntfunctie omgezet in een tekenreeks en wordt vervolgens "°F" tot het einde ervan samengevoegd.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

De bovenstaande uitdrukking geeft een pin op de kaart met de tekst "64 ° F" bedekt op de top van het zoals weergegeven in de afbeelding hieronder.

<center>

![Voorbeeld van](media/how-to-expressions/string-operator-expression.png) tekenreeksoperatorexpressie</center>

## <a name="interpolate-and-step-expressions"></a>Interpolaat- en stapexpressies

Interpolaat- en stapexpressies kunnen worden gebruikt om waarden te berekenen langs een geïnterpoleerde curve- of stapfunctie. Deze expressies krijgen bijvoorbeeld een expressie die een `['get',  'temperature']`numerieke waarde als invoer retourneert. De invoerwaarde wordt geëvalueerd aan de hand van paren van invoer- en uitvoerwaarden om de waarde te bepalen die het beste past bij de geïnterpoleerde curve- of stapfunctie. De uitvoerwaarden worden "stops" genoemd. De invoerwaarden voor elke stop moeten een getal zijn en in oplopende volgorde zijn. De uitvoerwaarden moeten een getal en een matrix van getallen of een kleur zijn.

### <a name="interpolate-expression"></a>Interpolaatexpressie

Een `interpolate` expressie kan worden gebruikt om een continue, vloeiende set waarden te berekenen door te interpoleren tussen stopwaarden. Een `interpolate` expressie die kleurwaarden retourneert, produceert een kleurverloop waaruit resultaatwaarden zijn geselecteerd.

Er zijn drie soorten interpolatiemethoden die in `interpolate` een expressie kunnen worden gebruikt:
 
* `['linear']`- Interpoleert lineair tussen het paar stops.
* `['exponential', base]`- Interpoleert exponentieel tussen de haltes. De `base` waarde bepaalt de snelheid waarmee de uitvoer toeneemt. Hogere waarden zorgen ervoor dat de output meer naar de hoge kant van het bereik stijgt. Een `base` waarde dicht bij 1 produceert een uitvoer die lineairer toeneemt.
* `['cubic-bezier', x1, y1, x2, y2]`- Interpolaten met behulp van een [kubieke Bezier-curve](https://developer.mozilla.org/docs/Web/CSS/timing-function) die wordt gedefinieerd door de gegeven controlepunten.

Hier is een voorbeeld van hoe deze verschillende soorten interpolaties eruit zien. 

| Lineair  | Exponentieel | Kubieke Bezier |
|---------|-------------|--------------|
| ![Lineaire interpolatiegrafiek](media/how-to-expressions/linear-interpolation.png) | ![Exponentiële interpolatiegrafiek](media/how-to-expressions/exponential-interpolation.png) | ![Kubieke Bezier-interpolatiegrafiek](media/how-to-expressions/bezier-curve-interpolation.png) |

De volgende pseudocode definieert `interpolate` de structuur van de expressie. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Voorbeeld**

In het volgende `linear interpolate` voorbeeld wordt `color` een expressie gebruikt om `temperature` de eigenschap van een bellenlaag in te stellen op basis van de eigenschap van de puntfunctie. Als `temperature` de waarde lager is dan 60, wordt 'blauw' geretourneerd. Als het tussen de 60 en minder dan 70, geel zal worden geretourneerd. Als het tussen de 70 en minder dan 80, zal "oranje" worden geretourneerd. Als het 80 of hoger is, wordt 'rood' geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

De volgende afbeelding laat zien hoe de kleuren worden gekozen voor de bovenstaande expressie.
 
<center>

![Voorbeeld van interpolatieexpressie](media/how-to-expressions/interpolate-expression-example.png)</center>

### <a name="step-expression"></a>Stapexpressie

Een `step` expressie kan worden gebruikt om discrete, getrapte resultaatwaarden te berekenen door een [stuk-zijdelig-constante functie](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) te evalueren die door einden wordt bepaald. 

De volgende pseudocode definieert `step` de structuur van de expressie. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Stapexpressies geven de uitvoerwaarde van de stop net voor de invoerwaarde of de eerste invoerwaarde als de invoer minder is dan de eerste stop. 

**Voorbeeld**

In het volgende `step` voorbeeld wordt `color` een expressie gebruikt om `temperature` de eigenschap van een bellenlaag in te stellen op basis van de eigenschap van de puntfunctie. Als `temperature` de waarde lager is dan 60, wordt 'blauw' geretourneerd. Als het tussen de 60 en minder dan 70, zal "geel" worden geretourneerd. Als het tussen de 70 en minder dan 80, zal "oranje" worden geretourneerd. Als het 80 of hoger is, wordt 'rood' geretourneerd.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

De volgende afbeelding laat zien hoe de kleuren worden gekozen voor de bovenstaande expressie.
 
<center>

![Voorbeeld van stapexpressie](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Laagspecifieke expressies

Speciale expressies die alleen van toepassing zijn op specifieke lagen.

### <a name="heat-map-density-expression"></a>De dichtheidsexpressie van de heatmap

Een heatmapdichtheidsexpressie haalt de dichtheidswaarde van de heatmap op voor `['heatmap-density']`elke pixel in een heatmaplaag en wordt gedefinieerd als . Deze waarde is `0` een `1`getal tussen en . Het wordt gebruikt in `interpolation` combinatie `step` met een of expressie om het kleurverloop te definiëren dat wordt gebruikt om de warmtekaart te kleuren. Deze expressie kan alleen worden gebruikt in de [kleuroptie](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) van de heatmaplaag.

> [!TIP]
> De kleur bij index 0, in een interpolatie-expressie of de standaardkleur van een stapkleur, definieert de kleur van het gebied waar geen gegevens zijn. De kleur bij index 0 kan worden gebruikt om een achtergrondkleur te definiëren. Velen geven de voorkeur aan deze waarde in te stellen op transparant of een semi-transparante zwart.

**Voorbeeld**

In dit voorbeeld wordt een lijninterpolatie-expressie gebruikt om een vloeiend kleurverloop te maken voor het renderen van de warmtekaart. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Naast het gebruik van een vloeiend verloop om een warmtekaart te kleuren, kunnen `step` kleuren binnen een reeks bereiken worden opgegeven met behulp van een expressie. Met `step` behulp van een expressie voor het inkleuren van de warmtekaart visueel breekt de dichtheid in bereiken die lijkt op een contour of radar stijl kaart.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Zie de documentatie [van de warmtekaartlaag toevoegen](map-add-heat-map-layer.md) voor meer informatie.

### <a name="line-progress-expression"></a>Regelvoortgangsexpressie

Een lijnvoortgangsexpressie haalt de voortgang op langs een verlooplijn `['line-progress']`in een lijnlaag en wordt gedefinieerd als . Deze waarde is een getal tussen 0 en 1. Het wordt gebruikt in `interpolation` combinatie `step` met een of expressie. Deze expressie kan alleen worden gebruikt met de [optie lijnverloop]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) van de lijnlaag. 

> [!NOTE]
> De `strokeGradient` optie van de `lineMetrics` lijnlaag vereist de optie `true`van de gegevensbron die moet worden ingesteld op .

**Voorbeeld**

In dit `['line-progress']` voorbeeld wordt de expressie gebruikt om een kleurverloop toe te passen op de lijn van een lijn.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Zie live voorbeeld](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Tekstveldopmaakexpressie

De expressie tekstveldopmaak kan `textField` worden gebruikt `textOptions` met de optie van de eigenschap symboollagen om gemengde tekstopmaak te bieden. Met deze expressie kan een set invoertekenreeksen en opmaakopties worden opgegeven. Voor elke invoertekenreeks in deze expressie kunnen de volgende opties worden opgegeven.

 * `'font-scale'`- Hiermee geeft u de schaalfactor voor de tekengrootte op. Indien opgegeven, overschrijft `size` deze waarde `textOptions` de eigenschap van de voor de afzonderlijke tekenreeks.
 * `'text-font'`- Hiermee geeft u een of meer lettertypefamilies op die voor deze tekenreeks moeten worden gebruikt. Indien opgegeven, overschrijft `font` deze waarde `textOptions` de eigenschap van de voor de afzonderlijke tekenreeks.
 * `'text-color'`- Hiermee geeft u een kleur op die u op een tekst moet toepassen bij het renderen. 

Met de volgende pseudocode wordt de structuur van de expressie van het tekstveldopmaak gedefinieerd. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Voorbeeld**

In het volgende voorbeeld wordt het tekstveld opmaakt door `title` een vet lettertype toe te voegen en de tekengrootte van de eigenschap van de functie op te schalen. In dit voorbeeld `subtitle` wordt ook de eigenschap van de functie op een nieuwe regel toegevoegd, met een verkleinde tekengrootte en rood gekleurd.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Deze laag geeft de puntfunctie weer zoals in de afbeelding hieronder:
 
<center>

![Afbeelding van puntfunctie met](media/how-to-expressions/text-field-format-expression.png) opgemaakt tekstveld</center>

### <a name="number-format-expression"></a>Getalopmaakexpressie

De `number-format` expressie kan alleen `textField` worden gebruikt met de optie van een symboollaag. Met deze expressie wordt het opgegeven getal omgezet in een opgemaakte tekenreeks. Met deze expressie wordt de functie [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) van JavaScript omwikkeld en ondersteunt u de volgende set opties.

 * `locale`- Geef deze optie op voor het converteren van getallen naar tekenreeksen op een manier die overeenkomt met de opgegeven taal. Geef een [BCP 47-taaltag door](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) aan deze optie.
 * `currency`- Het getal omzetten in een tekenreeks die een valuta vertegenwoordigt. Mogelijke waarden zijn de [ISO 4217 valutacodes](https://en.wikipedia.org/wiki/ISO_4217), zoals "USD" voor de Amerikaanse dollar, "EUR" voor de euro, of "CNY" voor de Chinese RMB.
 * `'min-fraction-digits'`- Hiermee geeft u het minimumaantal decimalen op dat moet worden opgenomen in de tekenreeksversie van het getal.
 * `'max-fraction-digits'`- Hiermee geeft u het maximumaantal decimalen op dat moet worden opgenomen in de tekenreeksversie van het getal.

Met de volgende pseudocode wordt de structuur van de expressie van het tekstveldopmaak gedefinieerd. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Voorbeeld**

In het volgende `number-format` voorbeeld wordt `revenue` een expressie gebruikt om te `textField` wijzigen hoe de eigenschap van de puntfunctie wordt weergegeven in de optie van een symboollaag, zodat deze een waarde in amerikaanse dollar wordt weergegeven.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Deze laag geeft de puntfunctie weer zoals in de afbeelding hieronder:

<center>

![Voorbeeld van](media/how-to-expressions/number-format-expression.png) getalopmaakexpressie</center>

### <a name="image-expression"></a>Afbeeldingsexpressie

Een afbeeldingsexpressie kan `image` worden `textField` gebruikt met de opties `fillPattern` en opties van een symboollaag en de optie van de veelhoeklaag. Deze expressie controleert of de gevraagde afbeelding in de stijl bestaat `null`en retourneert de opgeloste afbeeldingsnaam of , afhankelijk van of de afbeelding zich momenteel in de stijl bevindt. Dit validatieproces is synchroon en vereist dat de afbeelding aan de stijl is toegevoegd voordat deze in het afbeeldingsargument wordt aangevraagd.

**Voorbeeld**

In het volgende `image` voorbeeld wordt een expressie gebruikt om een pictogram inline met tekst in een symboollaag toe te voegen. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Met deze laag wordt het tekstveld in de symboollaag weergegeven, zoals in de afbeelding hieronder wordt weergegeven:

<center>

![Voorbeeld van](media/how-to-expressions/image-expression.png) afbeeldingsexpressie</center>

## <a name="zoom-expression"></a>Zoomexpressie

Een `zoom` expressie wordt gebruikt om het huidige zoomniveau van de `['zoom']`kaart op rendertijd op te halen en wordt gedefinieerd als . Met deze expressie wordt een getal geretourneerd tussen het minimum- en maximale zoomniveaubereik van de kaart. De interactieve kaartbesturingselementen voor azure maps voor web- en Android-ondersteuning voor 25 zoomniveaus, genummerd van 0 tot en met 24. Met `zoom` behulp van de expressie kunnen stijlen dynamisch worden gewijzigd als het zoomniveau van de kaart wordt gewijzigd. De `zoom` expressie mag alleen `interpolate` `step` worden gebruikt met en uitdrukkingen.

**Voorbeeld**

Standaard hebben de stralen van gegevenspunten die in de heatmaplaag worden weergegeven, een vaste pixelstraal voor alle zoomniveaus. Als de kaart wordt ingezoomd, worden de gegevens samengevoegd en ziet de heatmaplaag er anders uit. Een `zoom` expressie kan worden gebruikt om de straal voor elk zoomniveau zodanig te schalen dat elk gegevenspunt hetzelfde fysieke gebied van de kaart bestrijkt. Het zal de warmtekaart laag kijken meer statisch en consistent. Elk zoomniveau van de kaart heeft twee keer zoveel pixels verticaal en horizontaal als het vorige zoomniveau. Als u de straal schaalt, zodat deze met elk zoomniveau wordt verdubbeld, wordt een heatmap gemaakt die er consistent uitziet op alle zoomniveaus. Het kan worden bereikt `zoom` met `base 2 exponential interpolation` behulp van de expressie met een expressie, met de pixelstraal ingesteld `2 * Math.pow(2, minZoom - maxZoom)` voor het minimale zoomniveau en een geschaalde straal voor het maximale zoomniveau berekend zoals hieronder weergegeven.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Zie live voorbeeld](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Variabele bindingsexpressies

Variabele bindingsexpressies slaan de resultaten van een berekening op in een variabele. Dus, dat de berekening resultaten elders kunnen worden verwezen in een expressie meerdere keren. Het is een nuttige optimalisatie voor expressies die veel berekeningen met zich meebrengen.

| Expressie | Retourtype | Beschrijving |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'laten we',<br/>&nbsp;&nbsp;&nbsp;&nbsp;naam1: tekenreeks,<br/>&nbsp;&nbsp;&nbsp;&nbsp;waarde1: elke,<br/>&nbsp;&nbsp;&nbsp;&nbsp;naam2: tekenreeks,<br/>&nbsp;&nbsp;&nbsp;&nbsp;waarde2: elke,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;onderliggende expressie<br/>\] | | Hiermee slaat u een of meer `var` waarden op als variabelen voor gebruik door de expressie in de onderliggende expressie die het resultaat retourneert. |
| `['var', name: string]` | elke | Verwijst naar een variabele die `let` is gemaakt met behulp van de expressie. |

**Voorbeeld**

In dit voorbeeld wordt een expressie gebruikt die de `case` opbrengst berekent ten opzichte van de temperatuurverhouding en vervolgens een expressie gebruikt om verschillende booleaanse bewerkingen op deze waarde te evalueren. De `let` expressie wordt gebruikt om de opbrengst ten opzichte van de temperatuurverhouding op te slaan, zodat deze slechts één keer hoeft te worden berekend. De `var` expressie verwijst naar deze variabele zo vaak als nodig is zonder deze opnieuw te hoeven berekenen.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer codevoorbeelden die expressies implementeren:

> [!div class="nextstepaction"] 
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Een bubbellaag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een lijnlaag toevoegen](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Een polygoonlaag toevoegen](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Een heatmap-laag toevoegen](map-add-heat-map-layer.md)

Meer informatie over de laagopties die expressies ondersteunen:

> [!div class="nextstepaction"] 
> [BubbleLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [Polygoonopties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbollayerOpties](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
