---
title: Azure Cosmos DB indexerings beleid
description: Meer informatie over het configureren en wijzigen van het standaard indexerings beleid voor automatische indexering en betere prestaties in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/09/2020
ms.author: tisande
ms.openlocfilehash: a335da61fac914368b4044a97582ef0060f5de4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636322"
---
# <a name="indexing-policies-in-azure-cosmos-db"></a>Indexeringsbeleid in Azure Cosmos DB

In Azure Cosmos DB heeft elke container een indexerings beleid dat bepaalt hoe de items van de container moeten worden geïndexeerd. Het standaard indexerings beleid voor nieuw gemaakte containers indexeert elke eigenschap van elk item, waarbij bereik indexen worden afgedwongen voor wille keurige teken reeksen of getallen en ruimtelijke indexen voor een geojson-object van het type punt. Zo kunt u hoge query prestaties krijgen zonder dat u op de hoogte hoeft te zijn van indexering en index beheer vooraf.

In sommige gevallen wilt u mogelijk dit automatische gedrag overschrijven, zodat het beter aansluit bij uw vereisten. U kunt het indexerings beleid van een container aanpassen door de *indexerings modus*in te stellen en *eigenschaps paden*op te nemen of uit te sluiten.

> [!NOTE]
> De methode voor het bijwerken van het indexerings beleid dat in dit artikel wordt beschreven, is alleen van toepassing op de SQL-API (core) van Azure Cosmos DB.

## <a name="indexing-mode"></a>Indexerings modus

Azure Cosmos DB ondersteunt twee indexerings modi:

