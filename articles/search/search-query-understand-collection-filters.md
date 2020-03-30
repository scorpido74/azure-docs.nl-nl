---
title: Informatie over OData-verzamelingsfilters
titleSuffix: Azure Cognitive Search
description: Lees de mechanica van hoe OData-verzamelingsfilters werken in Azure Cognitive Search-query's, inclusief beperkingen en gedragingen die uniek zijn voor verzamelingen.
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
ms.openlocfilehash: f6e8ed5baef9b8594bb1fe03942e831fd8264a56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113065"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Informatie over OData-verzamelingsfilters in Azure Cognitive Search

Als u [wilt filteren](query-odata-filter-orderby-syntax.md) op verzamelingsvelden in Azure Cognitive Search, u de [ `any` operatoren en `all` operatoren](search-query-odata-collection-operators.md) gebruiken samen met **lambda-expressies.** Lambda-expressies zijn Booleaanse uitdrukkingen die verwijzen naar een **bereikvariabele.** De `any` `all` en exploitanten zijn `for` analoog aan een lus in de meeste programmeertalen, met het bereik variabele waarbij de rol van lus variabele, en de lambda expressie als het lichaam van de lus. De bereikvariabele neemt de "huidige" waarde van de verzameling over tijdens de iteratie van de lus.

Tenminste dat is hoe het conceptueel werkt. In werkelijkheid implementeert Azure Cognitive Search filters op `for` een heel andere manier dan hoe lussen werken. Idealiter zou dit verschil onzichtbaar zijn voor u, maar in bepaalde situaties is het niet. Het eindresultaat is dat er regels zijn die je moet volgen bij het schrijven van lambda-uitdrukkingen.

In dit artikel wordt uitgelegd waarom de regels voor verzamelingsfilters bestaan door te onderzoeken hoe Azure Cognitive Search deze filters uitvoert. Als u geavanceerde filters met complexe lambdauitdrukkingen schrijft, u dit artikel nuttig vinden in het opbouwen van uw begrip van wat er mogelijk is in filters en waarom.

Zie [OData-verzamelingsfilters oplossen in Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)voor informatie over wat de regels voor verzamelingsfilters zijn, inclusief voorbeelden.

## <a name="why-collection-filters-are-limited"></a>Waarom verzamelingsfilters beperkt zijn

Er zijn drie onderliggende redenen waarom niet alle filterfuncties worden ondersteund voor alle soorten verzamelingen:

1. Alleen bepaalde operatoren worden ondersteund voor bepaalde gegevenstypen. Het heeft bijvoorbeeld geen zin om de Booleaanse waarden te vergelijken en `true` `false` de Booleaanse waarden te gebruiken `lt` `gt`en enzovoort.
1. Azure Cognitive Search biedt geen ondersteuning voor `Collection(Edm.ComplexType)` **gecorreleerde zoekopdrachten** op velden van het type .
1. Azure Cognitive Search gebruikt omgekeerde indexen om filters uit te voeren voor alle soorten gegevens, inclusief verzamelingen.

De eerste reden is slechts een gevolg van de manier waarop de OData taal en EDM type systeem worden gedefinieerd. De laatste twee worden in de rest van dit artikel nader toegelicht.

## <a name="correlated-versus-uncorrelated-search"></a>Gecorreleerd versus niet-gecorreleerd zoeken

Bij het toepassen van meerdere filtercriteria op een verzameling complexe objecten worden de criteria **gecorreleerd** omdat ze van toepassing zijn op *elk object in de verzameling.* Het volgende filter retourneert bijvoorbeeld hotels met ten minste één deluxe kamer met een tarief van minder dan 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Als filteren *niet gecorreleerd*was, kan het bovenstaande filter hotels terugkeren waar een kamer deluxe is en een andere kamer een basistarief van minder dan 100 heeft. Dat zou niet zinvol zijn, omdat beide clausules van de lambda uitdrukking van toepassing zijn op dezelfde bereik variabele, namelijk `room`. Dit is de reden waarom dergelijke filters zijn gecorreleerd.

