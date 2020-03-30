---
title: UI-definitiefuncties maken
description: Beschrijft de functies die moeten worden gebruikt bij het samenstellen van gebruikersinterfacedefinities voor Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248448"
---
# <a name="createuidefinition-functions"></a>Functies UiIDefinition maken
Deze sectie bevat de handtekeningen voor alle ondersteunde functies van een CreateUiDefinition.

Als u een functie wilt gebruiken, omringt u de declaratie met vierkante haakjes. Bijvoorbeeld:

```json
"[function()]"
```

Tekenreeksen en andere functies kunnen worden verwezen als parameters voor een functie, maar tekenreeksen moeten worden omgeven in afzonderlijke aanhalingstekens. Bijvoorbeeld:

```json
"[fn1(fn2(), 'foobar')]"
```

Waar van toepassing u met behulp van de puntoperator verwijzen naar de eigenschappen van de uitvoer van een functie. Bijvoorbeeld:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Verwijzingen naar functies
Deze functies kunnen worden gebruikt om uitvoer te verwijzen uit de eigenschappen of context van een CreateUiDefinition.

### <a name="basics"></a>Basics
Geeft als resultaat de uitvoerwaarden van een element dat is gedefinieerd in de basisstap.

In het volgende voorbeeld wordt `foo` de uitvoer van het element geretourneerd dat wordt genoemd in de basisstap:

```json
"[basics('foo')]"
```

### <a name="steps"></a>stappen
Geeft als resultaat de uitvoerwaarden van een element dat is gedefinieerd in de opgegeven stap. Gebruik in plaats daarvan om de uitvoerwaarden van elementen in de basisstap te krijgen. `basics()`

In het volgende voorbeeld wordt `bar` de uitvoer `foo`van het element met de naam :

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Geeft als resultaat de locatie die is geselecteerd in de basisstap of de huidige context.

Het volgende voorbeeld `"westus"`kan terugkeren:

```json
"[location()]"
```

## <a name="string-functions"></a>Tekenreeksfuncties
Deze functies kunnen alleen worden gebruikt met JSON-tekenreeksen.

### <a name="concat"></a>Concat
Concatenates een of meer snaren.

Als bijvoorbeeld de uitvoerwaarde `element1` `"bar"`van als , in `"foobar!"`dit voorbeeld de tekenreeks retourneert:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>Subtekenreeks
Geeft als resultaat de subtekenreeks van de opgegeven tekenreeks. De subtekenreeks begint bij de opgegeven index en heeft de opgegeven lengte.

Het volgende `"ftw"`voorbeeld retourneert:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>vervangen
Retourneert een tekenreeks waarin alle exemplaren van de opgegeven tekenreeks in de huidige tekenreeks worden vervangen door een andere tekenreeks.

Het volgende `"Everything is awesome!"`voorbeeld retourneert:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Guid
Genereert een wereldwijd unieke tekenreeks (GUID).

Het volgende voorbeeld `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`kan terugkeren:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Retourneert een tekenreeks die is geconverteerd naar kleine letters.

Het volgende `"foobar"`voorbeeld retourneert:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>Toupper
Retourneert een tekenreeks die is geconverteerd naar hoofdletters.

Het volgende `"FOOBAR"`voorbeeld retourneert:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Verzamelingsfuncties
Deze functies kunnen worden gebruikt met verzamelingen, zoals JSON-tekenreeksen, arrays en objecten.

### <a name="contains"></a>bevat
Retourneert `true` als een tekenreeks de opgegeven subtekenreeks bevat, een array de opgegeven waarde bevat of een object de opgegeven sleutel bevat.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `true`voorbeeld retourneert:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `false`voorbeeld retourneert:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Het volgende `true`voorbeeld retourneert:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>lengte
Geeft als resultaat het aantal tekens in een tekenreeks, het aantal waarden in een array of het aantal sleutels in een object.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `6`voorbeeld retourneert:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `3`voorbeeld retourneert:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Het volgende `2`voorbeeld retourneert:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>leeg
Retourneert `true` als de tekenreeks, array of object null of leeg is.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `true`voorbeeld retourneert:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `false`voorbeeld retourneert:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Het volgende `false`voorbeeld retourneert:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Voorbeeld 4: null en niet gedefinieerd
Veronderstel `element1` `null` is of niet gedefinieerd. Het volgende `true`voorbeeld retourneert:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>Eerste
Geeft als resultaat het eerste teken van de opgegeven tekenreeks; eerste waarde van de opgegeven array; of de eerste sleutel en waarde van het opgegeven object.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `"f"`voorbeeld retourneert:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `1`voorbeeld retourneert:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Het volgende `{"key1": "foobar"}`voorbeeld retourneert:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>Laatste
Geeft als resultaat het laatste teken van de opgegeven tekenreeks, de laatste waarde van de opgegeven array of de laatste sleutel en waarde van het opgegeven object.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `"r"`voorbeeld retourneert:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `2`voorbeeld retourneert:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Het volgende `{"key2": "raboof"}`voorbeeld retourneert:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>Nemen
Geeft als resultaat een opgegeven aantal aaneengesloten tekens vanaf het begin van de tekenreeks, een opgegeven aantal aaneengesloten waarden vanaf het begin van de array of een opgegeven aantal aaneengesloten sleutels en waarden vanaf het begin van het object.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `"foo"`voorbeeld retourneert:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `[1, 2]`voorbeeld retourneert:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Het volgende `{"key1": "foobar"}`voorbeeld retourneert:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Hiermee omzeilt u een bepaald aantal elementen in een verzameling en retourneert u de resterende elementen.

