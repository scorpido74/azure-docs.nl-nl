---
title: Azure Cosmos DB-indexeringsbeleid
description: Meer informatie over het configureren en wijzigen van het standaardindexeringsbeleid voor automatische indexering en betere prestaties in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: tisande
ms.openlocfilehash: 930f156ebec76be860e7af02d41540ce67982f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292069"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexeringsbeleid in Azure Cosmos DB

In Azure Cosmos DB heeft elke container een indexeringsbeleid dat bepaalt hoe de items van de container moeten worden geïndexeerd. Met het standaardindexeringsbeleid voor nieuw gemaakte containers worden elke eigenschap van elk item geïndexeerd, worden bereikindexen voor elke tekenreeks of elk getal afgedwongen en ruimtelijke indexen voor elk GeoJSON-object van het type Point. Hierdoor u hoge queryprestaties krijgen zonder vooraf na te denken over indexering en indexbeheer.

In sommige gevallen wilt u mogelijk dit automatische gedrag overschrijven, zodat het beter aansluit bij uw vereisten. U het indexeringsbeleid van een container aanpassen door de *indexeringsmodus*in te stellen en *eigenschappaden*op te nemen of uit te sluiten.

> [!NOTE]
> De methode voor het bijwerken van indexeringsbeleid dat in dit artikel wordt beschreven, is alleen van toepassing op de SQL (Core)-API van Azure Cosmos DB.

## <a name="indexing-mode"></a>Indexeringsmodus

Azure Cosmos DB ondersteunt twee indexeringsmodi:

