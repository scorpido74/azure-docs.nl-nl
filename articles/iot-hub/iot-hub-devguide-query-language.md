---
title: De querytaal Azure IoT Hub begrijpen | Microsoft Documenten
description: Ontwikkelaarshandleiding - beschrijving van de SQL-achtige IoT Hub-querytaal die wordt gebruikt om informatie over apparaat/moduletweelingen en taken op te halen bij uw IoT-hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: bcc53322ac6942b52853be561bc3441e23fbf53b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632932"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-querytaal voor apparaat- en moduledubbels, taken en-berichtroutering

IoT Hub biedt een krachtige SQL-achtige taal om informatie op te halen over [apparaattweelingen,](iot-hub-devguide-device-twins.md) [moduletweelingen,](iot-hub-devguide-module-twins.md) [taken](iot-hub-devguide-jobs.md)en [berichtroutering](iot-hub-devguide-messages-d2c.md). Dit artikel presenteert:

* Een inleiding tot de belangrijkste functies van de IoT Hub-querytaal en
* De gedetailleerde beschrijving van de taal. Zie [query's in berichtroutering](../iot-hub/iot-hub-devguide-routing-query-syntax.md)voor meer informatie over querytaal voor berichtroutering.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Dubbele query's voor apparaten en modules

[Apparaattweelingen](iot-hub-devguide-device-twins.md) en [moduletweelingen](iot-hub-devguide-module-twins.md) kunnen willekeurige JSON-objecten bevatten als zowel tags als eigenschappen. Met IoT Hub u apparaattweelingen en modules opvragen als één JSON-document met alle dubbele informatie.

Stel bijvoorbeeld dat uw IoT-hub-apparaattweeling de volgende structuur heeft (moduletweeling zou vergelijkbaar zijn alleen met een extra moduleId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Dubbele query's van het apparaat

IoT Hub stelt de apparaattweeling bloot als een documentverzameling genaamd **apparaten.** De volgende query haalt bijvoorbeeld de hele set apparaattweelingen op:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDKs](iot-hub-devguide-sdks.md) ondersteunen het verbeteren van grote resultaten.

Met IoT Hub u het filteren van apparaattweelingen ophalen met willekeurige omstandigheden. Als u bijvoorbeeld apparaattweelingen wilt ontvangen waarbij de **tag location.region** is ingesteld op **VS,** gebruikt u de volgende query:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Booleaanse operatoren en rekenkundige vergelijkingen worden ook ondersteund. Als u bijvoorbeeld apparaattweelingen in de VS wilt ophalen die zijn geconfigureerd om minder dan elke minuut telemetrie te verzenden, gebruikt u de volgende query:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Als een gemak, is het ook mogelijk om array constanten te gebruiken met de **IN** en **NIN** (niet in) operators. Als u bijvoorbeeld apparaattweelingen wilt ophalen die WiFi of bedrade connectiviteit melden, gebruikt u de volgende query:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Het is vaak noodzakelijk om alle apparaattweelingen te identificeren die een specifieke eigenschap bevatten. IoT Hub ondersteunt `is_defined()` de functie hiervoor. Als u bijvoorbeeld apparaattweelingen `connectivity` wilt ophalen die de eigenschap definiëren, gebruikt u de volgende query:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Raadpleeg de [sectie WAAR-clausule](iot-hub-devguide-query-language.md#where-clause) voor de volledige verwijzing van de filtermogelijkheden.

Ook groepering en aggregaties worden ondersteund. Als u bijvoorbeeld het aantal apparaten in elke telemetrieconfiguratiestatus wilt vinden, gebruikt u de volgende query:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Met deze groeperingsquery wordt een resultaat retourneren dat vergelijkbaar is met het volgende voorbeeld:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

In dit voorbeeld hebben drie apparaten melding gemaakt van een geslaagde configuratie, twee passen de configuratie nog steeds toe en één meldde een fout.

Met projectiequery's kunnen ontwikkelaars alleen de eigenschappen retourneren waar ze om geven. Als u bijvoorbeeld de laatste activiteitstijd van alle losgekoppelde apparaten wilt ophalen, gebruikt u de volgende query:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Dubbele query's module

Query's op module twins is vergelijkbaar met het opvragen op apparaat tweelingen, maar met behulp van een andere verzameling / naamruimte; in plaats van vanaf **apparaten,** bevraagt u vanaf **devices.modules:**

```sql
SELECT * FROM devices.modules
```

We staan geen join toe tussen de collecties van apparaten en apparaten.modules. Als u moduletweelingen op verschillende apparaten wilt bevragen, doet u dit op basis van tags. Deze query retourneert alle moduletweelingen op alle apparaten met de scanstatus:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Deze query retourneert alle moduletweelingen met de scanstatus, maar alleen op de opgegeven subset van apparaten:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# voorbeeld

De queryfunctionaliteit wordt weergegeven door de [C#-service SDK](iot-hub-devguide-sdks.md) in de klasse **RegistryManager.**

Hier is een voorbeeld van een eenvoudige query:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Het **queryobject** wordt geinstantieerd met een paginagrootte (tot 100). Vervolgens worden meerdere pagina's opgehaald door de **GetNextAsTwinAsync-methoden** meerdere keren aan te roepen.

Het queryobject stelt meerdere **volgende** waarden bloot, afhankelijk van de deserialisatieoptie die vereist is voor de query. Bijvoorbeeld apparaattweeling- of taakobjecten of gewone JSON bij het gebruik van projecties.

### <a name="nodejs-example"></a>Voorbeeld van Knooppunt.js

De queryfunctionaliteit wordt weergegeven door de [Azure IoT-service SDK voor Node.js](iot-hub-devguide-sdks.md) in het **object Registry.**

Hier is een voorbeeld van een eenvoudige query:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Het **queryobject** wordt geinstantieerd met een paginagrootte (tot 100). Vervolgens worden meerdere pagina's opgehaald door meerdere keren de **nextAsTwin-methode** aan te roepen.

Het queryobject stelt meerdere **volgende** waarden bloot, afhankelijk van de deserialisatieoptie die vereist is voor de query. Bijvoorbeeld apparaattweeling- of taakobjecten of gewone JSON bij het gebruik van projecties.

### <a name="limitations"></a>Beperkingen

> [!IMPORTANT]
> Queryresultaten kunnen een paar minuten vertraging hebben met betrekking tot de nieuwste waarden in apparaattweelingen. Als u individuele apparaattweelingen op id bevraagt, gebruikt u de [API voor twee restaanvragen](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin). Deze API retourneert altijd de nieuwste waarden en heeft hogere beperkingslimieten. U de REST-API rechtstreeks uitgeven of de gelijkwaardige functionaliteit gebruiken in een van de [Azure IoT Hub Service SDKs.](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

Momenteel worden vergelijkingen alleen ondersteund tussen primitieve typen `... WHERE properties.desired.config = properties.reported.config` (geen objecten), bijvoorbeeld alleen worden ondersteund als deze eigenschappen primitieve waarden hebben.

## <a name="get-started-with-jobs-queries"></a>Aan de slag met vacatures

[Taken](iot-hub-devguide-jobs.md) bieden een manier om bewerkingen uit te voeren op sets apparaten. Elke apparaat tweeling bevat de informatie van de banen waarvan het deel uitmaakt in een verzameling genaamd **banen**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Momenteel is deze verzameling opvraagbaar als **devices.jobs** in de IoT Hub-querytaal.

> [!IMPORTANT]
> Momenteel wordt de eigenschap jobs nooit geretourneerd bij het bevragen van apparaattweelingen. Dat wil zeggen, query's die 'FROM devices' bevatten. De eigenschap jobs is alleen rechtstreeks toegankelijk `FROM devices.jobs`met query's met behulp van .
>
>

Als u bijvoorbeeld alle taken (in het verleden en gepland) wilt krijgen die van invloed zijn op één apparaat, u de volgende query gebruiken:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Houd er rekening mee hoe deze query de apparaatspecifieke status (en mogelijk de directe methoderespons) van elke geretourneerde taak biedt.

Het is ook mogelijk om te filteren met willekeurige Booleaanse omstandigheden op alle objecteigenschappen in de **devices.jobs** collectie.

Als u bijvoorbeeld alle voltooide apparaatdubbele updatetaken wilt ophalen die na september 2016 voor een specifiek apparaat zijn gemaakt, gebruikt u de volgende query:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

U ook de resultaten per apparaat van één taak ophalen.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Beperkingen

Momenteel worden query's op **devices.jobs** niet ondersteund:

* Projecties zijn dus `SELECT *` alleen mogelijk.
* Voorwaarden die verwijzen naar de apparaattweeling naast taakeigenschappen (zie de vorige sectie).
* Het uitvoeren van aggregaties, zoals tellen, avg, groeperen op.

## <a name="basics-of-an-iot-hub-query"></a>Basisbeginselen van een IoT Hub-query

Elke IoT Hub query bestaat uit SELECT en FROM clausules, met optionele WHERE en GROUP BY clausules. Elke query wordt uitgevoerd op een verzameling JSON-documenten, bijvoorbeeld apparaattweelingen. De FROM-clausule geeft aan dat de documentverzameling moet worden herhaald op (**apparaten**, **devices.modules**of **devices.jobs**). Vervolgens wordt het filter in de WHERE-component toegepast. Bij aggregaties worden de resultaten van deze stap gegroepeerd zoals gespecificeerd in de GROUP BY-clausule. Voor elke groep wordt een rij gegenereerd zoals gespecificeerd in de SELECT-component.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM-component

De **<from_specification>** clausule kan slechts drie waarden aannemen: VAN **apparaten** tot queryapparaattweelingen, **VAN devices.modules** tot querymoduletwins of **VAN devices.jobs** tot querytaak per apparaatdetails.

## <a name="where-clause"></a>WHERE-component

De **WHERE <filter_condition>** clausule is optioneel. Hierin worden een of meer voorwaarden vermeld waaraan de JSON-documenten in de FROM-collectie moeten voldoen om als onderdeel van het resultaat te worden opgenomen. Elk JSON-document moet de opgegeven voorwaarden evalueren om "waar" te zijn om in het resultaat te worden opgenomen.

De toegestane voorwaarden worden beschreven in sectie [Expressies en voorwaarden](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>SELECT-component

De **SELECT-<select_list>** verplicht is en geeft aan welke waarden uit de query worden opgehaald. Hiermee worden de JSON-waarden opgegeven die moeten worden gebruikt om nieuwe JSON-objecten te genereren.
Voor elk element van de gefilterde (en eventueel gegroepeerde) subset van de FROM-verzameling genereert de projectiefase een nieuw JSON-object. Dit object is opgebouwd met de waarden die zijn opgegeven in de SELECT-component.

Hieronder volgt de grammatica van de SELECT-clausule:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** verwijst naar een eigenschap van het JSON-document in de FROM-verzameling. Enkele voorbeelden van SELECT-clausules zijn te vinden in de sectie Aan de slag met apparaattweelingquery's.

Momenteel worden selectieclausules anders dan **SELECT*** alleen ondersteund in geaggregeerde query's op apparaattweelingen.

## <a name="group-by-clause"></a>GROEP PER-clausule

De **GROEP DOOR <group_specification>-clausule** is een optionele stap die wordt uitgevoerd na het filter dat is opgegeven in de WHERE-component en vóór de projectie die in de SELECT is opgegeven. Het groepeert documenten op basis van de waarde van een kenmerk. Deze groepen worden gebruikt om geaggregeerde waarden te genereren zoals gespecificeerd in de SELECT-component.

Een voorbeeld van een query met GROEP BY is:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

De formele syntaxis voor GROEP BY is:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** verwijst naar een eigenschap van het JSON-document in de FROM-verzameling.

Momenteel wordt de GROUP BY-clausule alleen ondersteund bij het bevragen van apparaattweelingen.

> [!IMPORTANT]
> De `group` term wordt momenteel behandeld als een speciaal zoekwoord in query's. In het geval `group` dat u als uw eigendomsnaam gebruikt, u deze `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`omringen met dubbele haakjes om fouten te voorkomen, bijvoorbeeld.
>

## <a name="expressions-and-conditions"></a>Expressies en voorwaarden

Op een hoog niveau, een *uitdrukking*:

* Evalueert naar een instantie van een JSON-type (zoals Booleaan, getal, tekenreeks, array of object).
* Wordt gedefinieerd door het manipuleren van gegevens afkomstig van het APPARAAT JSON-document en constanten met behulp van ingebouwde operators en functies.

*Voorwaarden* zijn expressies die worden geëvalueerd op een Booleaan. Elke constante anders dan **Booleaanse waar** wordt beschouwd als **vals**. Deze regel omvat **null,** **niet gedefinieerd,** elk object of arrayinstantie, een tekenreeks en de Booleaanse **false**.

De syntaxis voor expressies is:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Als u wilt begrijpen waar elk symbool in de syntaxis van de expressie voor staat, raadpleegt u de volgende tabel:

| Symbool | Definitie |
| --- | --- |
| attribute_name | Elke eigenschap van het JSON-document in de **FROM-collectie.** |
| binary_operator | Elke binaire operator die wordt vermeld in de sectie [Operators.](#operators) |
| function_name| Elke functie die wordt vermeld in de sectie [Functies.](#functions) |
| decimal_literal |Een vlotter uitgedrukt in decimale notatie. |
| hexadecimal_literal |Een getal uitgedrukt door de tekenreeks '0x' gevolgd door een reeks hexadecimale cijfers. |
| string_literal |String literalals zijn Unicode-tekenreeksen die worden vertegenwoordigd door een reeks van nul of meer Unicode-tekens of vluchtreeksen. String literalals zijn ingesloten in enkele aanhalingstekens of dubbele aanhalingstekens. Toegestane `\'`ontsnappingen: `\"` `\\`, `\uXXXX` , voor Unicode-tekens gedefinieerd door 4 hexadecimale cijfers. |

### <a name="operators"></a>Operators

De volgende marktdeelnemers worden ondersteund:

| Familie | Operators |
| --- | --- |
| Rekenkundig |+, -, *, /, % |
| Logisch |EN, OF, NIET |
| Vergelijking |=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Bij het opvragen van tweelingen en taken is de enige ondersteunde functie:

| Functie | Beschrijving |
| -------- | ----------- |
| IS_DEFINED(eigenschap) | Geeft als resultaat een Booleaanse die aangeeft `null`of aan de eigenschap een waarde is toegewezen (inclusief ). |

In routesomstandigheden worden de volgende wiskundige functies ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| ABS(x) | Retourneert de absolute (positieve) waarde van de opgegeven numerieke expressie. |
| EXP(x) | Geeft als resultaat de exponentiële waarde van de opgegeven numerieke expressie (e^x). |
| POWER(x,y) | Geeft als resultaat de waarde van de opgegeven expressie aan het opgegeven vermogen (x^y).|
| VIERKANT(x)    | Geeft als resultaat het kwadraat van de opgegeven numerieke waarde. |
| PLAFOND(x) | Retourneert het kleinste gehele getal dat groter is dan of gelijk is aan de opgegeven numerieke expressie. |
| FLOOR(x) | Retourneert het grootste gehele getal dat kleiner is dan of gelijk is aan de opgegeven numerieke expressie. |
| SIGN(x) | Geeft als resultaat het positieve teken (+1), nul (0) of negatief (-1) van de opgegeven numerieke expressie.|
| SQRT(x) | Geeft als resultaat de vierkantswortel van de opgegeven numerieke waarde. |

In routesomstandigheden worden de volgende typecontrole- en gietfuncties ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| AS_NUMBER | Hiermee converteert u de invoertekenreeks naar een getal. `noop`als invoer een getal is; `Undefined` als tekenreeks geen getal vertegenwoordigt.|
| IS_ARRAY | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een array is. |
| IS_BOOL | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een Booleaan is. |
| IS_DEFINED | Retourneert een Booleaanse waarde die aangeeft of aan de eigenschap een waarde is toegewezen. Dit wordt alleen ondersteund wanneer de waarde een primitief type is. Primitieve typen zijn tekenreeks, Booleaan, numeriek of `null`. DateTime, objecttypen en arrays worden niet ondersteund. |
| IS_NULL | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie null is. |
| IS_NUMBER | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een getal is. |
| IS_OBJECT | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een JSON-object is. |
| IS_PRIMITIVE | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van `null`de opgegeven expressie een primitief is (tekenreeks, Booleaan, numeriek of ). |
| IS_STRING | Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een tekenreeks is. |

In routesomstandigheden worden de volgende tekenreeksfuncties ondersteund:

| Functie | Beschrijving |
| -------- | ----------- |
| CONCAT(x, y, ...) | Retourneert een tekenreeks die het resultaat is van het samenvoegen van twee of meer tekenreekswaarden. |
| LENGTE(x) | Geeft als resultaat het aantal tekens van de opgegeven tekenreeksexpressie.|
| LAGER(x) | Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters. |
| BOVEN(x) | Retourneert een tekenreeksexpressie na het converteren van tekens in kleine letters naar hoofdletters. |
| SUBSTRING(tekenreeks, begin [, lengte]) | Geeft als resultaat een deel van een tekenreeksexpressie die begint bij de opgegeven tekenpositie op nul en blijft naar de opgegeven lengte of tot het einde van de tekenreeks. |
| INDEX_OF(tekenreeks, fragment) | Retourneert de beginpositie van het eerste exemplaar van de tweede tekenreeksexpressie binnen de eerste opgegeven tekenreeksexpressie, of -1 als de tekenreeks niet is gevonden.|
| STARTS_WITH(x, y) | Retourneert een Booleaan die aangeeft of de eerste tekenreeksexpressie begint met de tweede. |
| ENDS_WITH(x, y) | Retourneert een Booleaan die aangeeft of de eerste tekenreeksexpressie eindigt met de tweede. |
| CONTAINS(x,y) | Retourneert een Booleaanse waarde die aangeeft of de eerste tekenreeksexpressie de tweede bevat. |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het uitvoeren van query's in uw apps met [Azure IoT SDKs.](iot-hub-devguide-sdks.md)
