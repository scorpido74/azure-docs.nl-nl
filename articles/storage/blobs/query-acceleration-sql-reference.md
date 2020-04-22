---
title: SQL-taalverwijzing voor queryversnelling (voorbeeld)
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van sql-syntaxis voor queryversnelling.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: cea5fb507225f063e2d48c56fae254e123a8f72b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772117"
---
# <a name="query-acceleration-sql-language-reference-preview"></a>SQL-taalverwijzing voor queryversnelling (voorbeeld)

Queryversnelling ondersteunt een ANSI SQL-achtige taal voor het uitdrukken van query's over blob-inhoud.  Het SQL-dialect voor queryversnelling is een subset van ANSI SQL, met een beperkte set ondersteunde gegevenstypen, operators, enz., maar het breidt ook uit op ANSI SQL om query's te ondersteunen via hiërarchische semi-gestructureerde gegevensindelingen zoals JSON. 

> [!NOTE]
> De functie queryversnelling bevindt zich in een openbare preview en is beschikbaar in de regio's Canada Centraal en France Central. Zie het artikel [Bekende problemen](data-lake-storage-known-issues.md) om beperkingen te bekijken. Zie [dit formulier](https://aka.ms/adls/qa-preview-signup)om u in te schrijven voor het voorbeeld. 

## <a name="select-syntax"></a>Syntaxis selecteren

