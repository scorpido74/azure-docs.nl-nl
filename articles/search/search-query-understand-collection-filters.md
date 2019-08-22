---
title: Meer informatie over OData-verzamelings filters-Azure Search
description: Meer informatie over hoe OData-verzamelings filters werken in Azure Search query's.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 5c3a0205f5a9ac5115e78f1bc11f70b2c50a9714
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647415"
---
# <a name="understanding-odata-collection-filters-in-azure-search"></a>Meer informatie over OData-verzamelings filters in Azure Search

Als u wilt [filteren](query-odata-filter-orderby-syntax.md) op verzamelings velden in azure Search, kunt u de [ `any` Opera tors en `all` ](search-query-odata-collection-operators.md) gebruiken in combi natie met **lambda-expressies**. Lambda-expressies zijn Booleaanse expressies die verwijzen naar een **bereik variabele**. De `any` Opera `all` tors en zijn vergelijkbaar met `for` een lus in de meeste programmeer talen, waarbij de variabele Range de rol van de lus-variabele maakt en de lambda-expressie als hoofd tekst van de lus. De bereik variabele neemt de ' huidige ' waarde van de verzameling tijdens de herhaling van de lus.

Ten minste de manier waarop het concept werkt. Azure Search implementeert in werkelijkheid filters op een zeer andere manier voor het `for` werken met lussen. In het ideale geval is dit verschil onzichtbaar voor u, maar in bepaalde situaties niet. Het eind resultaat is dat er regels zijn die u moet volgen bij het schrijven van lambda-expressies.

In dit artikel wordt uitgelegd waarom de regels voor verzamelings filters bestaan door te verkennen hoe Azure Search deze filters uitvoert. Als u geavanceerde filters met samengestelde lambda-expressies schrijft, kunt u dit artikel nuttig vinden bij het bouwen van uw kennis van wat mogelijk is in filters en waarom.

Zie [problemen met OData-verzamelings filters in azure Search](search-query-troubleshoot-collection-filters.md)voor meer informatie over de regels voor verzamelings filters, inclusief voor beelden.

## <a name="why-collection-filters-are-limited"></a>Waarom verzamelings filters beperkt zijn

Er zijn drie onderliggende redenen waarom niet alle filter functies worden ondersteund voor alle typen verzamelingen:

1. Voor bepaalde gegevens typen worden alleen bepaalde Opera tors ondersteund. Het is bijvoorbeeld niet handig `true` om de Booleaanse waarden te vergelijken en `false` te `gt`gebruiken `lt`.
1. Azure Search biedt geen ondersteuning voor gecorreleerde **Zoek opdrachten** voor velden van het type `Collection(Edm.ComplexType)`.
1. Azure Search maakt gebruik van omgekeerde indexen om filters uit te voeren voor alle typen gegevens, inclusief verzamelingen.

De eerste reden is slechts een gevolg van de manier waarop de OData-taal en het EDM type systeem worden gedefinieerd. De laatste twee worden uitgebreid beschreven in de rest van dit artikel.

## <a name="correlated-versus-uncorrelated-search"></a>Gecorreleerde versus niet-gerelateerde zoek opdracht

Wanneer u meerdere filter criteria toepast op een verzameling complexe objecten, worden de criteria gecorreleerd, omdat ze van toepassing zijn op *elk object in de verzameling*. Het volgende filter retourneert bijvoorbeeld Hotels met ten minste één Deluxe-kamer met een snelheid van minder dan 100:

    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)

Als filteren nietis gecorreleerd, kan het bovenstaande filter bijvoorbeeld Hotels retour neren waarbij één kamer de waarde Deluxe heeft en een andere kamer een basis frequentie heeft van minder dan 100. Dat zou niet zinvol zijn, omdat beide componenten van de lambda-expressie van toepassing zijn op dezelfde bereik variabele `room`, namelijk. Daarom worden deze filters gecorreleerd.

Voor zoeken in volledige tekst is er echter geen manier om naar een specifieke bereik variabele te verwijzen. Als u zoeken met een zoek opdracht gebruikt om een [volledige lucene-query](query-lucene-syntax.md) uit te geven, zoals deze:

    Rooms/Type:deluxe AND Rooms/Description:"city view"

u kunt Hotels weer geven als er één kamer is en een andere kamer vermeldt "stads weergave" in de beschrijving. Het onderstaande document `1` kan bijvoorbeeld overeenkomen `Id` met de query:

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

De reden hiervoor is `Rooms/Type` dat naar alle geanalyseerde voor waarden van het `Rooms/Type` veld in het hele `Rooms/Description`document wordt verwezen, zoals wordt weer gegeven in de onderstaande tabellen.

Hoe `Rooms/Type` wordt opgeslagen voor zoeken in volledige tekst:

| Term in`Rooms/Type` | Document-Id's |
| --- | --- |
| PhotoDeluxe | 1, 2 |
| standard | 1 |

Hoe `Rooms/Description` wordt opgeslagen voor zoeken in volledige tekst:

| Term in`Rooms/Description` | Document-Id's |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| Garden | 1 |
| groot | 1 |
| Motel | 2 |
| daarheen | 1, 2 |
| standard | 1 |
| serie | 1 |
| weergeven | 1 |

In tegens telling tot het bovenstaande filter, waarin in principe wordt aangegeven dat "documenten `Type` met een kamer gelijk is aan" Deluxe room "en `BaseRate` **die dezelfde ruimte** heeft minder dan 100" bevat de zoek query " `Rooms/Type` overeenkomt met documenten waarbij de term" Deluxe "en `Rooms/Description` heeft de zin" City View ". Er is geen concept van afzonderlijke ruimtes waarvan de velden in het laatste geval kunnen worden gecorreleerd.

