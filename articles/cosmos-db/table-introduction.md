---
title: Inleiding tot de Table-API van Azure Cosmos DB
description: Meer informatie over hoe u Azure Cosmos DB kunt gebruiken om grote hoeveelheden sleutelwaardegegevens met lage latentie op te slaan en op te vragen met behulp van de Azure Table-API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 5b2e2c51eaa878ba0ce8bc31c001575acebe6919
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240154"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Inleiding tot Azure Cosmos DB: tabel-API

[Azure Cosmos DB](introduction.md) biedt de Table-API voor toepassingen die zijn geschreven voor Azure Table-opslag en die premium-mogelijkheden nodig hebben, zoals:

* [Turnkey wereldwijde distributie](distribute-data-globally.md).
* [Speciale doorvoer](partition-data.md) overal ter wereld.
* Latentie van slechts enkele milliseconden op het 99e percentiel.
* Gegarandeerde hoge beschikbaarheid.
* Automatische secundaire indexering.

Toepassingen die zijn geschreven voor Azure Table-opslag kunnen met behulp van de Table-API zonder codeaanpassingen worden gemigreerd naar Azure Cosmos DB en zo gebruikmaken van premium-mogelijkheden. In Table-API zijn client-SDK's beschikbaar voor .NET, Java, Python en Node.js.

> [!IMPORTANT]
> De .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) bevindt zich in de onderhoudsmodus en wordt binnenkort afgeschaft. Upgrade naar de nieuwe .NET Standard-bibliotheek [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) om de nieuwste functies te blijven ondersteunen door de Tabel-API.

## <a name="table-offerings"></a>Aanbiedingen voor Table
Als u momenteel gebruikmaakt van Azure Table-opslag, levert overstappen naar de Azure Cosmos DB Table-API de volgende voordelen op:

| | Azure Table Storage | Azure Cosmos DB Table-API |
| --- | --- | --- |
| Latentie | Snel, maar geen bovengrens voor latentie. | Single-digit milliseconde latentie voor leest en schrijft, ondersteund met <10 ms latency voor leest en schrijft op het 99e percentiel, op elke schaal, overal in de wereld. |
| Doorvoer | Model voor variabele doorvoersnelheid. Tabellen hebben een schaalbaarheidslimiet van 20.000 bewerkingen/sec. | Zeer schaalbaar met [toegewezen gereserveerde doorvoer per tabel](request-units.md), op basis van serviceovereenkomsten. Accounts hebben geen bovengrens voor doorvoer en bieden ondersteuning voor > 10 miljoen bewerkingen/sec per tabel. |
| Wereldwijde distributie | Eén regio met één optioneel leesbaar secundair leesgebied voor hoge beschikbaarheid. U kunt geen failover starten. | [Turnkey wereldwijde distributie](distribute-data-globally.md) van één naar een willekeurig aantal regio's. Ondersteuning voor [kant en klare wereldwijde distributie](high-availability.md), op elk moment en overal ter wereld. Multi-master-mogelijkheden om elke regio schrijfbewerkingen te laten accepteren. |
| Indexeren | Alleen primaire index op PartitionKey en RowKey. Geen secundaire indexen. | Automatische en volledige indexering van alle eigenschappen standaard, zonder indexbeheer. |
| Query’s uitvoeren | Voor de queryuitvoering wordt een index gebruikt als primaire sleutel. In andere gevallen wordt er gescand. | Query's kunnen profiteren van de automatische indexering van eigenschappen voor een snelle uitvoertijden van query's. |
| Consistentie | Sterke in primaire regio. Mogelijk in secundaire regio. | [Vijf goed gedefinieerde consistentieniveaus](consistency-levels.md) om beschikbaarheid, latentie, doorvoer en consistentie af te zetten op basis van uw toepassingsbehoeften. |
| Prijzen | Geoptimaliseerd voor opslag. | Geoptimaliseerd voor doorvoer. |
| SLA's | 99,9% tot 99,99% beschikbaarheid, afhankelijk van de replicatiestrategie. | 99,999% leesbeschikbaarheid, 99,99% schrijft beschikbaarheid op een account met één regio en 99,999% schrijft beschikbaarheid op accounts met meerdere regio's. [Uitgebreide SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/) voor beschikbaarheid, latentie, doorvoer en consistentie. |

## <a name="get-started"></a>Aan de slag

Maak een Azure Cosmos DB-account in de [Azure-portal](https://portal.azure.com). Ga dan aan de slag met onze [Snelstartgids voor Table-API met behulp van .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Als u tijdens de preview een tabel-API-account hebt gemaakt, moet u een [nieuw tabel-API-account](create-table-dotnet.md#create-a-database-account) maken om te kunnen werken met de algemeen beschikbare SDK’s voor tabel-API's.
>

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanwijzingen om aan de slag te gaan:
* [Een .NET-toepassing ontwikkelen met de Table-API](create-table-dotnet.md)
* [Ontwikkelen met de Table-API in .NET](tutorial-develop-table-dotnet.md)
* [Tabelgegevens opvragen met de Table-API](tutorial-query-table.md)
* [Meer informatie over het instellen van wereldwijde distributie met Azure Cosmos DB met behulp van de Table-API](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Table .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB Table .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB-tabel Java SDK](table-sdk-java.md)
* [Azure Cosmos DB Table Node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB Table SDK voor Python](table-sdk-python.md)