Voor zoeken in volledige tekst is er echter geen manier om naar een specifieke bereikvariabele te verwijzen. Als u zoeken in velden gebruikt om een [volledige Lucene-query](query-lucene-syntax.md) als deze uit te geven:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

u hotels terug waar één kamer deluxe is, en een andere kamer noemt city view in de beschrijving. Het onderstaande document `Id` met `1` of komt bijvoorbeeld overeen met de query:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

De reden `Rooms/Type` is dat verwijst naar alle `Rooms/Type` geanalyseerde termen van het `Rooms/Description`veld in het hele document, en op dezelfde manier voor , zoals weergegeven in de onderstaande tabellen.

Hoe `Rooms/Type` wordt opgeslagen voor full-text zoeken:

| Term in`Rooms/Type` | Document-iD's |
| --- | --- |
| Deluxe | 1, 2 |
| Standaard | 1 |

Hoe `Rooms/Description` wordt opgeslagen voor full-text zoeken:

| Term in`Rooms/Description` | Document-iD's |
| --- | --- |
| Binnenplaats | 2 |
| city | 1 |
| Tuin | 1 |
| Grote | 1 |
| Motel | 2 |
| ruimte | 1, 2 |
| Standaard | 1 |
| Suite | 1 |
| weergave | 1 |

Dus in tegenstelling tot het filter hierboven, die `Type` in principe zegt "overeenkomen met `BaseRate` documenten waar een kamer gelijk is aan `Rooms/Type` 'Deluxe Room' en `Rooms/Description` **diezelfde kamer** heeft minder dan 100", de zoekopdracht zegt "overeenkomen met documenten waar heeft de term "deluxe" en heeft de zinsnede "city view". Er is geen concept van individuele kamers waarvan de velden kunnen worden gecorreleerd in het laatste geval.

> [!NOTE]
> Als u ondersteuning voor gecorreleerde zoekopdrachten wilt zien die zijn toegevoegd aan Azure Cognitive Search, stem dan voor [dit user voice-item.](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections)

## <a name="inverted-indexes-and-collections"></a>Omgekeerde indexen en verzamelingen

Je hebt misschien gemerkt dat er veel minder beperkingen op lambda uitdrukkingen `Collection(Edm.Int32)` `Collection(Edm.GeographyPoint)`over complexe collecties dan er zijn voor eenvoudige collecties zoals , , en ga zo maar door. Dit komt omdat Azure Cognitive Search complexe verzamelingen opslaat als werkelijke verzamelingen van subdocumenten, terwijl eenvoudige verzamelingen helemaal niet als verzamelingen worden opgeslagen.

Denk bijvoorbeeld aan een filterbaar `seasons` tekenreeksverzamelingsveld zoals in een index voor een online winkel. Sommige documenten die naar deze index zijn geüpload, zien er mogelijk als volgt uit:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

De waarden `seasons` van het veld worden opgeslagen in een structuur die een **omgekeerde index**wordt genoemd, die er ongeveer als volgt uitziet:

| Termijn | Document-iD's |
| --- | --- |
| Voorjaar | 1, 2 |
| Zomer | 1 |
| Vallen | 1, 2 |
| Winter | 2, 3 |

Deze gegevensstructuur is ontworpen om één vraag met grote snelheid te beantwoorden: in welke documenten verschijnt een bepaalde term? Het beantwoorden van deze vraag werkt meer als een duidelijke gelijkheid strekken dan een lus over een collectie. In feite is dit de reden waarom Azure `eq` Cognitive Search voor tekenreeksverzamelingen `any`alleen toestaat als vergelijkingsoperator in een lambda-expressie voor .

