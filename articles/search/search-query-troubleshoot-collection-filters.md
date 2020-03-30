---
title: Problemen met OData-verzamelingsfilters oplossen
titleSuffix: Azure Cognitive Search
description: Meer informatie over benaderingen voor het oplossen van filterfouten in OData-verzamelingsfilterfouten in Azure Cognitive Search-query's.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113092"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Problemen met OData-verzamelingsfilters oplossen in Azure Cognitive Search

Als u [wilt filteren](query-odata-filter-orderby-syntax.md) op verzamelingsvelden in Azure Cognitive Search, u de [ `any` operatoren en `all` operatoren](search-query-odata-collection-operators.md) gebruiken samen met **lambda-expressies.** Een lambda-expressie is een subfilter dat op elk element van een verzameling wordt toegepast.

Niet elk kenmerk van filterexpressies is beschikbaar in een lambda-expressie. Welke functies beschikbaar zijn, hangt af van het gegevenstype van het verzamelingsveld dat u wilt filteren. Dit kan leiden tot een fout als u een functie probeert te gebruiken in een lambda-expressie die niet wordt ondersteund in die context. Als u dergelijke fouten tegenkomt terwijl u een complex filter over verzamelingsvelden probeert te schrijven, helpt dit artikel u het probleem op te lossen.

## <a name="common-collection-filter-errors"></a>Algemene foutfouten voor verzamelingsfilters

In de volgende tabel worden fouten weergegeven die u tegenkomen wanneer u een verzamelingsfilter probeert uit te voeren. Deze fouten worden uitgevoerd wanneer u een functie van filterexpressies gebruikt die niet worden ondersteund in een lambda-expressie. Elke fout geeft enige richtlijnen over hoe u uw filter herschrijven om de fout te voorkomen. De tabel bevat ook een link naar het relevante gedeelte van dit artikel met meer informatie over hoe u die fout voorkomen.

