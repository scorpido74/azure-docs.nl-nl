---
title: Toegang tot wijzigingsfeed in Azure Cosmos DB Azure Cosmos DB
description: In dit artikel worden verschillende opties beschreven die beschikbaar zijn voor het lezen en openen van wijzigingsfeed in Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688115"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Wijzigingenfeed in Azure Cosmos DB lezen

U werken met de Azure Cosmos DB-wijzigingsfeed met een van de volgende opties:

* Azure-functies gebruiken
* De processorbibliotheek voor de wijzigingenfeed gebruiken
* Azure Cosmos DB SQL API SDK gebruiken

## <a name="using-azure-functions"></a>Azure-functies gebruiken

Azure Functions is de eenvoudigste en aanbevolen optie. Wanneer u een Azure-functietrigger maakt voor Cosmos DB, u de container selecteren die moet worden verbonden en wordt de Azure-functie geactiveerd wanneer er een wijziging in de container is. Triggers kunnen worden gemaakt met behulp van de Azure Functions-portal, de Azure Cosmos DB-portal of programmatisch met SDKs. Visual Studio en VS Code bieden ondersteuning voor het schrijven van Azure-functies en u de AZURE Functions CLI zelfs gebruiken voor cross-platform ontwikkeling. U de code op uw bureaublad schrijven en debuggen en de functie vervolgens met één klik implementeren. Zie [Serverless database computing met Azure Functions](serverless-computing-database.md) en Change Feed gebruiken met Azure [Functions)](change-feed-functions.md)artikelen voor meer informatie.

## <a name="using-the-change-feed-processor-library"></a>De processorbibliotheek voor de wijzigingenfeed gebruiken

De bibliotheek voor de wijzigingsfeedprocessor verbergt de complexiteit en geeft u nog steeds volledige controle over de wijzigingsfeed. De bibliotheek volgt het waarnemerpatroon, waarbij uw verwerkingsfunctie door de bibliotheek wordt aangeroepen. Als u een feed met een hoge doorvoerwijziging hebt, u meerdere clients instantiëren om de wijzigingsfeed te lezen. Omdat u de bibliotheek voor de wijzigingsfeedprocessor gebruikt, wordt de belasting automatisch verdeeld over de verschillende clients zonder dat u deze logica hoeft te implementeren. Alle complexiteit wordt afgehandeld door de bibliotheek. Als u uw eigen load balancer wilt `IPartitionLoadBalancingStrategy` hebben, u implementeren voor een aangepaste partitiestrategie om wijzigingsfeed te verwerken. Zie voor meer informatie [de bibliotheek voor wijzigingsfeedprocessor](change-feed-processor.md)gebruiken .

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK gebruiken

Met de SDK krijgt u een low-level controle over de change feed. U het controlepunt beheren, toegang krijgen tot een bepaalde logische partitiesleutel, enz. Als u meerdere lezers hebt, kunt `ChangeFeedOptions` u lezenladen distribueren naar verschillende threads of verschillende clients.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in API's voor Cassandra en MongoDB

Feedfunctionaliteit wijzigen wordt opgedoken als wijzigingsstroom in De API van MongoDB en Query met predicaat in Cassandra API. Zie de Change streams in de Azure Cosmos [DB API voor MongoDB voor](mongodb-change-streams.md)meer informatie over de implementatiedetails voor MongoDB.

Native Apache Cassandra biedt change data capture (CDC), een mechanisme om specifieke tabellen voor archivering te markeren en het afwijzen van schrijft naar die tabellen zodra een configureerbare grootte op de schijf voor het CDC-logboek is bereikt. De wijzigingsfeedfunctie in Azure Cosmos DB API voor Cassandra verbetert de mogelijkheid om de wijzigingen met predicaat op te vragen via CQL. Zie [Feed wijzigen in de Azure Cosmos DB API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatiedetails.

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de wijzigingsfeed in de volgende artikelen:

* [Overzicht van wijzigingsfeed](change-feed.md)
* [Wijzigingsfeed gebruiken met Azure-functies](change-feed-functions.md)
* [De feedprocessorbibliotheek wijzigen gebruiken](change-feed-processor.md)
