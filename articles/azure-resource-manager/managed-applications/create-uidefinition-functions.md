---
title: UI-definitie functies maken
description: Hierin worden de functies beschreven die moeten worden gebruikt bij het maken van UI-definities voor Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: a93f4ff2ddc0737692de9e5619cf7a7521936224
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82980810"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition-functies
Deze sectie bevat de hand tekeningen voor alle ondersteunde functies van een CreateUiDefinition.

Als u een functie wilt gebruiken, plaatst u de aanroep tussen vier Kante haken. Bijvoorbeeld:

```json
"[function()]"
```

Naar teken reeksen en andere functies kan worden verwezen als para meters voor een functie, maar teken reeksen moeten tussen enkele aanhalings tekens worden geplaatst. Bijvoorbeeld:

```json
"[fn1(fn2(), 'foobar')]"
```

Waar van toepassing kunt u verwijzen naar eigenschappen van de uitvoer van een functie met behulp van de punt operator. Bijvoorbeeld:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Verwijzende functies
Deze functies kunnen worden gebruikt om te verwijzen naar uitvoer van de eigenschappen of context van een CreateUiDefinition.

### <a name="basics"></a>bewerkingen
Retourneert de uitvoer waarden van een element dat is gedefinieerd in de stap basis beginselen.

In het volgende voor beeld wordt de uitvoer van het element met `foo` de naam in de stap basis beginselen geretourneerd:

```json
"[basics('foo')]"
```

### <a name="steps"></a>stappen
Retourneert de uitvoer waarden van een element dat in de opgegeven stap is gedefinieerd. Als u de uitvoer waarden van elementen in de stap basis beginselen wilt ophalen, gebruikt u `basics()` in plaats daarvan.

In het volgende voor beeld wordt de uitvoer geretourneerd van het element met `bar` de naam in de stap met de naam `foo` :

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Retourneert de locatie die is geselecteerd in de stap basis beginselen of de huidige context.

Het volgende voor beeld kan resulteren in `"westus"` :

```json
"[location()]"
```

## <a name="string-functions"></a>Tekenreeksfuncties
Deze functies kunnen alleen worden gebruikt met JSON-teken reeksen.

### <a name="concat"></a>concat
Voegt een of meer teken reeksen samen.

Als bijvoorbeeld de uitvoer waarde van `element1` if `"bar"` is, retourneert dit voor beeld de teken reeks `"foobar!"` :

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>subtekenreeks
Retourneert de subtekenreeks van de opgegeven teken reeks. De subtekenreeks begint bij de opgegeven index en heeft de opgegeven lengte.

In het volgende voor beeld wordt geretourneerd `"ftw"` :

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>vervangen
Retourneert een teken reeks waarin alle instanties van de opgegeven teken reeks in de huidige teken reeks worden vervangen door een andere teken reeks.

In het volgende voor beeld wordt geretourneerd `"Everything is awesome!"` :

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Hiermee wordt een globaal unieke teken reeks (GUID) gegenereerd.

Het volgende voor beeld kan resulteren in `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` :

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Retourneert een teken reeks die is geconverteerd naar kleine letters.

In het volgende voor beeld wordt geretourneerd `"foobar"` :

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Retourneert een teken reeks die is geconverteerd naar hoofd letters.

In het volgende voor beeld wordt geretourneerd `"FOOBAR"` :

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Verzamelings functies
Deze functies kunnen worden gebruikt in combi natie met verzamelingen, zoals JSON-teken reeksen, matrices en objecten.

### <a name="contains"></a>bevat
Retourneert `true` als een teken reeks de opgegeven subtekenreeks bevat, een matrix bevat de opgegeven waarde of een object de opgegeven sleutel bevat.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `true` :

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `false` :

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>lengte
Retourneert het aantal tekens in een teken reeks, het aantal waarden in een matrix of het aantal sleutels in een object.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `6` :

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `3` :

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>leeg
Retourneert `true` of de teken reeks, matrix of object null of leeg is.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `true` :

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `false` :

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Voor beeld 4: null en niet-gedefinieerd
Hierbij wordt ervan uitgegaan dat het `element1` niet is `null` gedefinieerd. In het volgende voor beeld wordt geretourneerd `true` :

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>instantie
Retourneert het eerste teken van de opgegeven teken reeks. de eerste waarde van de opgegeven matrix. of de eerste sleutel en waarde van het opgegeven object.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `"f"` :

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `1` :

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
In het volgende voor beeld wordt geretourneerd `{"key1": "foobar"}` :

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>duren
Retourneert het laatste teken van de opgegeven teken reeks, de laatste waarde van de opgegeven matrix of de laatste sleutel en waarde van het opgegeven object.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `"r"` :

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `2` :

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

