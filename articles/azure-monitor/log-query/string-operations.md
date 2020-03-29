---
title: Werken met tekenreeksen in azure monitor-logboekquery's | Microsoft Documenten
description: Beschrijft hoe u een groot aantal andere bewerkingen op tekenreeksen in Azure Monitor-logboekquery's bewerken, vergelijken, zoeken en uitvoeren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397475"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Werken met tekenreeksen in azure monitor-logboekquery's


> [!NOTE]
> U moet aan [de slag gaan met Azure Monitor Log Analytics](get-started-portal.md) en Aan de slag met Azure [Monitor-logboekquery's](get-started-queries.md) voordat u deze zelfstudie voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel wordt beschreven hoe u een verscheidenheid aan andere bewerkingen op tekenreeksen bewerken, vergelijken, zoeken en uitvoeren.

Elk teken in een tekenreeks heeft een indexnummer, afhankelijk van de locatie. Het eerste teken is op index 0, het volgende teken is 1, enzovoort. Verschillende tekenreeksfuncties gebruiken indexnummers zoals weergegeven in de volgende secties. Veel van de volgende voorbeelden gebruiken de **afdrukopdracht** om tekenreeksmanipulatie aan te tonen zonder een specifieke gegevensbron te gebruiken.


## <a name="strings-and-escaping-them"></a>Strijkers en ontsnappen ze
Tekenreekswaarden zijn verpakt met enkele of dubbele aanhalingstekens. Backslash\\( ) wordt gebruikt om te ontsnappen aan tekens aan het teken na \" het, zoals \t voor tabblad, \n voor newline, en het citaat teken zelf.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Om te\\voorkomen dat " als een\@ontsnappingsteken fungeert, voeg " als voorvoegsel toe aan de tekenreeks:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Tekenreeksvergelijkingen

Operator       |Beschrijving                         |Hoofdlettergevoelig|Voorbeeld (opbrengsten `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Is gelijk aan                              |Ja           |`"aBc" == "aBc"`
`!=`           |Niet gelijk aan                          |Ja           |`"abc" != "ABC"`
`=~`           |Is gelijk aan                              |Nee            |`"abc" =~ "ABC"`
`!~`           |Niet gelijk aan                          |Nee            |`"aBc" !~ "xyz"`
`has`          |Rechts is een hele term in de linker-side |Nee|`"North America" has "america"`
`!has`         |Rechts is geen volledige term aan de linkerkant       |Nee            |`"North America" !has "amer"` 
`has_cs`       |Rechts is een hele term in de linker-side |Ja|`"North America" has_cs "America"`
`!has_cs`      |Rechts is geen volledige term aan de linkerkant       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Rechts is een term voorvoegsel aan de linkerkant         |Nee            |`"North America" hasprefix "ame"`
`!hasprefix`   |Rechts is geen term voorvoegsel aan de linkerkant     |Nee            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Rechts is een term voorvoegsel aan de linkerkant         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Rechts is geen term voorvoegsel aan de linkerkant     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Rechts is een term achtervoegsel in de linker-side         |Nee            |`"North America" hassuffix "ica"`
`!hassuffix`   |Rechts is geen term achtervoegsel in de linkerzijde     |Nee            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Rechts is een term achtervoegsel in de linker-side         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Rechts is geen term achtervoegsel in de linkerzijde     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Rechts treedt op als een subsequentie van de linkerzijde  |Nee            |`"FabriKam" contains "BRik"`
`!contains`    |Rechts komt niet voor in de linkerzijde           |Nee            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Rechts treedt op als een subsequentie van de linkerzijde  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Rechts komt niet voor in de linkerzijde           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Rechts is een eerste subsequentie van de linkerzijde|Nee            |`"Fabrikam" startswith "fab"`
`!startswith`  |Rechts is geen eerste subsequentie van de linkerkant|Nee        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Rechts is een eerste subsequentie van de linkerzijde|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Rechts is geen eerste subsequentie van de linkerkant|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Rechts is een afsluitende subsequentie van de linkerzijde|Nee             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Rechts is geen afsluitende subsequentie van de linkerkant|Nee         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Rechts is een afsluitende subsequentie van de linkerzijde|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Rechts is geen afsluitende subsequentie van de linkerkant|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|links bevat een overeenkomst voor rechts        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Gelijk aan een van de elementen       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Niet gelijk aan een van de elementen   |Ja           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Hiermee worden gebeurtenissen van een subtekenreeks in een tekenreeks geteld. Kan overeenkomen met gewone tekenreeksen of regex gebruiken. Plain string matches may overlap while regex matches do not.

### <a name="syntax"></a>Syntaxis
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumenten:
- `text`- De invoertekenreeks 
- `search`- Effen tekenreeks of reguliere expressie die overeenkomt met de tekst.
- `kind` - _normale_ | _regex_ (standaard: normaal).

### <a name="returns"></a>Retourneert

Het aantal keren dat de zoektekenreeks kan worden gekoppeld in de container. Plain string matches may overlap while regex matches do not.

### <a name="examples"></a>Voorbeelden

#### <a name="plain-string-matches"></a>Effen tekenreeksovereenkomsten

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex komt overeen

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Extract

Krijgt een overeenkomst voor een reguliere expressie van een bepaalde tekenreeks. Optioneel wordt de geëxtraheerde subtekenreeks ook omgezet naar het opgegeven type.

### <a name="syntax"></a>Syntaxis

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenten

- `regex`- Een regelmatige uitdrukking.
- `captureGroup`- Een positieve gehele constante die de te extraheren afvanggroep aangeeft. 0 voor de gehele wedstrijd, 1 voor de waarde die wordt geëvenaard door de eerste '('haakjes')' in de reguliere expressie, 2 of meer voor volgende haakjes.
- `text`- Een string om te zoeken.
- `typeLiteral`- Een optioneel type letterlijk (bijvoorbeeld type(lang)). Indien aanwezig, wordt de geëxtraheerde subtekenreeks omgezet in dit type.

### <a name="returns"></a>Retourneert
De substring komt overeen met de aangegeven capture group captureGroup, optioneel geconverteerd naar typeLiteral.
Als er geen overeenkomst is of als de typeconversie mislukt, geeft u null terug.

### <a name="examples"></a>Voorbeelden

In het volgende voorbeeld wordt het laatste octet van *ComputerIP* uit een hartslagrecord gehaald:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

In het volgende voorbeeld wordt het laatste octet geëxtraheerd, naar een *echt* type (getal) gegoten en wordt de volgende IP-waarde berekend
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

In het onderstaande voorbeeld *wordt* gezocht naar een definitie van 'Duur'. De wedstrijd wordt gegoten naar *echt* en vermenigvuldigd met een tijdconstante (1 s) *die Duur werpt om tijdspanne te typen.*
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isleeg, isnotleeg, niet leeg

- *isempty* retourneert waar als het argument een lege tekenreeks of null is (zie ook *isnull*).
- *isnotempty* retourneert waar als het argument geen lege tekenreeks of een null is (zie ook *isnotnull*). alias: *niet leeg*.

### <a name="syntax"></a>Syntaxis

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Voorbeelden

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Splitst een URL op in de onderdelen (protocol, host, poort, enz.) en retourneert een woordenboekobject met de onderdelen als tekenreeksen.

### <a name="syntax"></a>Syntaxis

```
parseurl(urlstring)
```

### <a name="examples"></a>Voorbeelden

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Het resultaat zal zijn:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>vervangen

Hiermee vervangt u alle regex-overeenkomsten door een andere tekenreeks. 

### <a name="syntax"></a>Syntaxis

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenten

- `regex`- De reguliere uitdrukking aan te passen door. Het kan vanggroepen bevatten in '('haakjes')'.
- `rewrite`- De vervangende regex voor elke wedstrijd gemaakt door matching regex. Gebruik \0 om te verwijzen naar de hele match, \1 voor de eerste groep vastleggen, \2, enzovoort voor volgende groep van vastleggen.
- `input_text`- De invoertekenreeks om in te zoeken.

### <a name="returns"></a>Retourneert
De tekst na het vervangen van alle wedstrijden van regex met evaluaties van herschrijven. Overeenkomsten overlappen elkaar niet.

### <a name="examples"></a>Voorbeelden

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Kan de volgende resultaten hebben:

Activiteit                                        |Vervangen
------------------------------------------------|----------------------------------------------------------
4663 - Er is geprobeerd toegang te krijgen tot een object  |Activiteits-ID 4663: er is geprobeerd toegang te krijgen tot een object.


## <a name="split"></a>split

Splitst een bepaalde tekenreeks op basis van een opgegeven scheidingsteken en retourneert een array van de resulterende subtekenreeksen.

### <a name="syntax"></a>Syntaxis
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumenten:

- `source`- De tekenreeks die moet worden gesplitst volgens de opgegeven scheidingsteken.
- `delimiter`- De scheidingsteken die wordt gebruikt om de brontekenreeks te splitsen.
- `requestedIndex`- Een optionele nul-gebaseerde index. Indien aanwezig, houdt de geretourneerde tekenreeksarray alleen dat item vast (als dit bestaat).


### <a name="examples"></a>Voorbeelden

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat strcat

Hiermee worden tekenreeksargumenten samengevoegd (ondersteunt 1-16 argumenten).

### <a name="syntax"></a>Syntaxis
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Voorbeelden
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen strlen

Geeft als resultaat de lengte van een tekenreeks.

### <a name="syntax"></a>Syntaxis
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Voorbeelden
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>Subtekenreeks

Haalt een subtekenreeks uit een bepaalde brontekenreeks, te beginnen bij de opgegeven index. Optioneel kan de lengte van de gevraagde subtekenreeks worden opgegeven.

### <a name="syntax"></a>Syntaxis
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumenten:

- `source`- De brontekenreeks waaruit de subtekenreeks wordt genomen.
- `startingIndex`- De nulgebaseerde starttekenpositie van de gevraagde subtekenreeks.
- `length`- Een optionele parameter die kan worden gebruikt om de gevraagde lengte van de geretourneerde subtekenreeks op te geven.

### <a name="examples"></a>Voorbeelden
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Hiermee converteert u een bepaalde tekenreeks naar alle onderste of hoofdletters.

### <a name="syntax"></a>Syntaxis
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Voorbeelden
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Volgende stappen
Ga verder met de geavanceerde tutorials:
* [Aggregatiefuncties](aggregations.md)
* [Geavanceerde aggregaties](advanced-aggregations.md)
* [Grafieken en diagrammen](charts.md)
* [Werken met JSON en datastructuren](json-data-structures.md)
* [Geavanceerde query's schrijven](advanced-query-writing.md)
* [Joins - cross-analyse](joins.md)
