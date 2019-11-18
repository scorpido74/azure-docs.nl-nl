---
title: Meer informatie over OData-verzamelings filters
titleSuffix: Azure Cognitive Search
description: Meer informatie over de mechanismen van hoe OData-verzamelings filters werken in azure Cognitive Search query's, inclusief beperkingen en gedragingen die uniek zijn voor verzamelingen.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113065"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Meer informatie over OData-verzamelings filters in azure Cognitive Search

Als u wilt [filteren](query-odata-filter-orderby-syntax.md) op verzamelings velden in azure Cognitive Search, kunt u de [Opera tors`any` en `all`](search-query-odata-collection-operators.md) gebruiken in combi natie met **lambda-expressies**. Lambda-expressies zijn Booleaanse expressies die verwijzen naar een **bereik variabele**. De Opera tors `any` en `all` zijn vergelijkbaar met een `for`-lus in de meeste programmeer talen, waarbij de bereik variabele de rol van lus-variabele maakt en de lambda-expressie als hoofd tekst van de lus. De bereik variabele neemt de ' huidige ' waarde van de verzameling tijdens de herhaling van de lus.

Ten minste de manier waarop het concept werkt. In werkelijkheid implementeert Azure Cognitive Search filters op een zeer andere manier om te bepalen hoe `for` lussen werkt. In het ideale geval is dit verschil onzichtbaar voor u, maar in bepaalde situaties niet. Het eind resultaat is dat er regels zijn die u moet volgen bij het schrijven van lambda-expressies.

In dit artikel wordt uitgelegd waarom de regels voor verzamelings filters bestaan door te ontdekken hoe Azure Cognitive Search deze filters uitvoert. Als u geavanceerde filters met samengestelde lambda-expressies schrijft, kunt u dit artikel nuttig vinden bij het bouwen van uw kennis van wat mogelijk is in filters en waarom.

Zie [problemen met OData-verzamelings filters in Azure Cognitive Search oplossen](search-query-troubleshoot-collection-filters.md)voor meer informatie over de regels voor verzamelings filters, met inbegrip van voor beelden.

## <a name="why-collection-filters-are-limited"></a>Waarom verzamelings filters beperkt zijn

Er zijn drie onderliggende redenen waarom niet alle filter functies worden ondersteund voor alle typen verzamelingen:

1. Voor bepaalde gegevens typen worden alleen bepaalde Opera tors ondersteund. Het is bijvoorbeeld niet zinvol om de Booleaanse waarden te vergelijken `true` en `false` met behulp van `lt`, `gt`, enzovoort.
1. Azure Cognitive Search biedt geen ondersteuning voor **gecorreleerde Zoek opdrachten** voor velden van het type `Collection(Edm.ComplexType)`.
1. Azure Cognitive Search maakt gebruik van omgekeerde indexen om filters uit te voeren voor alle typen gegevens, inclusief verzamelingen.

De eerste reden is slechts een gevolg van de manier waarop de OData-taal en het EDM type systeem worden gedefinieerd. De laatste twee worden uitgebreid beschreven in de rest van dit artikel.

## <a name="correlated-versus-uncorrelated-search"></a>Gecorreleerde versus niet-gerelateerde zoek opdracht

Wanneer u meerdere filter criteria toepast op een verzameling complexe objecten, worden de criteria **gecorreleerd** , omdat ze van toepassing zijn op *elk object in de verzameling*. Het volgende filter retourneert bijvoorbeeld Hotels met ten minste één Deluxe-kamer met een snelheid van minder dan 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Als filteren niet is *gecorreleerd*, kan het bovenstaande filter bijvoorbeeld Hotels retour neren waarbij één kamer de waarde Deluxe heeft en een andere kamer een basis frequentie heeft van minder dan 100. Dat zou niet logisch kunnen zijn, omdat beide componenten van de lambda-expressie van toepassing zijn op dezelfde bereik variabele, namelijk `room`. Daarom worden deze filters gecorreleerd.

Voor zoeken in volledige tekst is er echter geen manier om naar een specifieke bereik variabele te verwijzen. Als u zoeken met een zoek opdracht gebruikt om een [volledige lucene-query](query-lucene-syntax.md) uit te geven, zoals deze:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

u kunt Hotels weer geven als er één kamer is en een andere kamer vermeldt "stads weergave" in de beschrijving. Het onderstaande document met `Id` van `1` zou bijvoorbeeld overeenkomen met de query:

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

De reden hiervoor is dat `Rooms/Type` naar alle geanalyseerde voor waarden van het veld `Rooms/Type` in het hele document verwijst, op dezelfde manier als `Rooms/Description`, zoals wordt weer gegeven in de onderstaande tabellen.

Hoe `Rooms/Type` worden opgeslagen voor Zoek opdrachten in volledige tekst:

| Term in `Rooms/Type` | Document-Id's |
| --- | --- |
| PhotoDeluxe | 1, 2 |
| standaard | 1 |

Hoe `Rooms/Description` worden opgeslagen voor Zoek opdrachten in volledige tekst:

| Term in `Rooms/Description` | Document-Id's |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| Garden | 1 |
| lange | 1 |
| Motel | 2 |
| daarheen | 1, 2 |
| standaard | 1 |
| serie | 1 |
| weergeven | 1 |

In tegens telling tot het bovenstaande filter, waarin in principe wordt aangegeven dat een kamer `Type` gelijk is aan ' Deluxe room ' en **dat dezelfde ruimte** `BaseRate` minder dan 100 ' is, wordt in de zoek query ' overeenkomen met documenten waar `Rooms/Type` de term ' Deluxe ' heeft en `Rooms/Description` de zin ' City View ' heeft. Er is geen concept van afzonderlijke ruimtes waarvan de velden in het laatste geval kunnen worden gecorreleerd.