#### <a name="example-1-string"></a>Voorbeeld 1: tekenreeks
Het volgende `"bar"`voorbeeld retourneert:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Voorbeeld 2: array
Veronderstel `element1` `[1, 2, 3]`rendementen . Het volgende `[3]`voorbeeld retourneert:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Voorbeeld 3: object
Ga `element1` ervan uit dat retouren:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Het volgende `{"key2": "raboof"}`voorbeeld retourneert:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logische functies
Deze functies kunnen worden gebruikt in conditionals. Sommige functies ondersteunen mogelijk niet alle JSON-gegevenstypen.

### <a name="equals"></a>equals
Retourneert `true` als beide parameters hetzelfde type en dezelfde waarde hebben. Deze functie ondersteunt alle JSON-gegevenstypen.

Het volgende `true`voorbeeld retourneert:

```json
"[equals(0, 0)]"
```

Het volgende `true`voorbeeld retourneert:

```json
"[equals('foo', 'foo')]"
```

Het volgende `false`voorbeeld retourneert:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Retourneert `true` als de eerste parameter strikt kleiner is dan de tweede parameter. Deze functie ondersteunt alleen parameters van typenummer en tekenreeks.

Het volgende `true`voorbeeld retourneert:

```json
"[less(1, 2)]"
```

Het volgende `false`voorbeeld retourneert:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Retourneert `true` als de eerste parameter kleiner is dan of gelijk is aan de tweede parameter. Deze functie ondersteunt alleen parameters van typenummer en tekenreeks.

Het volgende `true`voorbeeld retourneert:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Retourneert `true` als de eerste parameter strikt groter is dan de tweede parameter. Deze functie ondersteunt alleen parameters van typenummer en tekenreeks.

Het volgende `false`voorbeeld retourneert:

```json
"[greater(1, 2)]"
```

Het volgende `true`voorbeeld retourneert:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Retourneert `true` als de eerste parameter groter is dan of gelijk is aan de tweede parameter. Deze functie ondersteunt alleen parameters van typenummer en tekenreeks.

Het volgende `true`voorbeeld retourneert:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>en
Retourneert `true` als alle `true`parameters evalueren op . Deze functie ondersteunt alleen twee of meer parameters van het type Boolean.

Het volgende `true`voorbeeld retourneert:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Het volgende `false`voorbeeld retourneert:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>of
Retourneert `true` als ten minste één `true`van de parameters evalueert tot . Deze functie ondersteunt alleen twee of meer parameters van het type Boolean.

Het volgende `true`voorbeeld retourneert:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Het volgende `true`voorbeeld retourneert:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Retourneert `true` als de `false`parameter evalueert op . Deze functie ondersteunt alleen parameters van het type Boolean.

Het volgende `true`voorbeeld retourneert:

```json
"[not(false)]"
```

Het volgende `false`voorbeeld retourneert:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>samensmelten
Geeft als resultaat de waarde van de eerste niet-null-parameter. Deze functie ondersteunt alle JSON-gegevenstypen.

Veronderstel `element1` `element2` en zijn niet gedefinieerd. Het volgende `"foobar"`voorbeeld retourneert:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Conversiefuncties
Deze functies kunnen worden gebruikt om waarden tussen JSON-gegevenstypen en coderingen om te zetten.

### <a name="int"></a>int
Hiermee converteert u de parameter naar een geheel getal. Deze functie ondersteunt parameters van typenummer en tekenreeks.

Het volgende `1`voorbeeld retourneert:

```json
"[int('1')]"
```

Het volgende `2`voorbeeld retourneert:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Hiermee converteert u de parameter naar een zwevend punt. Deze functie ondersteunt parameters van typenummer en tekenreeks.

Het volgende `1.0`voorbeeld retourneert:

```json
"[float('1.0')]"
```

Het volgende `2.9`voorbeeld retourneert:

```json
"[float(2.9)]"
```

### <a name="string"></a>tekenreeks
Hiermee converteert u de parameter naar een tekenreeks. Deze functie ondersteunt parameters van alle JSON-gegevenstypen.

Het volgende `"1"`voorbeeld retourneert:

```json
"[string(1)]"
```

Het volgende `"2.9"`voorbeeld retourneert:

```json
"[string(2.9)]"
```

Het volgende `"[1,2,3]"`voorbeeld retourneert:

```json
"[string([1,2,3])]"
```