| Foutbericht | Situatie | Zie voor meer informatie |
| --- | --- | --- |
| De functie 'ismatch' heeft geen parameters die gebonden zijn aan de bereikvariabele 's'. Alleen gebonden veldverwijzingen worden ondersteund in lambda-expressies ('any' of 'all'). Wijzig uw filter zodat de 'ismatch'-functie zich buiten de lambda-expressie bevindt en probeer het opnieuw. | Het `search.ismatch` `search.ismatchscoring` gebruiken of binnen een lambdauitdrukking | [Regels voor het filteren van complexe verzamelingen](#bkmk_complex) |
| Ongeldige lambda-uitdrukking. Vond een test voor gelijkheid of ongelijkheid waarbij het tegenovergestelde werd verwacht in een lambda-expressie die over een veld van type Verzameling (Edm.String) wordt opgesteld. Voor 'any' gebruik je uitdrukkingen van het formulier 'x eq y' of 'search.in(...)'. Gebruik voor 'allen' uitdrukkingen van het formulier 'x ne y', 'not (x eq y)', of 'not search.in(...)'. | Filteren op een veld van het type`Collection(Edm.String)` | [Regels voor het filteren van tekenreeksverzamelingen](#bkmk_strings) |
| Ongeldige lambda-uitdrukking. Ik heb een niet-ondersteunde vorm van complexe Booleaanse expressie gevonden. Voor 'any', gebruik je uitdrukkingen die 'O's van AND's' zijn, ook wel disjunctief normaal formulier genoemd. Bijvoorbeeld: "a en b) of c en d)" wanneer a, b, c en d vergelijkings- of gelijkheidssubexpressies zijn. Gebruik voor 'all' uitdrukkingen die 'AND's of ORs' zijn, ook wel bekend als Conjunctive Normal Form. Bijvoorbeeld: "a of b) en c of d)" wanneer a, b, c en d vergelijkings- of ongelijkheidssubexpressies zijn. Voorbeelden van vergelijkingsuitdrukkingen: 'x gt 5', 'x le 2'. Voorbeeld van een gelijkheidsuitdrukking: 'x eq 5'. Voorbeeld van een ongelijkheidsuitdrukking: 'x ne 5'. | Filteren op velden `Collection(Edm.DateTimeOffset)`van `Collection(Edm.Double)` `Collection(Edm.Int32)`het type , , of`Collection(Edm.Int64)` | [Regels voor het filteren van vergelijkbare verzamelingen](#bkmk_comparables) |
| Ongeldige lambda-uitdrukking. Vond een niet-ondersteund gebruik van geo.distance() of geo.intersects() in een lambda-expressie die over een veld van type Verzameling (Edm.GeographyPoint) wordt gewijzigd. Voor 'elke', zorg ervoor dat u geo.distance() te vergelijken met behulp van de 'lt' of 'le' operators en zorg ervoor dat elk gebruik van geo.intersects() niet wordt ontkend. Voor 'alles' moet u geo.distance() vergelijken met de 'gt' of 'ge'-operatoren en ervoor zorgen dat elk gebruik van geo.intersects() wordt ontkend. | Filteren op een veld van het type`Collection(Edm.GeographyPoint)` | [Regels voor het filteren van GeographyPoint-verzamelingen](#bkmk_geopoints) |
| Ongeldige lambda-uitdrukking. Complexe Booleaanse expressies worden niet ondersteund in lambda-expressies die herhalen op velden van het type Collection (Edm.GeographyPoint). Voor 'any', sluit je dan aan bij sub-expressies met 'of'; 'en' wordt niet ondersteund. Voor 'allen' u zich aansluiten bij sub-expressies met 'en'; 'of' wordt niet ondersteund. | Filteren op velden `Collection(Edm.String)` van type of`Collection(Edm.GeographyPoint)` | [Regels voor het filteren van tekenreeksverzamelingen](#bkmk_strings) <br/><br/> [Regels voor het filteren van GeographyPoint-verzamelingen](#bkmk_geopoints) |
| Ongeldige lambda-uitdrukking. Vond een vergelijkingsoperator (een van 'lt', 'le', 'gt' of 'ge'). Alleen gelijkheidsoperatoren zijn toegestaan in lambda-expressies die herhalen op velden van het type Collection (Edm.String). Voor 'any', gebruik je uitdrukkingen van het formulier 'x eq y'. Gebruik voor 'allen' uitdrukkingen van het formulier 'x ne y' of 'not (x eq y)'. | Filteren op een veld van het type`Collection(Edm.String)` | [Regels voor het filteren van tekenreeksverzamelingen](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Geldige verzamelingsfilters schrijven

De regels voor het schrijven van geldige verzamelingsfilters zijn verschillend voor elk gegevenstype. In de volgende secties worden de regels beschreven door voorbeelden weer te geven van welke filterfuncties worden ondersteund en welke niet:

- [Regels voor het filteren van tekenreeksverzamelingen](#bkmk_strings)
- [Regels voor het filteren van Booleaanse collecties](#bkmk_bools)
- [Regels voor het filteren van GeographyPoint-verzamelingen](#bkmk_geopoints)
- [Regels voor het filteren van vergelijkbare verzamelingen](#bkmk_comparables)
- [Regels voor het filteren van complexe verzamelingen](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regels voor het filteren van tekenreeksverzamelingen

Binnen lambda uitdrukkingen voor string collecties, de enige `eq` `ne`vergelijking operators die kunnen worden gebruikt zijn en .

> [!NOTE]
> Azure Cognitive Search ondersteunt `lt` / `le` / `gt` / `ge` de operatoren niet voor tekenreeksen, zowel binnen als buiten een lambda-expressie.

Het lichaam `any` van een kan alleen testen `all` op gelijkheid, terwijl het lichaam van een alleen kan testen op ongelijkheid.

Het is ook mogelijk om `or` meerdere uitdrukkingen te `any`combineren `and` via in het `all`lichaam van een , en via in het lichaam van een . Aangezien `search.in` de functie is gelijk aan `or`het combineren van gelijkheid controles met `any`, het is ook toegestaan in het lichaam van een . Omgekeerd, `not search.in` is toegestaan in het `all`lichaam van een .

Deze expressies zijn bijvoorbeeld toegestaan:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

terwijl deze expressies niet zijn toegestaan:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regels voor het filteren van Booleaanse collecties

Het `Edm.Boolean` type ondersteunt `eq` `ne` alleen de en operators. Als zodanig heeft het niet veel zin om het combineren van dergelijke `and` / `or` clausules die dezelfde bereik variabele met controleren, omdat dat altijd zou leiden tot tautologieën of tegenstrijdigheden.

Hier volgen enkele voorbeelden van filters op Booleaanse collecties die zijn toegestaan:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

In tegenstelling tot stringcollecties hebben Booleaanse collecties geen grenzen aan welke operator kan worden gebruikt in welk type lambdaexpressie. Beide `eq` `ne` en kan worden gebruikt `any` `all`in het lichaam van of .

Expressies zoals de volgende zijn niet toegestaan voor Booleaanse collecties:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regels voor het filteren van GeographyPoint-verzamelingen

Waarden van `Edm.GeographyPoint` type in een verzameling kunnen niet rechtstreeks met elkaar worden vergeleken. In plaats daarvan moeten ze `geo.distance` worden `geo.intersects` gebruikt als parameters voor de en functies. De `geo.distance` functie moet op zijn beurt worden vergeleken met `lt` `le`een `gt`afstandswaarde met behulp van een van de vergelijkingsoperatoren , , of `ge`. Deze regels zijn ook van toepassing op velden van Edm.GeographyPoint die niet worden getoond.

Net als `Edm.GeographyPoint` string collecties, collecties hebben een aantal regels voor hoe de geo-ruimtelijke functies kunnen worden gebruikt en gecombineerd in de verschillende soorten lambda uitdrukkingen:

- Welke vergelijkingsoperatoren u `geo.distance` met de functie gebruiken, is afhankelijk van het type lambda-expressie. Voor, `any`u `lt` `le`alleen gebruiken of. Voor, `all`u `gt` `ge`alleen gebruiken of. U uitingen met `geo.distance`betrekking tot, maar je moet`geo.distance(...) lt x` `not (geo.distance(...) ge x)` de `geo.distance(...) le x` `not (geo.distance(...) gt x)`vergelijking operator (wordt en wordt) te veranderen.
- In het lichaam `all`van `geo.intersects` een , moet de functie worden ontkend. Omgekeerd, in het lichaam `any`van `geo.intersects` een , de functie mag niet worden ontkend.
- In het lichaam `any`van een , geo-ruimtelijke uitdrukkingen kunnen worden gecombineerd met behulp van `or`. In het lichaam `all`van een , `and`kunnen dergelijke uitdrukkingen worden gecombineerd met behulp van .

De bovenstaande beperkingen bestaan om soortgelijke redenen als de beperking van gelijkheid/ongelijkheid op tekenreekscollecties. Zie [OData-verzamelingsfilters in Azure Cognitive Search begrijpen](search-query-understand-collection-filters.md) voor een diepere blik op deze redenen.

Hier volgen enkele voorbeelden `Edm.GeographyPoint` van filters voor verzamelingen die zijn toegestaan:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Expressies zoals de volgende zijn `Edm.GeographyPoint` niet toegestaan voor verzamelingen:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regels voor het filteren van vergelijkbare verzamelingen

Deze sectie is van toepassing op alle volgende gegevenstypen:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typen zoals `Edm.Int32` `Edm.DateTimeOffset` en ondersteunen alle zes `eq`de `ne` `lt`vergelijkingsoperatoren: , , , `le` `gt`en `ge`. Lambda uitdrukkingen over collecties van deze types kunnen eenvoudige uitdrukkingen bevatten met behulp van een van deze operatoren. Dit geldt `any` voor `all`beide en . Deze filters zijn bijvoorbeeld toegestaan:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Er zijn echter beperkingen op hoe dergelijke vergelijkingsuitdrukkingen kunnen worden gecombineerd in complexere expressies in een lambda-expressie:

- Regels `any`voor :
  - Eenvoudige ongelijkheidsuitdrukkingen kunnen niet nuttig worden gecombineerd met andere uitdrukkingen. Deze expressie is bijvoorbeeld toegestaan:
    - `ratings/any(r: r ne 5)`

    maar deze uitdrukking is niet:
    - `ratings/any(r: r ne 5 and r gt 2)`

    en hoewel deze expressie is toegestaan, is deze niet handig omdat de omstandigheden elkaar overlappen:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Eenvoudige vergelijkingsuitdrukkingen `eq` `lt`met `le` `gt`, `ge` , , `and` / `or`of kunnen worden gecombineerd met . Bijvoorbeeld:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Vergelijkingsuitdrukkingen `and` in combinatie met (voegvoegingen) kunnen verder worden gecombineerd met behulp van `or`. Deze vorm staat bekend in booleaanse logica als "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Bijvoorbeeld:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regels `all`voor :
  - Eenvoudige gelijkheidsuitdrukkingen kunnen niet nuttig worden gecombineerd met andere expressies. Deze expressie is bijvoorbeeld toegestaan:
    - `ratings/all(r: r eq 5)`

    maar deze uitdrukking is niet:
    - `ratings/all(r: r eq 5 or r le 2)`

    en hoewel deze expressie is toegestaan, is deze niet handig omdat de omstandigheden elkaar overlappen:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Eenvoudige vergelijkingsuitdrukkingen `ne` `lt`met `le` `gt`, `ge` , , `and` / `or`of kunnen worden gecombineerd met . Bijvoorbeeld:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Vergelijkingsuitdrukkingen `or` in combinatie met (disjunctions) kunnen verder worden gecombineerd met behulp van `and`. Deze vorm staat in booleaanse logica bekend als "[Conjunctive Normal Form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Bijvoorbeeld:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regels voor het filteren van complexe verzamelingen

Lambda uitdrukkingen over complexe collecties ondersteunen een veel flexibelersyntaxis dan lambda uitdrukkingen over collecties van primitieve types. U elke filterconstructie in zo'n lambda-expressie gebruiken die u buiten één gebruiken, met slechts twee uitzonderingen.

Ten eerste, `search.ismatch` `search.ismatchscoring` de functies en worden niet ondersteund in lambda uitdrukkingen. Zie [OData-verzamelingsfilters in Azure Cognitive Search voor](search-query-understand-collection-filters.md)meer informatie.

Ten tweede is het niet toegestaan om te verwijzen naar velden die niet gebonden zijn *aan* de bereikvariabele (zogenaamde *vrije variabelen).* Denk bijvoorbeeld aan de volgende twee gelijkwaardige OData-filterexpressies:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

De eerste expressie is toegestaan, terwijl het tweede `details/margin` formulier wordt geweigerd omdat `s`het niet gebonden is aan de bereikvariabele .

Deze regel strekt zich ook uit tot expressies met variabelen die in een buitenbereik zijn gebonden. Dergelijke variabelen zijn vrij met betrekking tot de reikwijdte waarin zij voorkomen. De eerste expressie is bijvoorbeeld toegestaan, terwijl de tweede equivalente `s/name` expressie niet is toegestaan omdat `a`deze vrij is met betrekking tot de bereikvariabele:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Deze beperking zou in de praktijk geen probleem moeten zijn, omdat het altijd mogelijk is om filters zodanig te construeren dat lambda-expressies alleen gebonden variabelen bevatten.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Spiekbriefje voor regels voor verzamelingsfilters

In de volgende tabel worden de regels voor het maken van geldige filters voor elk gegevenstype verzameling samengevat.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Zie Hoe u [geldige verzamelingsfilters](#bkmk_examples)wilt schrijven voor voorbeelden van het maken van geldige filters voor elk geval.

Als u vaak filters schrijft en de regels van de eerste beginselen begrijpt, u meer dan alleen het onthouden ervan, zie [OData-verzamelingsfilters begrijpen in Azure Cognitive Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Volgende stappen  

- [Informatie over OData-verzamelingsfilters in Azure Cognitive Search](search-query-understand-collection-filters.md)
- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