- **Consistent**: de index wordt synchroon bijgewerkt wanneer u items maakt, bijwerkt of verwijdert. Dit betekent dat de consistentie van uw Lees query's de [consistentie is die voor het account is geconfigureerd](consistency-levels.md).
- **Geen**: indexeren is uitgeschakeld op de container. Dit wordt meestal gebruikt wanneer een container wordt gebruikt als een pure sleutel waarde Store zonder dat hiervoor secundaire indexen nodig zijn. Het kan ook worden gebruikt om de prestaties van bulk bewerkingen te verbeteren. Nadat de bulk bewerkingen zijn voltooid, kan de index modus worden ingesteld op consistent en vervolgens worden bewaakt met behulp van de [IndexTransformationProgress](how-to-manage-indexing-policy.md#dotnet-sdk) totdat de bewerking is voltooid.

> [!NOTE]
> Azure Cosmos DB ondersteunt ook een vertraagde indexerings modus. Lazy-indexering voert updates op een veel lagere prioriteits niveau uit als de engine geen andere werkzaamheden uitvoert. Dit kan leiden tot **inconsistente of onvolledige** query resultaten. Als u van plan bent om een query uit te zoeken op een Cosmos-container, moet u geen luie indexering selecteren. In juni 2020 hebben we een wijziging aangebracht waarbij nieuwe containers niet langer kunnen worden ingesteld op de vertraagde indexerings modus. Als uw Azure Cosmos DB-account al ten minste één container met een luie index bevat, wordt dit account automatisch uitgesloten van de wijziging. U kunt ook een uitzonde ring aanvragen door contact op te nemen met de [ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Indexerings beleid is standaard ingesteld op `automatic` . Het wordt bereikt door de `automatic` eigenschap in het indexerings beleid in te stellen op `true` . Als u deze eigenschap instelt op, `true` kan Azure CosmosDB documenten automatisch indexeren wanneer ze zijn geschreven.

## <a name="including-and-excluding-property-paths"></a><a id="include-exclude-paths"></a>Eigenschaps paden opnemen en uitsluiten

Een aangepast indexerings beleid kan eigenschaps paden opgeven die expliciet worden opgenomen of uitgesloten van indexeren. Door het aantal geïndexeerde paden te optimaliseren, kunt u de hoeveelheid opslag die wordt gebruikt door uw container verlagen en de latentie van schrijf bewerkingen verbeteren. Deze paden worden gedefinieerd volgens [de methode die wordt beschreven in de sectie Overzicht indexering](index-overview.md#from-trees-to-property-paths) met de volgende toevoegingen:

- een pad dat leidt naar een scalaire waarde (teken reeks of getal) eindigt op`/?`
- elementen uit een matrix worden samen met de `/[]` notatie (in plaats van `/0` , `/1` enzovoort) beschreven.
- het `/*` Joker teken kan worden gebruikt om alle elementen onder het knoop punt te vergelijken

Hetzelfde voor beeld opnieuw uitvoeren:

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

- het `headquarters` `employees` pad is`/headquarters/employees/?`

- het `locations` `country` pad is`/locations/[]/country/?`

- het pad naar iets onder `headquarters` is`/headquarters/*`

We kunnen bijvoorbeeld het `/headquarters/employees/?` pad toevoegen. Dit pad zorgt ervoor dat de eigenschap Employees wordt geïndexeerd, maar dat er geen extra geneste JSON binnen deze eigenschap zou worden geïndexeerd.

## <a name="includeexclude-strategy"></a>Strategie voor opnemen/uitsluiten

Elk indexerings beleid moet het basispad bevatten `/*` als een opgenomen of uitgesloten pad.

- Neem het hoofdpad op om selectieve paden uit te sluiten die niet hoeven te worden geïndexeerd. Dit is de aanbevolen benadering, omdat hiermee Azure Cosmos DB proactief een nieuwe eigenschap kan indexeren die aan uw model kan worden toegevoegd.
- Sluit het hoofdpad uit om selectieve paden op te nemen die moeten worden geïndexeerd.

- Voor paden met gewone tekens die bevatten: alfanumerieke tekens en _ (onderstrepings teken), hoeft u de padtekenreeks niet te escapepen rond dubbele aanhalings tekens (bijvoorbeeld '/Path/? '). Voor paden met andere speciale tekens moet u de teken reeks voor het pad Escape rond dubbele aanhalings tekens (bijvoorbeeld "/ \" Path-ABC \" /?"). Als u speciale tekens in uw pad verwacht, kunt u elk pad voor de beveiliging op te zeggen. Dit is functioneel geen verschil als u elk pad en alleen de bestanden met speciale tekens weglaat.

- De eigenschap System `_etag` wordt standaard uitgesloten van indexeren, tenzij de ETAG wordt toegevoegd aan het opgenomen pad voor indexering.

- Als de indexerings modus is ingesteld op **consistent**, worden de systeem eigenschappen `id` `_ts` automatisch geïndexeerd.

Bij het opnemen en uitsluiten van paden kunnen de volgende kenmerken optreden:

- `kind`Dit kan ofwel `range` of zijn `hash` . De functionaliteit van bereik index biedt alle functionaliteit van een hash-index. Daarom raden we u aan een bereik index te gebruiken.

- `precision`is een getal dat is gedefinieerd op index niveau voor opgenomen paden. Een waarde die de `-1` maximale nauw keurigheid aangeeft. We raden u aan deze waarde altijd in te stellen op `-1` .

- `dataType`Dit kan ofwel `String` of zijn `Number` . Hiermee worden de typen JSON-eigenschappen aangegeven die worden geïndexeerd.

Als deze eigenschap niet is opgegeven, hebben deze eigenschappen de volgende standaard waarden:

| **Eigenschaps naam**     | **Standaardwaarde** |
| ----------------------- | -------------------------------- |
| `kind`   | `range` |
| `precision`   | `-1`  |
| `dataType`    | `String` en `Number` |

Zie [deze sectie](how-to-manage-indexing-policy.md#indexing-policy-examples) voor voor beelden van indexerings beleid voor het opnemen en uitsluiten van paden.

## <a name="includeexclude-precedence"></a>Voor rang opnemen/uitsluiten

Als uw opgenomen paden en uitgesloten paden een conflict veroorzaken, heeft het precieze pad prioriteit.

Hier volgt een voorbeeld:

**Opgenomen pad**:`/food/ingredients/nutrition/*`

**Uitgesloten pad**:`/food/ingredients/*`

In dit geval heeft het opgenomen pad voor rang op het uitgesloten pad, omdat dit nauw keuriger is. Op basis van deze paden worden alle gegevens in het `food/ingredients` pad, of genest binnen, uitgesloten van de index. De uitzonde ring hiervan zijn gegevens in het opgenomen pad: `/food/ingredients/nutrition/*` , dat wordt geïndexeerd.

Hier volgen enkele regels voor de prioriteit van opgenomen en uitgesloten paden in Azure Cosmos DB:

- Diepere paden zijn nauw keuriger dan smallere paden. bijvoorbeeld: `/a/b/?` is nauw keuriger dan `/a/?` .

- De `/?` is nauw keuriger dan `/*` . Het `/a/?` is bijvoorbeeld nauw keuriger dan `/a/*` dat voor `/a/?` rang heeft.

- Het pad `/*` moet een opgenomen pad of uitgesloten pad zijn.

## <a name="spatial-indexes"></a>Ruimtelijke indexen

Wanneer u een ruimtelijk pad definieert in het indexerings beleid, moet u definiëren welke index ```type``` moet worden toegepast op dat pad. Mogelijke typen voor ruimtelijke indexen zijn onder andere:

* Spreek

* Polygoon

* Multi Polygon

* Lines Tring

Met Azure Cosmos DB worden standaard geen ruimtelijke indexen gemaakt. Als u ruimtelijke SQL-functies wilt gebruiken, moet u een ruimtelijke index maken op basis van de vereiste eigenschappen. Zie [deze sectie](geospatial.md) voor voor beelden van indexerings beleid voor het toevoegen van ruimtelijke indexen.

## <a name="composite-indexes"></a>Samengestelde indexen

Query's die een- `ORDER BY` component met twee of meer eigenschappen hebben, hebben een samengestelde index nodig. U kunt ook een samengestelde index definiëren om de prestaties van veel gelijkheid en bereik query's te verbeteren. Standaard zijn er geen samengestelde indexen gedefinieerd, dus u moet zo nodig [samengestelde indexen toevoegen](how-to-manage-indexing-policy.md#composite-indexing-policy-examples) .

In tegens telling tot met opgenomen of uitgesloten paden kunt u geen pad maken met het `/*` Joker teken. Elk samengesteld pad heeft een impliciet `/?` aan het einde van het pad dat u niet hoeft op te geven. Samengestelde paden leiden naar een scalaire waarde. Dit is de enige waarde die wordt opgenomen in de samengestelde index.

Wanneer u een samengestelde index definieert, geeft u het volgende op:

- Twee of meer eigenschaps paden. De volg orde waarin eigenschaps paden worden gedefinieerd.

- De volg orde (oplopend of aflopend).

> [!NOTE]
> Wanneer u een samengestelde index toevoegt, gebruikt de query bestaande bereik indexen totdat de nieuwe samengestelde index toevoeging is voltooid. Daarom is het mogelijk dat u de prestatie verbeteringen niet onmiddellijk kunt waarnemen wanneer u een samengestelde index toevoegt. Het is mogelijk om de voortgang van de index transformatie [te volgen met behulp van een van de sdk's](how-to-manage-indexing-policy.md).

### <a name="order-by-queries-on-multiple-properties"></a>Volg orde van query's op meerdere eigenschappen:

De volgende overwegingen worden gebruikt bij het gebruik van samengestelde indexen voor query's met een- `ORDER BY` component met twee of meer eigenschappen:

- Als de samengestelde index paden niet overeenkomen met de volg orde van de eigenschappen in de `ORDER BY` component, kan de samengestelde index de query niet ondersteunen.

- De volg orde van samengestelde index paden (oplopend of aflopend) moet ook overeenkomen met de `order` in de- `ORDER BY` component.

- De samengestelde index ondersteunt ook een `ORDER BY` component met de tegenovergestelde volg orde op alle paden.

Bekijk het volgende voor beeld waarbij een samengestelde index is gedefinieerd voor de eigenschappen naam, leeftijd en _ts:

| **Samengestelde index**     | **Voorbeeld `ORDER BY` query**      | **Ondersteund door samengestelde index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.name ASC, c.age asc``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c ORDER BY c.age ASC, c.name asc```   | ```No```             |
| ```(name ASC, age ASC)```    | ```SELECT * FROM c ORDER BY c.name DESC, c.age DESC``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c ORDER BY c.name ASC, c.age DESC``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC, timestamp ASC``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c ORDER BY c.name ASC, c.age ASC``` | ```No```            |

U moet het indexerings beleid aanpassen zodat u alle benodigde query's kunt uitvoeren `ORDER BY` .

### <a name="queries-with-filters-on-multiple-properties"></a>Query's met filters op meerdere eigenschappen

Als een query filters heeft voor twee of meer eigenschappen, kan het handig zijn om een samengestelde index voor deze eigenschappen te maken.

Denk bijvoorbeeld aan de volgende query met een gelijkheids filter voor twee eigenschappen:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age = 18
```

Deze query is efficiënter, neemt minder tijd in beslag en verbruikt meer dan één RU, als het een samengestelde index op (naam ASC, Age ASC) kan gebruiken.

Query's met bereik filters kunnen ook worden geoptimaliseerd met een samengestelde index. De query kan echter slechts één bereik filter hebben. Bereik filters zijn onder andere `>` ,, `<` `<=` , `>=` en `!=` . Het bereik filter moet als laatste worden gedefinieerd in de samengestelde index.

Houd rekening met de volgende query met filters voor gelijkheid en bereik:

```sql
SELECT * FROM c WHERE c.name = "John" AND c.age > 18
```

Deze query is efficiënter met een samengestelde index op (naam ASC, Age ASC). De query maakt echter geen gebruik van een samengestelde index op (leeftijd ASC, naam ASC), omdat de gelijkheids filters eerst moeten worden gedefinieerd in de samengestelde index.

De volgende overwegingen worden gebruikt bij het maken van samengestelde indexen voor query's met filters voor meerdere eigenschappen

- De eigenschappen in het filter van de query moeten overeenkomen met die in de samengestelde index. Als een eigenschap zich in de samengestelde index bevindt maar niet is opgenomen in de query als een filter, wordt de samengestelde index niet gebruikt voor de query.
- Als een query extra eigenschappen in het filter heeft die niet in een samengestelde index zijn gedefinieerd, wordt een combi natie van samengestelde en bereik indexen gebruikt om de query te evalueren. Hiervoor is minder RU vereist dan exclusief het gebruik van bereik indexen.
- Als een eigenschap een bereik filter heeft ( `>` ,,, `<` `<=` `>=` of `!=` ), dan moet deze eigenschap als laatste worden gedefinieerd in de samengestelde index. Als een query meer dan één bereik filter heeft, wordt de samengestelde index niet gebruikt.
- Bij het maken van een samengestelde index voor het optimaliseren van query's met meerdere filters, `ORDER` heeft de samengestelde index geen invloed op de resultaten. Deze eigenschap is optioneel.
- Als u geen samengestelde index voor een query met filters voor meerdere eigenschappen definieert, zal de query toch slagen. De RU-kosten van de query kunnen echter worden verminderd met een samengestelde index.

Bekijk de volgende voor beelden waarin een samengestelde index is gedefinieerd voor de eigenschappen naam, leeftijd en tijds tempel:

| **Samengestelde index**     | **Voorbeeld query**      | **Ondersteund door samengestelde index?** |
| ----------------------- | -------------------------------- | -------------- |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18``` | ```Yes```            |
| ```(name ASC, age ASC)```   | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18```   | ```Yes```             |
| ```(name DESC, age ASC)```    | ```SELECT * FROM c WHERE c.name = "John" AND c.age > 18``` | ```Yes```            |
| ```(name ASC, age ASC)```     | ```SELECT * FROM c WHERE c.name != "John" AND c.age > 18``` | ```No```             |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age = 18 AND c.timestamp > 123049923``` | ```Yes```            |
| ```(name ASC, age ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.name = "John" AND c.age < 18 AND c.timestamp = 123049923``` | ```No```            |

### <a name="queries-with-a-filter-as-well-as-an-order-by-clause"></a>Query's met een filter en een ORDER BY-component

Als een query filtert op een of meer eigenschappen en andere eigenschappen in de ORDER BY-component heeft, kan het handig zijn om de eigenschappen in het filter toe te voegen aan de- `ORDER BY` component.

Bijvoorbeeld, door de eigenschappen in het filter toe te voegen aan de ORDER BY-component, kan de volgende query worden herschreven om gebruik te maken van een samengestelde index:

Query's uitvoeren met de bereik index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp
```

Query's uitvoeren met samengestelde index:

```sql
SELECT * FROM c WHERE c.name = "John" ORDER BY c.name, c.timestamp
```

Dezelfde patroon-en query optimalisaties kunnen worden gegeneraliseerd voor query's met meerdere gelijkheids filters:

Query's uitvoeren met de bereik index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.timestamp
```

Query's uitvoeren met samengestelde index:

```sql
SELECT * FROM c WHERE c.name = "John", c.age = 18 ORDER BY c.name, c.age, c.timestamp
```

De volgende overwegingen worden gebruikt bij het maken van samengestelde indexen voor het optimaliseren van een query met een filter en `ORDER BY` component:

* Als de query filtert op Eigenschappen, moeten deze eerst worden opgenomen in de `ORDER BY` component.
* Als u geen samengestelde index definieert voor een query met een filter voor één eigenschap en een afzonderlijke `ORDER BY` component met behulp van een andere eigenschap, zal de query toch slagen. De RU-kosten van de query kunnen echter worden verminderd met een samengestelde index, met name als de eigenschap in de `ORDER BY` component een hoge kardinaliteit heeft.
* Alle overwegingen voor het maken van samengestelde indexen voor `ORDER BY` query's met meerdere eigenschappen en query's met filters voor meerdere eigenschappen zijn nog steeds van toepassing.


| **Samengestelde index**                      | **Voorbeeld `ORDER BY` query**                                  | **Ondersteund door samengestelde index?** |
| ---------------------------------------- | ------------------------------------------------------------ | --------------------------------- |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.name ASC, c.timestamp ASC``` | `Yes` |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC, c.name ASC``` | `No`  |
| ```(name ASC, timestamp ASC)```          | ```SELECT * FROM c WHERE c.name = "John" ORDER BY c.timestamp ASC``` | ```No```   |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.age ASC, c.name ASC,c.timestamp ASC``` | `Yes` |
| ```(age ASC, name ASC, timestamp ASC)``` | ```SELECT * FROM c WHERE c.age = 18 and c.name = "John" ORDER BY c.timestamp ASC``` | `No` |

## <a name="modifying-the-indexing-policy"></a>Het indexerings beleid wijzigen

Het indexerings beleid van een container kan op elk gewenst moment worden bijgewerkt [met behulp van de Azure portal of een van de ondersteunde sdk's](how-to-manage-indexing-policy.md). Een update voor het indexerings beleid activeert een trans formatie van de oude index naar de nieuwe, die online en op locatie wordt uitgevoerd (zodat er geen extra opslag ruimte wordt verbruikt tijdens de bewerking). De oude beleids index is efficiënt getransformeerd naar het nieuwe beleid zonder dat dit van invloed is op de schrijf Beschik baarheid of de door Voer ingericht op de container. Index transformatie is een asynchrone bewerking en de tijd die nodig is om te volt ooien, is afhankelijk van de ingerichte door Voer, het aantal items en de grootte ervan.

> [!NOTE]
> Bij het toevoegen van een bereik-of ruimtelijke index, retour neren query's mogelijk niet alle overeenkomende resultaten en worden deze zonder fouten geretourneerd. Dit betekent dat query resultaten mogelijk niet consistent zijn totdat de index transformatie is voltooid. Het is mogelijk om de voortgang van de index transformatie [te volgen met behulp van een van de sdk's](how-to-manage-indexing-policy.md).

Als de modus nieuw indexerings beleid is ingesteld op consistent, kan er geen andere wijziging van het indexerings beleid worden toegepast terwijl de index transformatie wordt uitgevoerd. Een actieve index transformatie kan worden geannuleerd door de modus van het indexerings beleid in te stellen op geen (waardoor de index onmiddellijk wordt verwijderd).

## <a name="indexing-policies-and-ttl"></a>Indexerings beleid en TTL

Voor de [functie time-to-Live (TTL)](time-to-live.md) moet indexering actief zijn op de container waarop deze is ingeschakeld. Dit betekent dat:

- het is niet mogelijk om TTL te activeren voor een container waarbij de indexerings modus is ingesteld op geen,
- het is niet mogelijk om de indexerings modus in te stellen op geen in een container waarin TTL wordt geactiveerd.

Voor scenario's waarin geen eigenschapspad moet worden geïndexeerd, maar TTL vereist is, kunt u een indexerings beleid gebruiken met:

- een indexerings modus is ingesteld op consistent en
- geen opgenomen pad en
- `/*`alleen als het uitgesloten pad.

## <a name="next-steps"></a>Volgende stappen

Lees meer over indexeren in de volgende artikelen:

- [Overzicht van indexeren](index-overview.md)
- [Indexerings beleid beheren](how-to-manage-indexing-policy.md)