In het volgende voor beeld wordt geretourneerd `{"key2": "raboof"}` :

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>Houd
Retourneert een opgegeven aantal aaneengesloten tekens vanaf het begin van de teken reeks, een opgegeven aantal aaneengesloten waarden vanaf het begin van de matrix of een opgegeven aantal aaneengesloten sleutels en waarden vanaf het begin van het object.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `"foo"` :

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `[1, 2]` :

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

In het volgende voor beeld wordt geretourneerd `{"key1": "foobar"}` :

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Hiermee wordt een opgegeven aantal elementen in een verzameling omzeild en worden de resterende elementen geretourneerd.

#### <a name="example-1-string"></a>Voor beeld 1: teken reeks
In het volgende voor beeld wordt geretourneerd `"bar"` :

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Voor beeld 2: matrix
`element1`Retourneert als resultaat `[1, 2, 3]` . In het volgende voor beeld wordt geretourneerd `[3]` :

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Voor beeld 3: object
`element1`Retourneert aannemen:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
In het volgende voor beeld wordt geretourneerd `{"key2": "raboof"}` :

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logische functies
Deze functies kunnen worden gebruikt in voor waarden. Sommige functies bieden mogelijk geen ondersteuning voor alle JSON-gegevens typen.

### <a name="equals"></a>is gelijk aan
Retourneert `true` als beide para meters hetzelfde type en dezelfde waarde hebben. Deze functie ondersteunt alle JSON-gegevens typen.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[equals(0, 0)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[equals('foo', 'foo')]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Retourneert `true` als de eerste para meter strikt kleiner is dan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[less(1, 2)]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Retourneert `true` als de eerste para meter kleiner is dan of gelijk is aan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Retourneert `true` als de eerste para meter strikt groter is dan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[greater(1, 2)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Retourneert `true` als de eerste para meter groter is dan of gelijk is aan de tweede para meter. Deze functie ondersteunt alleen para meters van het type Number en string.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>en
Retourneert `true` of alle para meters worden geëvalueerd `true` . Deze functie biedt ondersteuning voor twee of meer para meters van het type Boolean.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>of
Retourneert `true` of ten minste één van de para meters wordt geëvalueerd `true` . Deze functie biedt ondersteuning voor twee of meer para meters van het type Boolean.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Retourneert `true` of de para meter resulteert in `false` . Deze functie ondersteunt alleen para meters van het type Boolean.

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[not(false)]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Voeg
Retourneert de waarde van de eerste para meter die niet null is. Deze functie ondersteunt alle JSON-gegevens typen.

Aannemen dat `element1` en niet `element2` zijn gedefinieerd. In het volgende voor beeld wordt geretourneerd `"foobar"` :

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

Deze functie is vooral nuttig in de context van een optionele aanroep die plaatsvindt als gevolg van de gebruikers actie nadat de pagina is geladen. Een voor beeld is als de beperkingen voor één veld in de gebruikers interface afhankelijk zijn van de momenteel geselecteerde waarde van een andere, **niet-zicht bare** veld. In dit geval `coalesce()` kan de functie worden gebruikt om de syntaxis geldig te maken op het moment dat de pagina wordt geladen en het gewenste effect heeft wanneer de gebruiker met het veld communiceert.

Bekijk dit `DropDown` , waarmee de gebruiker uit verschillende soorten data bases kan kiezen:

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

Als voor waarde voor de actie van een ander veld op de huidige gekozen waarde van dit veld, gebruikt u `coalesce()` , zoals hier wordt weer gegeven:

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

Dit is nodig omdat de `databaseType` is in eerste instantie niet zichtbaar is en daarom geen waarde heeft. Dit zorgt ervoor dat de volledige expressie niet correct wordt geëvalueerd.

## <a name="conversion-functions"></a>Conversie functies
Deze functies kunnen worden gebruikt om waarden te converteren tussen JSON-gegevens typen en-code ringen.

### <a name="int"></a>int
Zet de para meter om in een geheel getal. Deze functie ondersteunt para meters van het type nummer en de teken reeks.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[int('1')]"
```

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Zet de para meter om in een drijvende komma. Deze functie ondersteunt para meters van het type nummer en de teken reeks.

In het volgende voor beeld wordt geretourneerd `1.0` :

```json
"[float('1.0')]"
```

In het volgende voor beeld wordt geretourneerd `2.9` :

```json
"[float(2.9)]"
```

### <a name="string"></a>tekenreeks
Zet de para meter om in een teken reeks. Deze functie biedt ondersteuning voor para meters van alle JSON-gegevens typen.

In het volgende voor beeld wordt geretourneerd `"1"` :

```json
"[string(1)]"
```

In het volgende voor beeld wordt geretourneerd `"2.9"` :

```json
"[string(2.9)]"
```

In het volgende voor beeld wordt geretourneerd `"[1,2,3]"` :

```json
"[string([1,2,3])]"
```

In het volgende voor beeld wordt geretourneerd `"{"foo":"bar"}"` :

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>booleaans
Zet de para meter om in een Boole-waarde. Deze functie biedt ondersteuning voor para meters van het type Number, String en Boolean. Vergelijkbaar met Boole-waarden in Java script, een wille keurige waarde, behalve `0` of `'false'` als resultaat `true` .

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[bool(1)]"
```

In het volgende voor beeld wordt geretourneerd `false` :

```json
"[bool(0)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[bool(true)]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Hiermee converteert u de para meter naar een systeem eigen type. Met andere woorden, deze functie is het omgekeerde van `string()` . Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[parse('1')]"
```

In het volgende voor beeld wordt geretourneerd `true` :

```json
"[parse('true')]"
```

In het volgende voor beeld wordt geretourneerd `[1,2,3]` :

```json
"[parse('[1,2,3]')]"
```

In het volgende voor beeld wordt geretourneerd `{"foo":"bar"}` :

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codeert de para meter naar een gecodeerde base-64-teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"Zm9vYmFy"` :

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Decodeert de para meter uit een gecodeerde base-64-teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"foobar"` :

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codeert de para meter naar een URL-gecodeerde teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"https%3A%2F%2Fportal.azure.com%2F"` :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Decodeert de para meter van een URL-gecodeerde teken reeks. Deze functie ondersteunt alleen para meters van het type String.

In het volgende voor beeld wordt geretourneerd `"https://portal.azure.com/"` :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Wiskundige functies
### <a name="add"></a>add
Telt twee getallen op en retourneert het resultaat.

In het volgende voor beeld wordt geretourneerd `3` :

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Trekt het tweede getal af van het eerste getal en retourneert het resultaat.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Vermenigvuldigt twee getallen en retourneert het resultaat.

In het volgende voor beeld wordt geretourneerd `6` :

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Deelt het eerste getal door het tweede getal en retourneert het resultaat. Het resultaat is altijd een geheel getal.

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Deelt het eerste getal door het tweede getal en retourneert de rest.

In het volgende voor beeld wordt geretourneerd `0` :

```json
"[mod(6, 3)]"
```

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
Retourneert de kleine van de twee getallen.

In het volgende voor beeld wordt geretourneerd `1` :

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Retourneert de grootste van de twee getallen.

In het volgende voor beeld wordt geretourneerd `2` :

```json
"[max(1, 2)]"
```

### <a name="range"></a>bereik
Hiermee wordt een reeks integraal cijfers binnen het opgegeven bereik gegenereerd.

In het volgende voor beeld wordt geretourneerd `[1,2,3]` :

```json
"[range(1, 3)]"
```

### <a name="rand"></a>ASELECT
Retourneert een wille keurig integraal getal binnen het opgegeven bereik. Met deze functie worden geen cryptografisch beveiligde wille keurige getallen gegenereerd.

Het volgende voor beeld kan resulteren in `42` :

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Floor
Retourneert de grootste integer die kleiner dan of gelijk aan het opgegeven getal is.

In het volgende voor beeld wordt geretourneerd `3` :

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Retourneert het grootste gehele getal dat groter is dan of gelijk is aan het opgegeven getal.

In het volgende voor beeld wordt geretourneerd `4` :

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datumfuncties
### <a name="utcnow"></a>utcNow
Retourneert een teken reeks in de ISO 8601-notatie van de huidige datum en tijd op de lokale computer.

Het volgende voor beeld kan resulteren in `"1990-12-31T23:59:59.000Z"` :

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Voegt een integraal aantal seconden toe aan de opgegeven tijds tempel.

In het volgende voor beeld wordt geretourneerd `"1991-01-01T00:00:00.000Z"` :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Voegt een integraal aantal minuten toe aan de opgegeven tijds tempel.

In het volgende voor beeld wordt geretourneerd `"1991-01-01T00:00:59.000Z"` :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Voegt een integraal aantal uren toe aan de opgegeven tijds tempel.

In het volgende voor beeld wordt geretourneerd `"1991-01-01T00:59:59.000Z"` :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Volgende stappen
* Zie [overzicht van Azure Resource Manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.

