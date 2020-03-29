---
title: Een synthetische partitiesleutel maken in Azure Cosmos DB
description: Meer informatie over het gebruik van synthetische partitiesleutels in uw Azure Cosmos-containers om de gegevens en werkbelasting gelijkmatig over de partitiesleutels te verdelen
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441223"
---
# <a name="create-a-synthetic-partition-key"></a>Een synthetische partitiesleutel maken

Het is de beste praktijk om een partitiesleutel te hebben met veel verschillende waarden, zoals honderden of duizenden. Het doel is om uw gegevens en werkbelasting gelijkmatig te verdelen over de items die zijn gekoppeld aan deze partitiesleutelwaarden. Als een dergelijke eigenschap niet in uw gegevens bestaat, u een *synthetische partitiesleutel*maken. In dit document worden verschillende basistechnieken beschreven voor het genereren van een synthetische partitiesleutel voor uw Cosmos-container.

## <a name="concatenate-multiple-properties-of-an-item"></a>Meerdere eigenschappen van een item in concatenate

U een partitiesleutel vormen door meerdere eigenschapswaarden `partitionKey` samen te brengen in één kunstmatige eigenschap. Deze sleutels worden aangeduid als synthetische sleutels. Denk bijvoorbeeld aan het volgende voorbeelddocument:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Voor het vorige document is een optie het instellen /deviceId of /date als de partitiesleutel. Gebruik deze optie als u uw container wilt partitioneren op basis van apparaat-id of datum. Een andere optie is om deze twee waarden `partitionKey` te integreren in een synthetische eigenschap die wordt gebruikt als de partitiesleutel.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

In realtime scenario's u duizenden items in een database hebben. In plaats van de synthetische sleutel handmatig toe te voegen, definieert u de logica aan de clientzijde om waarden samen te voegen en voegt u de synthetische sleutel in de items in uw Cosmos-containers.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Een partitiesleutel gebruiken met een willekeurig achtervoegsel

Een andere mogelijke strategie om de werkbelasting gelijkmatiger te verdelen, is het toevoegen van een willekeurig getal aan het einde van de waarde van de partitiesleutel. Wanneer u items op deze manier distribueert, u parallelle schrijfbewerkingen uitvoeren tussen partities.

Een voorbeeld is als een partitiesleutel een datum vertegenwoordigt. U een willekeurig getal kiezen tussen 1 en 400 en het als achtervoegsel op de datum ingaan. Deze methode resulteert in `2018-08-09.1`partitiesleutelwaarden zoals`2018-08-09.2`, `2018-08-09.400`, enzovoort, via . Omdat u de partitiesleutel randomiseert, worden de schrijfbewerkingen op de container op elke dag gelijkmatig verdeeld over meerdere partities. Deze methode resulteert in een betere parallellisme en een algehele hogere doorvoer.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Een partitiesleutel met vooraf berekende achtervoegsels gebruiken 

De random achtervoegselstrategie kan de schrijfdoorvoer sterk verbeteren, maar het is moeilijk om een specifiek item te lezen. U weet niet de achtervoegselwaarde die is gebruikt toen u het item schreef. Gebruik de vooraf berekende achtervoegselsstrategie om het lezen van afzonderlijke items gemakkelijker te maken. In plaats van een willekeurig getal te gebruiken om de items over de partities te verdelen, gebruikt u een getal dat wordt berekend op basis van iets dat u wilt opvragen.

Denk aan het vorige voorbeeld, waarbij een container een datum als partitiesleutel gebruikt. Stel nu dat elk `Vehicle-Identification-Number` `VIN`item een ( ) attribuut heeft dat we willen openen. Verder, stel dat u vaak query's `VIN`uitvoeren om items te vinden door de , in aanvulling op datum. Voordat uw toepassing het item naar de container schrijft, kan het een hash-achtervoegsel berekenen op basis van het chassisnummer en het toevoegen aan de datum van de partitiesleutel. De berekening kan leiden tot een getal tussen 1 en 400 dat gelijkmatig wordt verdeeld. Dit resultaat is vergelijkbaar met de resultaten van de random achtervoegsel strategie methode. De waarde van de partitiesleutel is vervolgens de datum die is gekoppeld aan het berekende resultaat.

Met deze strategie worden de schrijfbewerkingen gelijkmatig verdeeld over de partitiesleutelwaarden en over de partities. U een bepaald item en bepaalde datum eenvoudig lezen, `Vehicle-Identification-Number`omdat u de waarde van de partitiesleutel voor een specifieke berekenen. Het voordeel van deze methode is dat u voorkomen dat u één hete partitiesleutel maakt, d.w.z. een partitiesleutel die alle werkbelasting nodig heeft. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het partitioneringsconcept vindt u in de volgende artikelen:

* Meer informatie over [logische partities](partition-data.md).
* Meer informatie over het [inrichten van doorvoer op Azure Cosmos-containers en -databases](set-throughput.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van doorvoer op een Azure Cosmos-database](how-to-provision-database-throughput.md).