> [!NOTE]
> Als u ondersteuning wilt zien voor gecorreleerde Zoek opdrachten die zijn toegevoegd aan Azure Cognitive Search, kunt u stemmen voor [dit gebruikers spraak item](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Omgekeerde indexen en verzamelingen

U hebt wellicht gezien dat er veel minder beperkingen zijn voor lambda-expressies over complexe verzamelingen dan voor eenvoudige verzamelingen, zoals `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`, enzovoort. Dit komt doordat Azure-Cognitive Search complexe verzamelingen opslaat als echte verzamelingen van subdocumenten, terwijl eenvoudige verzamelingen niet worden opgeslagen als verzamelingen.

Denk bijvoorbeeld aan een veld voor een filter bare teken reeks verzameling als `seasons` in een index voor een online winkel. Sommige documenten die naar deze index worden geüpload, kunnen er als volgt uitzien:

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

De waarden van het veld `seasons` worden opgeslagen in een structuur, een **omgekeerde index**genoemd, die er ongeveer als volgt uitziet:

| Termijn | Document-Id's |
| --- | --- |
| lente | 1, 2 |
| zomer | 1 |
| Meld | 1, 2 |
| winter | 2, 3 |

Deze gegevens structuur is ontworpen om één vraag te beantwoorden met een fantastische snelheid: in welke documenten wordt een bepaalde term weer gegeven? Het beantwoorden van deze vraag werkt meer als een normale gelijkheids controle dan een lus voor een verzameling. In feite is dit de reden waarom voor teken reeks verzamelingen, Azure Cognitive Search alleen `eq` als vergelijkings operator in een lambda-expressie voor `any`toestaat.

Op basis van gelijkheid kunt u vervolgens kijken hoe u meerdere gelijkheids controles kunt combi neren voor dezelfde bereik variabele met `or`. Het werkt met algebra en [de distributieve eigenschap van Kwant oren](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Deze expressie:

    seasons/any(s: s eq 'winter' or s eq 'fall')

is gelijk aan:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

en elk van de twee `any` subexpressers kunnen efficiënt worden uitgevoerd met behulp van de omgekeerde index. En dankzij [het negatie recht van Kwant](https://en.wikipedia.org/wiki/Existential_quantification#Negation), deze expressie:

    seasons/all(s: s ne 'winter' and s ne 'fall')

is gelijk aan:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Daarom is het mogelijk om `all` te gebruiken met `ne` en `and`.

> [!NOTE]
> Hoewel de details zich buiten het bereik van dit document bevinden, zijn dezelfde principes ook van toepassing op [afstanden en intersectie tests voor verzamelingen van georuimtelijke punten](search-query-odata-geo-spatial-functions.md) . Daarom moet u in `any`:
>
> - `geo.intersects` kan niet worden geannuleerd
> - `geo.distance` moet worden vergeleken met `lt` of `le`
> - expressies moeten worden gecombineerd met `or`, niet `and`
>
> De omgekeerde regels zijn van toepassing op `all`.

Een groter aantal verschillende expressies is toegestaan bij het filteren op verzamelingen gegevens typen die ondersteuning bieden voor de Opera tors `lt`, `gt`, `le`en `ge`, zoals `Collection(Edm.Int32)`. U kunt met name `and` gebruiken, maar ook `or` in `any`, zolang de onderliggende vergelijkings expressies worden gecombineerd in **bereik vergelijkingen** met `and`, die vervolgens verder worden gecombineerd met behulp van `or`. Deze structuur van Boole-expressies heet [disjunctive Normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), ook wel bekend als ' or of and '. Lambda-expressies voor `all` voor deze gegevens typen moeten daarentegen in [Conjunctive normaal vorm (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), ook wel bekend als ' and van or ' zijn. Met Azure Cognitive Search kunnen dergelijke bereik vergelijkingen worden uitgevoerd, omdat deze met omkeer bare indexen op een efficiënte manier kan worden gebruikt, net zoals bij het zoeken naar teken reeksen.

In samen vatting vindt u de vuist regels voor wat is toegestaan in een lambda-expressie:

- Binnen `any`zijn *positieve controles* altijd toegestaan, zoals gelijkheid, bereik vergelijkingen, `geo.intersects`of `geo.distance` vergeleken met `lt` of `le` (' dichtheid ' als gelijkheid van het controleren van de afstand).
- In `any`is `or` altijd toegestaan. U kunt alleen `and` gebruiken voor gegevens typen die het bereik van de controle kunnen uitdrukken, en alleen als u or van and (DNF) gebruikt.
- In `all`worden de regels omgekeerd: alleen *negatieve controles* zijn toegestaan. u kunt `and` altijd gebruiken en u kunt `or` alleen gebruiken voor bereik controles die worden weer gegeven als and van or (CNF).

In de praktijk zijn dit de typen filters die u waarschijnlijk toch kunt gebruiken. Het is nog steeds nuttig om inzicht te krijgen in de grenzen van wat mogelijk is.

Zie een [geldige verzameling filters schrijven](search-query-troubleshoot-collection-filters.md#bkmk_examples)voor specifieke voor beelden van welke soorten filters zijn toegestaan en welke niet.

## <a name="next-steps"></a>Volgende stappen  

- [Problemen met OData-verzamelings filters in azure Cognitive Search oplossen](search-query-troubleshoot-collection-filters.md)
- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