Voortbouwend op gelijkheid, zullen we vervolgens kijken hoe het mogelijk is om meerdere `or`gelijkheidscontroles op dezelfde bereikvariabele te combineren met . Het werkt dankzij algebra en [de distributieve eigenschap van kwantificeerders.](https://en.wikipedia.org/wiki/Existential_quantification#Negation) Deze uitdrukking:

    seasons/any(s: s eq 'winter' or s eq 'fall')

is gelijk aan:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

en elk van `any` de twee subexpressies kan efficiënt worden uitgevoerd met behulp van de omgekeerde index. Ook, dankzij [de negatie wet van kwantificeerders](https://en.wikipedia.org/wiki/Existential_quantification#Negation), deze uitdrukking:

    seasons/all(s: s ne 'winter' and s ne 'fall')

is gelijk aan:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

daarom is het mogelijk om `all` `ne` te `and`gebruiken met en.

> [!NOTE]
> Hoewel de details buiten het bereik van dit document vallen, reiken deze dezelfde principes ook uit tot [afstands- en snijtests voor verzamelingen van geo-ruimtelijke punten.](search-query-odata-geo-spatial-functions.md) Dit is de `any`reden waarom, in:
>
> - `geo.intersects`kan niet worden ontkend
> - `geo.distance`moeten worden `lt` vergeleken met of`le`
> - uitdrukkingen moeten worden `or`gecombineerd met , niet`and`
>
> De omgekeerde regels `all`gelden voor .

Een grotere verscheidenheid aan expressies is toegestaan bij het `lt`filteren `gt` `le`op `ge` verzamelingen van `Collection(Edm.Int32)` gegevenstypen die de , , en operatoren ondersteunen, zoals bijvoorbeeld. In het bijzonder `and` `or` u `any`zowel gebruik maken als in, zolang `and`de onderliggende **vergelijkingsexpressies** worden gecombineerd in bereikvergelijkingen met behulp van, die vervolgens verder worden gecombineerd met behulp van `or`. Deze structuur van Booleaanse uitdrukkingen wordt [Disjunctive Normal Form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form)genoemd, ook wel bekend als "ORs of ANDs". Daarentegen moeten lambda-expressies voor `all` deze gegevenstypen in [conjunctief normale vorm (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form)zijn , ook wel bekend als "AND's van ORs". Azure Cognitive Search maakt dergelijke bereikvergelijkingen mogelijk omdat het ze efficiënt kan uitvoeren met omgekeerde indexen, net zoals het snel opzoeknaar tekenreeksen kan doen.

Samengevat, hier zijn de vuistregels voor wat is toegestaan in een lambda uitdrukking:

- Binnen `any`, *positieve controles* zijn altijd toegestaan, zoals `geo.intersects`gelijkheid, bereik vergelijkingen, , of `geo.distance` vergeleken met `lt` of `le` (denk aan "nabijheid" als gelijkheid als het gaat om het controleren van afstand).
- Binnen `any` `or` , is altijd toegestaan. U kunt `and` alleen gebruiken voor gegevenstypen die bereikcontroles kunnen uitdrukken, en alleen als u OK's van AND's (DNF) gebruikt.
- Binnen `all`, de regels worden omgekeerd - alleen *negatieve* controles `and` zijn toegestaan, `or` kunt u altijd gebruiken, en u alleen gebruiken voor bereik controles uitgedrukt als ANDs van ORs (CNF).

In de praktijk zijn dit de typen filters die u het meest waarschijnlijk toch zult gebruiken. Het is nog steeds nuttig om de grenzen van wat mogelijk is wel te begrijpen.

Zie [Hoe u geldige verzamelingsfilters](search-query-troubleshoot-collection-filters.md#bkmk_examples)schrijven voor specifieke voorbeelden van welke filters zijn toegestaan en welke niet.

## <a name="next-steps"></a>Volgende stappen  

- [Problemen met OData-verzamelingsfilters oplossen in Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)
- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