Het volgende `"{"foo":"bar"}"`voorbeeld retourneert:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>Booleaanse waarde
Hiermee converteert u de parameter naar een Booleaan. Deze functie ondersteunt parameters van typenummer, tekenreeks en Booleaan. Vergelijkbaar met Booleans in JavaScript, `'false'` `true`elke waarde behalve `0` of retourneert .

Het volgende `true`voorbeeld retourneert:

```json
"[bool(1)]"
```

Het volgende `false`voorbeeld retourneert:

```json
"[bool(0)]"
```

Het volgende `true`voorbeeld retourneert:

```json
"[bool(true)]"
```

Het volgende `true`voorbeeld retourneert:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Converteert de parameter naar een native type. Met andere woorden, deze functie `string()`is het omgekeerde van . Deze functie ondersteunt alleen parameters van de teksttekenreeks.

Het volgende `1`voorbeeld retourneert:

```json
"[parse('1')]"
```

Het volgende `true`voorbeeld retourneert:

```json
"[parse('true')]"
```

Het volgende `[1,2,3]`voorbeeld retourneert:

```json
"[parse('[1,2,3]')]"
```

Het volgende `{"foo":"bar"}`voorbeeld retourneert:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codeert de parameter op een basis-64 gecodeerde tekenreeks. Deze functie ondersteunt alleen parameters van de teksttekenreeks.

Het volgende `"Zm9vYmFy"`voorbeeld retourneert:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64 decodeBase64
Decodeert de parameter van een basis-64 gecodeerde tekenreeks. Deze functie ondersteunt alleen parameters van de teksttekenreeks.

Het volgende `"foobar"`voorbeeld retourneert:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>codeUriComponent
Codeert de parameter op een door de URL gecodeerde tekenreeks. Deze functie ondersteunt alleen parameters van de teksttekenreeks.

Het volgende `"https%3A%2F%2Fportal.azure.com%2F"`voorbeeld retourneert:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Decodeert de parameter uit een door de URL gecodeerde tekenreeks. Deze functie ondersteunt alleen parameters van de teksttekenreeks.

Het volgende `"https://portal.azure.com/"`voorbeeld retourneert:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Wiskundige functies
### <a name="add"></a>add
Hiermee voegt u twee getallen toe en geeft u het resultaat als resultaat.

Het volgende `3`voorbeeld retourneert:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Hiermee wordt het tweede getal van het eerste getal afgetrokken en wordt het resultaat geretourneerd.

Het volgende `1`voorbeeld retourneert:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>Mul
Vermenigvuldigt twee getallen en geeft het resultaat als resultaat.

Het volgende `6`voorbeeld retourneert:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Hiermee wordt het eerste getal gedeeld door het tweede getal en wordt het resultaat geretourneerd. Het resultaat is altijd een geheel getal.

Het volgende `2`voorbeeld retourneert:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>Mod
Hiermee wordt het eerste getal gedeeld door het tweede getal en wordt de rest geretourneerd.

Het volgende `0`voorbeeld retourneert:

```json
"[mod(6, 3)]"
```

Het volgende `2`voorbeeld retourneert:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min.
Geeft als resultaat het kleine van de twee getallen.

Het volgende `1`voorbeeld retourneert:

```json
"[min(1, 2)]"
```

### <a name="max"></a>Max
Geeft als resultaat de grotere van de twee getallen.

Het volgende `2`voorbeeld retourneert:

```json
"[max(1, 2)]"
```

### <a name="range"></a>Bereik
Hiermee genereert u een reeks integrale getallen binnen het opgegeven bereik.

Het volgende `[1,2,3]`voorbeeld retourneert:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Geeft als resultaat een willekeurig integraal getal binnen het opgegeven bereik. Deze functie genereert geen cryptografisch veilige willekeurige getallen.

Het volgende voorbeeld `42`kan terugkeren:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Verdieping
Geeft als resultaat het grootste gehele getal dat kleiner is dan of gelijk is aan het opgegeven getal.

Het volgende `3`voorbeeld retourneert:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil (ceil)
Geeft als resultaat het grootste gehele getal dat groter is dan of gelijk is aan het opgegeven getal.

Het volgende `4`voorbeeld retourneert:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datumfuncties
### <a name="utcnow"></a>utcNu
Retourneert een tekenreeks in de ISO 8601-notatie van de huidige datum en tijd op de lokale computer.

Het volgende voorbeeld `"1990-12-31T23:59:59.000Z"`kan terugkeren:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Hiermee voegt u een integraal aantal seconden toe aan de opgegeven tijdstempel.

Het volgende `"1991-01-01T00:00:00.000Z"`voorbeeld retourneert:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Hiermee voegt u een integraal aantal minuten toe aan de opgegeven tijdstempel.

Het volgende `"1991-01-01T00:00:59.000Z"`voorbeeld retourneert:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Hiermee voegt u een integraal aantal uren toe aan de opgegeven tijdstempel.

Het volgende `"1991-01-01T00:59:59.000Z"`voorbeeld retourneert:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Volgende stappen
* Zie Overzicht azure [resource manager](../management/overview.md)voor een inleiding tot Azure Resource Manager.

