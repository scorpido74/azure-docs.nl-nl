---
title: Consistentie niveaus Apache Cassandra en Azure Cosmos DB
description: Consistentie niveaus van Apache Cassandra en Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 155e1ed5b089c16887c845c94f36c9c6d9f0af6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284102"
---
# <a name="apache-cassandra-and-azure-cosmos-db-consistency-levels"></a>Consistentie niveaus Apache Cassandra en Azure Cosmos DB

In tegens telling tot Azure Cosmos DB biedt Apache Cassandra geen systeem eigen nauw keurige consistentie garanties. In plaats daarvan biedt Apache Cassandra een schrijf consistentie niveau en een consistentie niveau voor lezen om de hoge Beschik baarheid, consistentie en latentie-afwegingen mogelijk te maken. Bij het gebruik van de Cassandra-API van Azure Cosmos DB:

* Het schrijf consistentie niveau van Apache Cassandra wordt toegewezen aan het standaard consistentie niveau dat is geconfigureerd voor uw Azure Cosmos-account. Consistentie voor een schrijf bewerking (LC) kan niet per aanvraag worden gewijzigd.

* Azure Cosmos DB wijst het Lees consistentie niveau dat is opgegeven door het Cassandra-client stuur programma dynamisch toe aan een van de Azure Cosmos DB consistentie niveaus die dynamisch zijn geconfigureerd op een lees aanvraag.

## <a name="mapping-consistency-levels"></a>Consistentie niveaus toewijzen

In de volgende tabel ziet u hoe de systeem eigen Cassandra consistentie niveaus worden toegewezen aan de consistentie niveaus van de Azure Cosmos DB wanneer u Cassandra-API gebruikt:  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Toewijzing van Cassandra-consistentie model" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

Als uw Azure Cosmos-account is geconfigureerd met een ander consistentie niveau dan de sterke consistentie, kunt u de kans ontdekken dat uw clients sterke en consistente Lees bewerkingen voor uw werk belastingen krijgen door te kijken naar de metrische gegevens van de *Probabilistically-gebonden veroudering* (PBS). Deze metriek wordt weer gegeven in de Azure Portal voor meer informatie, Zie [Probabilistically gebonden verouderde waarde (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic gebonden veroudering laten zien hoe uiteindelijk uw uiteindelijke consistentie is. Deze metrische gegevens bieden een inzicht in hoe vaak u een sterkere consistentie kunt krijgen dan het consistentie niveau dat u momenteel hebt geconfigureerd in uw Azure Cosmos-account. Met andere woorden, u kunt de waarschijnlijkheid (gemeten in milliseconden) voor het verkrijgen van sterk consistente Lees bewerkingen voor een combi natie van de regio's schrijven en lezen zien.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over globale distributie-en consistentie niveaus voor Azure Cosmos DB:

* [Overzicht van wereldwijde distributie](distribute-data-globally.md)
* [Overzicht van consistentie niveau](consistency-levels.md)
* [Hoge beschikbaarheid](high-availability.md)