De enige SQL-instructie die wordt ondersteund door queryversnelling is de SELECT-instructie. In dit voorbeeld wordt elke rij geretourneerd voor welke expressie true retourneert.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Voor csv-geformatteerde gegevens `BlobStorage`moet de *tabel* .  Dit betekent dat de query wordt uitgevoerd tegen welke blob is opgegeven in de REST-aanroep.
Voor JSON-opgemaakte gegevens is *de tabel* een 'tabelbeschrijving'.   Zie de sectie [Tabeldescriptors](#table-descriptors) van dit artikel.

In het volgende voorbeeld retourneert deze instructie voor elke rij waarvoor de *expressie* WAAR waar terugkeert, een nieuwe rij die is gemaakt van de evaluatie van elk van de projectieexpressieen.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

In het volgende voorbeeld wordt een geaggregeerde berekening (bijvoorbeeld de gemiddelde waarde van een bepaalde kolom) over elk van de rijen geretourneerd waarvoor *expressie* true retourneert. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

In het volgende voorbeeld worden geschikte verschuivingen geretourneerd voor het splitsen van een blob met CSV-indeling.  Zie de [sectie Sys.Split](#sys-split) van dit artikel.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## <a name="data-types"></a>Gegevenstypen

|Gegevenstype|Beschrijving|
|---------|-------------------------------------------|
|INT      |64-bits ondertekend geheel getal.                     |
|Float    |64-bits ("dubbele precisie") zwevend punt.|
|Tekenreeks   |Unicode-tekenreeks met variabele lengte.            |
|Tijdstempel|Een punt in de tijd.                           |
|Booleaanse  |Waar of onwaar.                             |

Bij het lezen van waarden uit csv-opgemaakte gegevens worden alle waarden gelezen als tekenreeksen.  Tekenreekswaarden kunnen worden geconverteerd naar andere typen met CAST-expressies.  Waarden kunnen impliciet worden gegoten naar andere typen, afhankelijk van de context. Zie [Voorrang voor gegevenstype (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017)voor meer informatie.

## <a name="expressions"></a>Expressies

### <a name="referencing-fields"></a>Verwijzingen naar velden

Voor JSON-opgemaakte gegevens of csv-opgemaakte gegevens met een koptekstrij, kunnen velden met naam worden verwezen.  Veldnamen kunnen worden geciteerd of niet geciteerd. Geciteerde veldnamen zijn ingesloten in dubbele aanhalingstekens ("), kunnen spaties bevatten en zijn hoofdlettergevoelig.  Niet-geciteerde veldnamen zijn hoofdletters ongevoelig en mogen geen speciale tekens bevatten.

In csv-geformatteerde gegevens kunnen velden ook worden verwezen door ordinal, vooraf zijn bevestigd met een underscore (_) teken.  Het eerste veld kan bijvoorbeeld worden aangeduid als _1 of naar het elfde veld kan worden verwezen als _11.  Het verwijzen naar velden door ordinal is handig voor csv-opgemaakte gegevens die geen koptekstrij bevatten, in welk geval de enige manier om naar een bepaald veld te verwijzen is door ordinal.

### <a name="operators"></a>Operators

De volgende standaard SQL-operators worden ondersteund:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

Als gegevenstypen links en rechts van een operator verschillend zijn, wordt automatische conversie uitgevoerd volgens de hier opgegeven regels: [Prioriteit van het gegevenstype (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

De SQL-taal voor queryversnelling ondersteunt slechts een zeer kleine subset van de gegevenstypen die in dat artikel worden besproken.  Zie de sectie [Gegevenstypen](#data-types) van dit artikel.

### <a name="casts"></a>Werpt

De SQL-taal voor queryversnelling ondersteunt de CAST-operator, volgens de regels hier: [Gegevenstypeconversie (Database Engine)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

De SQL-taal voor queryversnelling ondersteunt slechts een kleine subset van de gegevenstypen die in dat artikel worden besproken.  Zie de sectie [Gegevenstypen](#data-types) van dit artikel.

### <a name="string-functions"></a>Tekenreeksfuncties

De SQL-taal voor queryversnellingondersteunt de volgende standaard SQL-tekenreeksfuncties:

``LIKE``, ``CHAR_LENGTH``, ``CHARACTER_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

Hier zijn een paar voorbeelden:

|Functie|Voorbeeld|Resultaat|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

De [functie VIND-ik-leuk](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) helpt u om te zoeken naar een patroon. Hier volgen een paar voorbeelden die de functie [VIND-ik-leuk](https://docs.microsoft.com/sql/t-sql/language-elements/like-transact-sql?view=sql-server-ver15) gebruiken om in de gegevenstekenreeks ``abc,abd,cd\ntest,test2,test3\na_bc,xc%d^e,gh[i ``te zoeken.

|Query’s uitvoeren|Voorbeeld|
|--|--|
|``SELECT _1, _2, _3 from BlobStorage where _2 LIKE 'a%'``|``abc,abd,cd\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a[bcd]c``|``abc,abd,cd\n``|
|``SELECT _1 from BlobStorage where _2 LIKE '[^xyz]%'``|``abc\ntest\n``|
|``SELECT * from BlobStorage where _1 LIKE 'a_``|``abc,abd,cd\n``|
|``SELECT _2,_3 from BlobStorage where _3 LIKE '[g-h]_![[a-j]' Escape '!'``|``xc%d^e,gh[i\n``|

### <a name="date-functions"></a>Datumfuncties

De volgende standaard SQL-datumfuncties worden ondersteund:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Momenteel converteren we alle [datumformaten van standaard IS08601.](https://www.w3.org/TR/NOTE-datetime) 

#### <a name="date_add-function"></a>DATE_ADD functie

De SQL-taal voor queryversnelling ondersteunt jaar, maand, ``DATE_ADD`` dag, uur, minuut, tweede voor de functie.

Voorbeelden:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### <a name="date_diff-function"></a>DATE_DIFF functie

De SQL-taal voor queryversnelling ondersteunt jaar, maand, ``DATE_DIFF`` dag, uur, minuut, tweede voor de functie.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT, functie

Voor EXTRACT anders dan datumonderdeel ``DATE_ADD`` dat voor de functie wordt ondersteund, ondersteunt de SQL-taal voor queryversnelling timezone_hour en timezone_minute als datumonderdeel.

Voorbeelden:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING functie

Voorbeelden:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

In deze tabel worden tekenreeksen beschreven die u ``TO_STRING`` gebruiken om de uitvoerindeling van de functie op te geven.

|Tekenreeks opmaken    |Uitvoer                               |
|-----------------|-------------------------------------|
|yy               |Jaar in 2 cijfers – 1999 als '99'|
|y                |Jaar in 4 cijferig formaat               |
|yyyy             |Jaar in 4 cijferig formaat               |
|M                |Maand van het jaar – 1                    |
|MM               |Nul gewatteerde maand – 01               |
|MMM              |Afgekort. maand van jaar -JAN            |
|MMMM             |Volle maand – mei                      |
|d                |Dag van de maand (1-31)                  |
|dd               |Nul gewatteerde dag van de maand (01-31)     |
|a                |AM of PM                             |
|h                |Uur van de dag (1-12)                   |
|hh               |Zero gewatteerde uren od dag (01-12)     |
|H                |Uur van de dag (0-23)                   |
|HH               |Nul gewatteerd uur van de dag (00-23)      |
|m                |Minuut van uur (0-59)                |
|mm               |Nul gewatteerde minuten (00-59)           |
|s                |Tweede van minuten (0-59)             |
|ss               |Nul gewatteerde seconden (00-59)          |
|S                |Fractie van seconden (0,1-0,9)        |
|SS               |Fractie seconden (0,01-0,99)      |
|Sss              |Fractie seconden (0,001-0,999)    |
|X                |Offset in uren                      |
|XX of XXXX       |Verschuiving in uren en minuten (+0430)  |
|XXX of XXXXX     |Verschuiving in uren en minuten (-07:00) |
|x                |Offset in uren (7)                  |
|xx of xxxx       |Offset in uur en minuut (+0530)    |
|Xxx of xxxxx     |Verschuiving in uur en minuut (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP functie

Alleen IS08601-formaten worden ondersteund.

Voorbeelden:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> U de ``UTCNOW`` functie ook gebruiken om de systeemtijd te krijgen.


## <a name="aggregate-expressions"></a>Geaggregeerde expressies

Een SELECT-instructie kan een of meer projectieexpressies of één samengevoegde expressie bevatten.  De volgende geaggregeerde expressies worden ondersteund:

|Expressie|Beschrijving|
|--|--|
|[AANTAL.\*](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Geeft als resultaat het aantal records dat overeenkomt met de predicaatexpressie.|
|[AANTAL(expressie)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Geeft als resultaat het aantal records waarvoor expressie niet-null is.|
|[GEMIDDELDE(expressie)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Geeft als resultaat het gemiddelde van de niet-null-waarden van expressie.|
|[MIN(expressie)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Geeft als resultaat de minimale niet-null-waarde van expressie.|
|[MAX(expressie](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |Geeft als resultaat de maximale niet-null-waarde van expressie.|
|[SOM(expressie)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Geeft als resultaat de som van alle niet-null-waarden van expressie.|

### <a name="missing"></a>Ontbrekende

De ``IS MISSING`` operator is de enige niet-standaard die de SQL-taal voor queryversnelling ondersteunt.  Voor JSON-gegevens wordt als een veld ontbreekt in ``IS MISSING`` een bepaalde invoerrecord, wordt het expressieveld geëvalueerd op de Booleaanse waarde true.

<a id="table-descriptors" />

## <a name="table-descriptors"></a>Tabelbeschrijvingen

Voor CSV-gegevens is de `BlobStorage`tabelnaam altijd .  Bijvoorbeeld:

```sql
SELECT * FROM BlobStorage
```

Voor JSON-gegevens zijn aanvullende opties beschikbaar:

```sql
SELECT * FROM BlobStorage[*].path
```

Hiermee kunnen query's over subsets van de JSON-gegevens worden ingeschakeld.

Voor JSON-query's u het pad in een deel van de FROM-component vermelden. Deze paden helpen om de subset van JSON-gegevens te ontleden. Deze paden kunnen verwijzen naar JSON-array- en objectwaarden.

Laten we een voorbeeld nemen om dit in meer detail te begrijpen.

Dit zijn onze voorbeeldgegevens:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Mogelijk bent u alleen `warehouses` geïnteresseerd in het JSON-object uit de bovenstaande gegevens. Het `warehouses` object is een JSON-arraytype, dus je dit vermelden in de FROM-component. Uw voorbeeldquery kan er ongeveer zo uitzien.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

De query krijgt alle velden, maar selecteert alleen de breedtegraad.

Als u alleen toegang `dimensions` wilt krijgen tot de JSON-objectwaarde, u verwijzen naar dat object in uw query gebruiken. Bijvoorbeeld:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Dit beperkt ook uw toegang `dimensions` tot leden van het object. Als u toegang wilt krijgen tot andere leden van JSON-velden en innerlijke waarden van JSON-objecten, u een query gebruiken zoals weergegeven in het volgende voorbeeld:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage en BlobStorage\*verwijzen beide naar het hele object. Als u echter een pad in de FROM-component hebt, moet\*u BlobStorage gebruiken.

<a id="sys-split" />

## <a name="syssplit"></a>Sys.Split (Sys.Split)

Dit is een speciale vorm van de SELECT-instructie, die alleen beschikbaar is voor csv-geformatteerde gegevens.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Gebruik deze instructie in gevallen waarin u CSV-gegevensrecords in batches wilt downloaden en verwerken. Op die manier u records parallel verwerken in plaats van alle records tegelijk te downloaden. Met deze instructie worden geen records uit het CSV-bestand teruggegeven. In plaats daarvan retourneert het een verzameling batchgroottes. U vervolgens elke batchgrootte gebruiken om een batch met gegevensrecords op te halen. 

Gebruik de *parameter split_size* om het aantal bytes op te geven dat elke batch moet bevatten. Als u bijvoorbeeld slechts 10 MB aan gegevens tegelijk wilt verwerken, ziet uw `SELECT sys.split(10485760)FROM BlobStorage` instructie er als volgt uit: omdat 10 MB gelijk is aan 10.485.760 bytes. Elke batch bevat zoveel records als kan passen in die 10 MB. 

In de meeste gevallen is de grootte van elke batch iets groter dan het getal dat u opgeeft. Dat komt omdat een batch geen gedeeltelijke record kan bevatten. Als de laatste record in een batch begint voor het einde van uw drempelwaarde, is de batch groter, zodat deze de volledige record kan bevatten. De grootte van de laatste batch zal waarschijnlijk kleiner zijn dan de grootte die u opgeeft.

>[!NOTE]
> De split_size moet ten minste 10 MB (10485760) zijn.

## <a name="see-also"></a>Zie ook

- [Azure Data Lake Storage-queryversnelling (voorbeeld)](data-lake-storage-query-acceleration.md)
- [Gegevens filteren met azure data lake storage-queryversnelling (voorbeeld)](data-lake-storage-query-acceleration-how-to.md)