> [!NOTE]
> Als u ondersteuning wilt zien voor gecorreleerde Zoek opdrachten die zijn toegevoegd aan Azure Search, moet u stemmen voor [dit gebruikers spraak item](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Omgekeerde indexen en verzamelingen

U hebt wellicht gezien dat er veel minder beperkingen zijn voor lambda-expressies over complexe verzamelingen dan voor eenvoudige verzamelingen `Collection(Edm.Int32)`, `Collection(Edm.GeographyPoint)`enzovoort. Dit komt doordat Azure Search complexe verzamelingen opslaat als echte verzamelingen van subdocumenten, terwijl eenvoudige verzamelingen niet worden opgeslagen als verzamelingen.

Denk bijvoorbeeld aan een veld voor een filter bare teken reeks `seasons` verzameling, zoals in een index voor een online winkel. Sommige documenten die naar deze index worden geüpload, kunnen er als volgt uitzien:

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

De waarden van het `seasons` veld worden opgeslagen in een structuur, een **omgekeerde index**, die er ongeveer als volgt uitziet:

| Begrip | Document-Id's |
| --- | --- |
| lente | 1, 2 |
| zomer | 1 |
| Meld | 1, 2 |
| winter | 2, 3 |

Deze gegevens structuur is ontworpen om één vraag te beantwoorden met geweldige snelheid: In welke documenten wordt een bepaalde term weer gegeven? Het beantwoorden van deze vraag werkt meer als een normale gelijkheids controle dan een lus voor een verzameling. Daarom is dit de reden waarom voor teken reeks verzamelingen, Azure Search alleen `eq` een vergelijkings operator in een lambda- `any`expressie kan hebben voor.

Op basis van gelijkheid kunt u vervolgens kijken hoe u meerdere gelijkheids controles kunt combi neren voor dezelfde bereik variabele met `or`. Het werkt met algebra en [de distributieve eigenschap van Kwant oren](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Deze expressie:

    seasons/any(s: s eq 'winter' or s eq 'fall')

is gelijk aan:

    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')

en elk van de twee `any` Subexpressies kunnen efficiënt worden uitgevoerd met behulp van de omgekeerde index. En dankzij [het negatie recht van](https://en.wikipedia.org/wiki/Existential_quantification#Negation)Kwant, deze expressie:

    seasons/all(s: s ne 'winter' and s ne 'fall')

is gelijk aan:

    not seasons/any(s: s eq 'winter' or s eq 'fall')

Daarom is het mogelijk om met `all` `ne` en `and`te gebruiken.

> [!NOTE]
> Hoewel de details zich buiten het bereik van dit document bevinden, zijn dezelfde principes ook van toepassing op [afstanden en intersectie tests voor verzamelingen van georuimtelijke punten](search-query-odata-geo-spatial-functions.md) . Daarom doet u het volgende `any`in:
>
> - `geo.intersects`kan niet worden genegeerd
> - `geo.distance`moet worden vergeleken met `lt` of`le`
> - expressies moeten worden gecombineerd met `or`, en niet`and`
>
> De omgekeerde regels zijn van `all`toepassing op.

Een groter aantal verschillende expressies is toegestaan bij het filteren op verzamelingen gegevens typen die ondersteuning bieden `lt` `Collection(Edm.Int32)` voor `gt`de Opera tors `ge` ,, `le`en, zoals bijvoorbeeld. U kunt `and` met name `or` ook gebruiken in `any`, zolang de onderliggende vergelijkings expressies worden gecombineerd in **bereik vergelijkingen** met `and`, die vervolgens verder worden gecombineerd met `or`. Deze structuur van Boole-expressies heet [disjunctive Normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), ook wel bekend als ' or of and '. Lambda-expressies voor `all` deze gegevens typen moeten daarentegen in [Conjunctive normaal vorm (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), ook wel bekend als ' and van or ' zijn. Met Azure Search kunnen dergelijke bereik vergelijkingen worden uitgevoerd, omdat deze met behulp van omgedraaide indexen efficiënt kan uitvoeren, net zoals een snelle term zoekopdracht voor teken reeksen.

In samen vatting vindt u de vuist regels voor wat is toegestaan in een lambda-expressie:

- In `any`zijn *positieve controles* altijd toegestaan, zoals `geo.intersects`gelijkheid, bereik vergelijkingen, of `geo.distance` vergeleken met `lt` of `le` (' closed ' als een vergelijk bare waarde wanneer het is te controleren afstand).
- In `any`isaltijdtoegestaan `or` . U kunt alleen `and` voor gegevens typen gebruiken waarmee u het bereik kunt controleren, en alleen als u or van and (DNF) gebruikt.
- In `all`worden de regels omgekeerd: alleen *negatieve controles* zijn toegestaan. u kunt altijd gebruiken `and` en u kunt deze alleen gebruiken `or` voor bereik controles, uitgedrukt als and of or (CNF).

In de praktijk zijn dit de typen filters die u waarschijnlijk toch kunt gebruiken. Het is nog steeds nuttig om inzicht te krijgen in de grenzen van wat mogelijk is.

Zie een [geldige verzameling filters schrijven](search-query-troubleshoot-collection-filters.md#bkmk_examples)voor specifieke voor beelden van welke soorten filters zijn toegestaan en welke niet.

## <a name="next-steps"></a>Volgende stappen  

- [Problemen met OData-verzamelings filters in Azure Search oplossen](search-query-troubleshoot-collection-filters.md)
- [Filters in Azure Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Search](query-odata-filter-orderby-syntax.md)
- [Verwijzing naar de syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md)
- [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
