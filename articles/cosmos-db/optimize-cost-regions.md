---
title: Kosten optimaliseren voor implementaties met meerdere regio's in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u de kosten van implementaties met meerdere regio's in Azure Cosmos DB beheert.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753307"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Multiregionale kosten optimaliseren in Azure Cosmos DB

U op elk gewenst moment regio's toevoegen en verwijderen aan uw Azure Cosmos-account. De doorvoer die u configureert voor verschillende Azure Cosmos-databases en -containers, is gereserveerd in elke regio die aan uw account is gekoppeld. Als de doorvoer die per uur is ingericht, dat is de som van RU/s die is geconfigureerd voor alle databases en containers voor uw Azure Cosmos-account `T` en het aantal Azure-regio's dat aan uw databaseaccount is gekoppeld, is `N`de totale ingerichte doorvoer voor uw Cosmos-account voor een bepaald uur gelijk aan:

1. `T x N RU/s`als uw Azure Cosmos-account is geconfigureerd met één schrijfgebied. 

1. `T x (N+1) RU/s`als uw Azure Cosmos-account is geconfigureerd met alle regio's die schrijfbewerkingen kunnen verwerken. 

Ingerichte doorvoer met één schrijfregio kost $ 0,008/uur per 100 RU/s, en ingerichte doorvoer met meerdere schrijfregio's kost $ 0,016/per uur per 100 RU/s. Zie de pagina Azure Cosmos [DB-prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/)voor meer informatie .

## <a name="costs-for-multiple-write-regions"></a>Kosten voor meerdere schrijfregio's

In een multi-master systeem, de netto beschikbare `N` RU's voor schrijfbewerkingen verhoogt de tijden waar `N` is het aantal schrijfregio's. In tegenstelling tot enkele regio schrijft, elke regio is nu beschrijfbaar en moet conflictoplossing te ondersteunen. De hoeveelheid werklast voor schrijvers is toegenomen. Vanuit het oogpunt van kostenplanning, om RU/s waarde van schrijft wereldwijd uit te voeren, `M` moet u M `RUs` op container- of databaseniveau inrichten. U dan zoveel regio's toevoegen als u `M` wilt en ze gebruiken voor schrijft om RU-waarde van wereldwijde schrijft uit te voeren. 

### <a name="example"></a>Voorbeeld

Bedenk dat u een container in West-VS hebt voorzien van doorvoer 10K RU/s en deze maand 1 TB aan gegevens opslaat. Laten we aannemen dat u drie regio's toevoegt - Oost-VS, Noord-Europa en Oost-Azië, elk met dezelfde opslag en doorvoer en u wilt de mogelijkheid om vanuit uw wereldwijd gedistribueerde app naar de containers in alle vier de regio's te schrijven. Uw totale maandelijkse factuur (uitgaande van 31 dagen) in een maand is als volgt:

|**Item**|**Gebruik (maandelijks)**|**Tarief**|**Maandelijkse kosten**|
|----|----|----|----|
|Doorvoerfactuur voor container in West-VS (meerdere schrijfregio's) |10K RU/s * 24 * 31 |$ 0,016 per 100 RU/s per uur |$ 1.190,40 |
|Doorvoerfactuur voor 3 extra regio's - Oost-VS, Noord-Europa en Oost-Azië (meerdere schrijfregio's) |(3 + 1) * 10K RU/s * 24 * 31 |$ 0,016 per 100 RU/s per uur |$ 4.761,60 |
|Opslagfactuur voor container in West-VS |1 TB (of 1.024 GB) |$ 0,25/GB |$ 256 |
|Opslagfactuur voor 3 extra regio's - Oost-VS, Noord-Europa en Oost-Azië |3 * 1 TB (of 3.072 GB) |$ 0,25/GB |$ 768 |
|**Totaal**|||**$ 6.976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Het gebruik van de doorvoer per regio verbeteren

Als u bijvoorbeeld een of meer onderbenutte of overbenutte regio's inefficiënt gebruikt, u de volgende stappen nemen om het gebruik van de doorvoer te verbeteren:  

1. Zorg ervoor dat u de ingerichte doorvoer (RU's) in het schrijfgebied eerst optimaliseert en gebruik vervolgens het maximale gebruik van de R's in leesgebieden door wijzigingsfeed uit het leesgebied te gebruiken, enz. 

2. Meerdere schrijfregio's kunnen worden geschaald in alle regio's die zijn gekoppeld aan het Azure Cosmos-account. 

3. Houd de activiteit in uw regio's in de gaten en u regio's op aanvraag toevoegen en verwijderen om uw lees- en schrijfdoorvoer te schalen.

## <a name="next-steps"></a>Volgende stappen

Vervolgens u meer te weten komen over kostenoptimalisatie in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [het begrijpen van uw Azure Cosmos DB-factuur](understand-your-bill.md)
* Meer informatie over [het optimaliseren van doorvoerkosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van opslagkosten](optimize-cost-storage.md)
* Meer informatie over [het optimaliseren van de kosten van lezen en schrijven](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](optimize-cost-queries.md)