- **Consistent:** de index wordt synchroon bijgewerkt terwijl u items maakt, bijwerkt of verwijdert. Dit betekent dat de consistentie van uw leesquery's de consistentie is [die is geconfigureerd voor het account.](consistency-levels.md)
- **Geen**: Indexering is uitgeschakeld op de container. Dit wordt vaak gebruikt wanneer een container wordt gebruikt als een pure key-value winkel zonder de noodzaak van secundaire indexen. Het kan ook worden gebruikt om de prestaties van bulkbewerkingen te verbeteren. Nadat de bulkbewerkingen zijn voltooid, kan de indexmodus worden ingesteld op Consistent en vervolgens worden gecontroleerd met behulp van [de IndexTransformationProgress](how-to-manage-indexing-policy.md#use-the-net-sdk-v2) totdat deze is voltooid.

> [!NOTE]
> Azure Cosmos DB ondersteunt ook een Lazy-indexeringsmodus. Lazy indexing voert updates uit voor de index op een veel lager prioriteitsniveau wanneer de engine geen ander werk doet. Dit kan resulteren in **inconsistente of onvolledige** queryresultaten. Als u van plan bent een Cosmos-container op te vragen, moet u geen luie indexering selecteren.

Standaard is het indexeringsbeleid `automatic`ingesteld op . Het wordt bereikt door `automatic` het instellen van `true`de eigenschap in het indexeringsbeleid op . Als u `true` deze eigenschap instelt, kan Azure CosmosDB documenten automatisch indexeren terwijl ze zijn geschreven.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Vastgoedpaden opnemen en uitsluiten

Een aangepast indexeringsbeleid kan eigenschapspaden opgeven die expliciet zijn opgenomen of uitgesloten van indexering. Door het aantal paden dat wordt geïndexeerd te optimaliseren, u de hoeveelheid opslagruimte die door uw container wordt gebruikt, verlagen en de latentie van schrijfbewerkingen verbeteren. Deze paden worden gedefinieerd volgens [de methode die wordt beschreven in de sectie indexeringsoverzicht](index-overview.md#from-trees-to-property-paths) met de volgende toevoegingen:

- een pad dat leidt tot een scalaire waarde (tekenreeks of getal) eindigt met`/?`
- elementen uit een array worden `/[]` samen aangepakt via `/0` `/1` de notatie (in plaats van , enz.)
- de `/*` wildcard kan worden gebruikt om alle elementen onder het knooppunt te matchen

Neem hetzelfde voorbeeld opnieuw:

```
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 }
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

- het `headquarters`pad `employees` van de`/headquarters/employees/?`

- het `locations` `country` ' pad is`/locations/[]/country/?`

- het pad naar `headquarters` alles onder is`/headquarters/*`

We kunnen bijvoorbeeld het `/headquarters/employees/?` pad opnemen. Dit pad zou ervoor zorgen dat we de eigenschap van de werknemers indexeren, maar geen extra geneste JSON in deze eigenschap indexeren.

## <a name="includeexclude-strategy"></a>Strategie opnemen/uitsluiten

Elk indexeringsbeleid moet het `/*` hoofdpad opnemen als een opgenomen of een uitgesloten pad.

- Voeg het hoofdpad toe om paden selectief uit te sluiten die niet hoeven te worden geïndexeerd. Dit is de aanbevolen aanpak, omdat Azure Cosmos DB proactief elke nieuwe eigenschap kan indexeren die aan uw model kan worden toegevoegd.
- Sluit het hoofdpad uit om paden selectief op te nemen die moeten worden geïndexeerd.

- Voor paden met gewone tekens die: alfanumerieke tekens en _ (underscore) bevatten, hoeft u niet te ontsnappen aan de padtekenreeks rond dubbele aanhalingstekens (bijvoorbeeld "/pad/?"). Voor paden met andere speciale tekens moet u ontsnappen aan de padtekenreeks\"rond\"dubbele aanhalingstekens (bijvoorbeeld "/ pad-abc /?"). Als je speciale personages op je pad verwacht, kun je voor de veiligheid aan elk pad ontsnappen. Functioneel maakt het geen verschil als je aan elk pad ontsnapt vs alleen degenen die speciale personages hebben.

- De eigenschap `_etag` systeem is standaard uitgesloten van indexering, tenzij de etag wordt toegevoegd aan het opgenomen pad voor indexering.

- Als de indexeringsmodus is ingesteld op `id` `_ts` **consistent,** worden de systeemeigenschappen automatisch geïndexeerd.

Wanneer u paden opneem en uitsluit, u de volgende kenmerken tegenkomen:

- `kind`kan een `range` `hash`van beide zijn of . De functionaliteit van de bereikindex biedt alle functionaliteit van een hash-index, dus we raden u aan een bereikindex te gebruiken.

- `precision`is een getal dat is gedefinieerd op indexniveau voor opgenomen paden. Een waarde `-1` van geeft maximale precisie aan. We raden u aan `-1`deze waarde altijd in te stellen op .

- `dataType`kan een `String` `Number`van beide zijn of . Dit geeft de typen JSON-eigenschappen aan die worden geïndexeerd.

Wanneer deze eigenschappen niet zijn opgegeven, hebben deze eigenschappen de volgende standaardwaarden:

| **Naam van eigenschap**     | **Standaardwaarde** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` en `Number` |

Zie [dit gedeelte](how-to-manage-indexing-policy.md#indexing-policy-examples) voor het indexeren van beleidsvoorbeelden voor het opnemen en uitsluiten van paden.

## <a name="spatial-indexes"></a>Ruimtelijke indexen

Wanneer u een ruimtelijk pad in het indexeringsbeleid ```type``` definieert, moet u bepalen welke index op dat pad moet worden toegepast. Mogelijke typen ruimtelijke indexen zijn:

* Punt

* Veelhoek

* Multipolygoon

* Lijntekenreeks

Azure Cosmos DB maakt standaard geen ruimtelijke indexen. Als u ruimtelijke SQL-ingebouwde functies wilt gebruiken, moet u een ruimtelijke index maken over de vereiste eigenschappen. Zie [deze sectie](geospatial.md) voor het indexeren van beleidsvoorbeelden voor het toevoegen van ruimtelijke indexen.

## <a name="composite-indexes"></a>Samengestelde indexen

Query's met `ORDER BY` een component met twee of meer eigenschappen vereisen een samengestelde index. U ook een samengestelde index definiëren om de prestaties van veel gelijkheids- en bereikquery's te verbeteren. Standaard worden er geen samengestelde indexen gedefinieerd, dus u moet [samengestelde indexen toevoegen](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) als dat nodig is.

Wanneer u een samengestelde index definieert, geeft u het volgende op:

- Twee of meer eigendomspaden. De volgorde waarin eigenschapspaden worden gedefinieerd, is belangrijk.

- De volgorde (oplopend of aflopend).

> [!NOTE]
> Wanneer u een samengestelde index toevoegt, gebruikt de query bestaande bereikindexen totdat de nieuwe samengestelde indextoevoeging is voltooid. Wanneer u een samengestelde index toevoegt, u daarom niet onmiddellijk prestatieverbeteringen waarnemen. Het is mogelijk om de voortgang van de indextransformatie te volgen [met behulp van een van de SDK's.](how-to-manage-indexing-policy.md)

### <a name="order-by-queries-on-multiple-properties"></a>ORD OP query's op meerdere eigenschappen:

De volgende overwegingen worden gebruikt bij het `ORDER BY` gebruik van samengestelde indexen voor query's met een clausule met twee of meer eigenschappen:

- Als de samengestelde indexpaden niet overeenkomen met `ORDER BY` de volgorde van de eigenschappen in de component, kan de samengestelde index de query niet ondersteunen.

- De volgorde van samengestelde indexpaden (oplopend `order` of `ORDER BY` aflopend) moet ook overeenkomen met de in de clausule.

- De samengestelde index `ORDER BY` ondersteunt ook een clausule met de tegenovergestelde volgorde op alle paden.

Houd rekening met het volgende voorbeeld waarbij een samengestelde index is gedefinieerd op eigenschappen naam, leeftijd en _ts:

| **Samengestelde index**     | **Voorbeeldquery `ORDER BY`**      | **Ondersteund door Composite Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

U moet uw indexeringsbeleid aanpassen, `ORDER BY` zodat u alle benodigde query's weergeven.

### <a name="queries-with-filters-on-multiple-properties"></a>Query's met filters op meerdere eigenschappen

Als een query filters heeft op twee of meer eigenschappen, kan het handig zijn om een samengestelde index voor deze eigenschappen te maken.

Houd bijvoorbeeld rekening met de volgende query met een gelijkheidsfilter op twee eigenschappen:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Deze query zal efficiënter zijn, kost minder tijd en verbruikt minder RU's, als het in staat is om een samengestelde index op (naam ASC, leeftijd ASC) hefboomwerking.

Query's met bereikfilters kunnen ook worden geoptimaliseerd met een samengestelde index. De query kan echter slechts één bereikfilter hebben. Bereikfilters `>`omvatten `<` `<=`, `>=`, `!=`, en . Het bereikfilter moet als laatste in de samengestelde index worden gedefinieerd.

Houd rekening met de volgende query met zowel gelijkheids- als bereikfilters:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Deze query wordt efficiënter met een samengestelde index op (naam ASC, leeftijd ASC). De query zou echter geen samengestelde index gebruiken op (leeftijd ASC, naam ASC), omdat de gelijkheidsfilters eerst in de samengestelde index moeten worden gedefinieerd.

De volgende overwegingen worden gebruikt bij het maken van samengestelde indexen voor query's met filters op meerdere eigenschappen

- De eigenschappen in het filter van de query moeten overeenkomen met de eigenschappen in de samengestelde index. Als een eigenschap zich in de samengestelde index bevindt, maar niet als filter in de query is opgenomen, maakt de query geen gebruik van de samengestelde index.
- Als een query extra eigenschappen in het filter heeft die niet zijn gedefinieerd in een samengestelde index, wordt een combinatie van samengestelde en bereikindexen gebruikt om de query te evalueren. Dit vereist minder RU's dan uitsluitend het gebruik van bereikindexen.
- Als een eigenschap een`>`bereikfilter `>=`heeft `!=`( , `<`, `<=`, of ), moet deze eigenschap als laatste in de samengestelde index worden gedefinieerd. Als een query meer dan één bereikfilter heeft, wordt de samengestelde index niet gebruikt.
- Bij het maken van een samengestelde index `ORDER` om query's met meerdere filters te optimaliseren, heeft de samengestelde index geen invloed op de resultaten. Deze eigenschap is optioneel.
- Als u geen samengestelde index definieert voor een query met filters op meerdere eigenschappen, wordt de query nog steeds geslaagd. De RU-kosten van de query kunnen echter worden verlaagd met een samengestelde index.

Houd rekening met de volgende voorbeelden waarbij een samengestelde index is gedefinieerd op eigenschappen naam, leeftijd en tijdstempel:

| **Samengestelde index**     | **Voorbeeldquery**      | **Ondersteund door Composite Index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Query's met een filter en een ORDER BY-component

Als een query filtert op een of meer eigenschappen en verschillende eigenschappen heeft in de component `ORDER BY` ORDER BY, kan het handig zijn om de eigenschappen in het filter aan de component toe te voegen.

Door bijvoorbeeld de eigenschappen in het filter toe te voegen aan de clausule ORDER BY, kan de volgende query worden herschreven om een samengestelde index te gebruiken:

Query met bereikindex:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Query met samengestelde index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Hetzelfde patroon en queryoptimalisaties kunnen worden gegeneraliseerd voor query's met filters voor meerdere gelijkheiden:

Query met bereikindex:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Query met samengestelde index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

De volgende overwegingen worden gebruikt bij het maken van `ORDER BY` samengestelde indexen om een query te optimaliseren met een filter en een clausule:

* Als de query filters op eigenschappen, moeten `ORDER BY` deze eerst worden opgenomen in de clausule.
* Als u geen samengestelde index definieert op een query met `ORDER BY` een filter op één eigenschap en een afzonderlijke component met een andere eigenschap, wordt de query nog steeds geslaagd. De RU-kosten van de query kunnen echter worden verlaagd met `ORDER BY` een samengestelde index, vooral als de eigenschap in de clausule een hoge kardinaliteit heeft.
* Alle overwegingen voor het `ORDER BY` maken van samengestelde indexen voor query's met meerdere eigenschappen en query's met filters op meerdere eigenschappen zijn nog steeds van toepassing.


| **Samengestelde index**                      | **Voorbeeldquery `ORDER BY`**                                  | **Ondersteund door Composite Index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Het indexeringsbeleid wijzigen

Het indexeringsbeleid van een container kan op elk gewenst moment worden bijgewerkt [met behulp van de Azure-portal of een van de ondersteunde SDK's.](how-to-manage-indexing-policy.md) Een update van het indexeringsbeleid activeert een transformatie van de oude index naar de nieuwe, die online en op zijn plaats wordt uitgevoerd (er wordt dus geen extra opslagruimte verbruikt tijdens de bewerking). De index van het oude beleid wordt efficiënt omgezet in het nieuwe beleid zonder dat dit gevolgen heeft voor de schrijfbeschikbaarheid of de doorvoer die op de container is ingericht. Indextransformatie is een asynchrone bewerking en de tijd die nodig is om te voltooien, is afhankelijk van de ingerichte doorvoer, het aantal items en de grootte ervan.

> [!NOTE]
> Tijdens het toevoegen van een bereik of ruimtelijke index, query's kunnen niet alle overeenkomende resultaten terug, en zal dit doen zonder fouten terug te sturen. Dit betekent dat queryresultaten mogelijk niet consistent zijn totdat de indextransformatie is voltooid. Het is mogelijk om de voortgang van de indextransformatie te volgen [met behulp van een van de SDK's.](how-to-manage-indexing-policy.md)

Als de modus van het nieuwe indexeringsbeleid is ingesteld op Consistent, kan geen andere wijziging van het indexeringsbeleid worden toegepast terwijl de indextransformatie aan de gang is. Een actieve indextransformatie kan worden geannuleerd door de modus van het indexeringsbeleid in te stellen op Geen (waardoor de index onmiddellijk wordt gedaald).

## <a name="indexing-policies-and-ttl"></a>Indexeringsbeleid en TTL

De [Functie Time-to-Live (TTL)](time-to-live.md) vereist indexering om actief te zijn op de container die is ingeschakeld. Dit betekent dat:

- het is niet mogelijk om TTL te activeren op een container waar de indexeringsmodus is ingesteld op Geen,
- het is niet mogelijk om de indexeringsmodus in te stellen op Geen op een container waar TTL wordt geactiveerd.

Voor scenario's waarbij geen eigenschappenpad moet worden geïndexeerd, maar TTL vereist is, u een indexeringsbeleid gebruiken met:

- een indexeringsmodus ingesteld op Consistent en
- geen opgenomen pad, en
- `/*`als het enige uitgesloten pad.

## <a name="next-steps"></a>Volgende stappen

Lees meer over indexeren in de volgende artikelen:

- [Indexoverzicht](index-overview.md)
- [Indexeringsbeleid beheren](how-to-manage-indexing-policy.md)
