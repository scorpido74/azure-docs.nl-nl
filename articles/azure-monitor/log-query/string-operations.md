---
title: Werken met teken reeksen in Azure Monitor-logboek query's | Microsoft Docs
description: Hierin wordt beschreven hoe u een aantal andere bewerkingen voor teken reeksen in Azure Monitor-logboek query's bewerkt, vergelijkt, doorzoekt en uitvoert.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 0d7bf025b414df819887192bb59f7fd8da64b5d9
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932939"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Werken met teken reeksen in Azure Monitor-logboek query's


> [!NOTE]
> U moet aan de [slag met Azure Monitor Log Analytics om](get-started-portal.md) aan de slag te gaan [met Azure monitor-logboek query's](get-started-queries.md) voordat u deze zelf studie voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel wordt beschreven hoe u een aantal andere bewerkingen op teken reeksen kunt bewerken, vergelijken, zoeken en uitvoeren.

Elk teken in een teken reeks heeft een index nummer, afhankelijk van de locatie. Het eerste teken is bij index 0, het volgende teken is 1, enzovoort. Met verschillende teken reeks functies worden index nummers gebruikt, zoals wordt weer gegeven in de volgende secties. In veel van de volgende voor beelden wordt de opdracht **afdrukken** gebruikt voor het demonstreren van teken reeks manipulatie zonder een specifieke gegevens bron te gebruiken.


## <a name="strings-and-escaping-them"></a>Teken reeksen en Escapes
Teken reeks waarden worden verpakt met één of dubbele aanhalings tekens. Back slash (\\) wordt gebruikt om tekens naar het volgende teken te escapen, zoals \t voor Tab, \n voor nieuwe regel en \" het aanhalings teken zelf.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Als u wilt voor komen dat '\\' als escape teken fungeert, voegt u '\@' toe als voor voegsel voor de teken reeks:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Teken reeks vergelijkingen

Operator       |Beschrijving                         |Hoofdletter gevoelig|Voor beeld (levert `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Is gelijk aan                              |Ja           |`"aBc" == "aBc"`
`!=`           |Niet gelijk aan                          |Ja           |`"abc" != "ABC"`
`=~`           |Is gelijk aan                              |Nee            |`"abc" =~ "ABC"`
`!~`           |Niet gelijk aan                          |Nee            |`"aBc" !~ "xyz"`
`has`          |Aan de rechter kant is een hele term aan de linkerkant |Nee|`"North America" has "america"`
`!has`         |Rechts aan de rechter kant is geen volledige term aan de linkerkant       |Nee            |`"North America" !has "amer"` 
`has_cs`       |Aan de rechter kant is een hele term aan de linkerkant |Ja|`"North America" has_cs "America"`
`!has_cs`      |Rechts aan de rechter kant is geen volledige term aan de linkerkant       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Rechts aan de rechter kant is een voor voegsel van de term aan de linkerkant         |Nee            |`"North America" hasprefix "ame"`
`!hasprefix`   |Rechts aan de rechter kant is geen term voorvoegsel in links     |Nee            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Rechts aan de rechter kant is een voor voegsel van de term aan de linkerkant         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Rechts aan de rechter kant is geen term voorvoegsel in links     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Rechts aan de rechter kant is een term achtervoegsel aan de linkerkant         |Nee            |`"North America" hassuffix "ica"`
`!hassuffix`   |Rechts aan de rechter kant is geen term achtervoegsel aan de linkerkant     |Nee            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Rechts aan de rechter kant is een term achtervoegsel aan de linkerkant         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Rechts aan de rechter kant is geen term achtervoegsel aan de linkerkant     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Aan de rechter kant vindt u een subreeks van links  |Nee            |`"FabriKam" contains "BRik"`
`!contains`    |Aan de rechter kant komt niet aan de linkerkant           |Nee            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Aan de rechter kant vindt u een subreeks van links  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Aan de rechter kant komt niet aan de linkerkant           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Aan de rechter kant is een eerste subreeks van links aan de linkerkant|Nee            |`"Fabrikam" startswith "fab"`
`!startswith`  |Rechts aan de rechter kant is geen eerste subreeks van links aan de linkerkant|Nee        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Aan de rechter kant is een eerste subreeks van links aan de linkerkant|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Rechts aan de rechter kant is geen eerste subreeks van links aan de linkerkant|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Rechts aan de rechter kant is een afsluitende subreeks van links|Nee             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Rechts aan de rechter kant is geen afsluitende subreeks van links aan de linkerkant|Nee         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Rechts aan de rechter kant is een afsluitende subreeks van links|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Rechts aan de rechter kant is geen afsluitende subreeks van links aan de linkerkant|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|links van de rechter kant bevat een overeenkomst voor rechts        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Is gelijk aan een van de elementen       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Is niet gelijk aan een van de elementen   |Ja           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Telt het aantal exemplaren van een subtekenreeks in een teken reeks. Kan teken reeksen overeenkomen of regex gebruiken. Niet-overeenkomende teken reeks overeenkomsten kunnen overlappen terwijl regex-overeenkomsten niet zijn toegestaan.

### <a name="syntax"></a>Syntaxis
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Opmerkingen
- `text`-de invoer teken reeks 
- `search` teken reeks of reguliere expressie die binnen de tekst moet komen.
- `kind` - _normale_ | _regex_ (standaard: normaal).

### <a name="returns"></a>Retourneert

Het aantal keren dat de zoek teken reeks in de container kan worden gevonden. Niet-overeenkomende teken reeks overeenkomsten kunnen overlappen terwijl regex-overeenkomsten niet zijn toegestaan.

### <a name="examples"></a>Voorbeelden

#### <a name="plain-string-matches"></a>Teken reeks overeenkomsten zonder opmaak

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex-overeenkomsten

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>daaruit

Hiermee wordt een overeenkomst opgehaald voor een reguliere expressie van een opgegeven teken reeks. Hiermee wordt ook de geëxtraheerde subtekenreeks geconverteerd naar het opgegeven type.

### <a name="syntax"></a>Syntaxis

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenten

- `regex`-een reguliere expressie.
- `captureGroup`-een positieve integer-constante die aangeeft welke opname groep moet worden geëxtraheerd. 0 voor de volledige overeenkomst, 1 voor de waarde die overeenkomt met het eerste ' (' haakje ') ' in de reguliere expressie, 2 of meer voor de volgende haakjes.
- `text`: een teken reeks die moet worden gezocht.
- `typeLiteral`-een optioneel type letterlijke waarde (bijvoorbeeld typeof (Long)). Als deze wordt vermeld, wordt de geëxtraheerde subtekenreeks geconverteerd naar dit type.

### <a name="returns"></a>Retourneert
De subtekenreeks die overeenkomt met de aangegeven captureGroup van de opname groep, optioneel geconverteerd naar typeLiteral.
Als er geen overeenkomst is, of als het type conversie mislukt, retourneert u null.

### <a name="examples"></a>Voorbeelden

In het volgende voor beeld wordt het laatste octet van *ComputerIP* geëxtraheerd uit een heartbeat-record:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

In het volgende voor beeld wordt het laatste octet geëxtraheerd, wordt dit geconverteerd naar een *echt* type (getal) en wordt de volgende IP-waarde berekend:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

In het onderstaande voor beeld zoekt de teken reeks *tracering* naar een definitie van ' duration '. De overeenkomst wordt in *werkelijkheid* geconverteerd en vermenigvuldigd met een tijd constante (1 s) *die de duur van het type time-out overgeeft*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty, isnotempty, geneigd

- *IsEmpty* retourneert True als het argument een lege teken reeks of null is (Zie ook *IsNull*).
- *isnotempty* retourneert ' True ' als het argument geen lege teken reeks of null is (Zie ook *isnotnull*). alias: de *neiging*.

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

Hiermee wordt een URL gesplitst in de onderdelen (protocol, host, poort enzovoort) en wordt een Dictionary-object geretourneerd dat de onderdelen als teken reeksen bevat.

### <a name="syntax"></a>Syntaxis

```
parseurl(urlstring)
```

### <a name="examples"></a>Voorbeelden

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

De uitkomst is:
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

Vervangt alle regex-overeenkomsten met een andere teken reeks. 

### <a name="syntax"></a>Syntaxis

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenten

- `regex`: de reguliere expressie die moet worden vergeleken met. Het kan opname groepen bevatten in ' (' haakjes ') '.
- `rewrite`-de vervangende regex voor een overeenkomst die is gemaakt met een overeenkomende regex. Gebruik \ 0 om te verwijzen naar de volledige overeenkomst, \ 1 voor de eerste vastleg groep, \ 2, enzovoort voor volgende opname groepen.
- `input_text`: de invoer teken reeks waarnaar moet worden gezocht.

### <a name="returns"></a>Retourneert
De tekst na het vervangen van alle treffers van regex met evaluaties van herschrijven. Overeenkomsten overlappen elkaar niet.

### <a name="examples"></a>Voorbeelden

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Kan de volgende resultaten hebben:

Activiteit                                        |Geplaatst
------------------------------------------------|----------------------------------------------------------
4663-er is geprobeerd om toegang te krijgen tot een object  |Activiteits-ID 4663: er is geprobeerd om toegang te krijgen tot een object.


## <a name="split"></a>split

Splitst een opgegeven teken reeks volgens een opgegeven scheidings teken en retourneert een matrix van de resulterende subtekenreeksen.

### <a name="syntax"></a>Syntaxis
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Opmerkingen

- `source`-de teken reeks die moet worden gesplitst op basis van het opgegeven scheidings teken.
- `delimiter`-het scheidings teken dat wordt gebruikt om de bron teken reeks te splitsen.
- `requestedIndex`-een optionele index op basis van nul. Indien opgegeven, bevat de geretourneerde teken reeks matrix alleen dat item (indien aanwezig).


### <a name="examples"></a>Voorbeelden

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Voegt teken reeks argumenten samen (ondersteunt 1-16-argumenten).

### <a name="syntax"></a>Syntaxis
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Voorbeelden
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Retourneert de lengte van een teken reeks.

### <a name="syntax"></a>Syntaxis
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Voorbeelden
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>subtekenreeks

Hiermee wordt een subtekenreeks geëxtraheerd uit een bepaalde bron teken reeks, beginnend bij de opgegeven index. Optioneel: de lengte van de aangevraagde subtekenreeks kan worden opgegeven.

### <a name="syntax"></a>Syntaxis
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Opmerkingen

- `source`-de bron teken reeks waaruit de subtekenreeks wordt opgehaald.
- `startingIndex`-de begin positie op basis van nul van de aangevraagde subtekenreeks.
- `length`: een optionele para meter die kan worden gebruikt om de aangevraagde lengte van de geretourneerde subtekenreeks op te geven.

### <a name="examples"></a>Voorbeelden
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Converteert een opgegeven teken reeks naar een kleine letter of een hoofd letters.

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
Ga door met de geavanceerde zelf studies:
* [Aggregatie functies](aggregations.md)
* [Geavanceerde aggregaties](advanced-aggregations.md)
* [Grafieken en diagrammen](charts.md)
* [Werken met JSON en gegevens structuren](json-data-structures.md)
* [Geavanceerde query's schrijven](advanced-query-writing.md)
* [Samen voegingen-cross-analyse](joins.md)
