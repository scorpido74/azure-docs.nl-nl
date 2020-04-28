---
title: Toegang tot wijzigings feed in Azure Cosmos DB Azure Cosmos DB
description: In dit artikel worden verschillende opties beschreven die beschikbaar zijn voor lees-en toegangs wijzigings invoer in Azure Cosmos DB Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: fc7e78a44d03af8952c1e178a3e92b1ee0c6fe66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74688115"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Wijzigingenfeed in Azure Cosmos DB lezen

U kunt met behulp van de volgende opties met de Azure Cosmos DB wijzigings feed werken:

* Azure-functies gebruiken
* De processorbibliotheek voor de wijzigingenfeed gebruiken
* Azure Cosmos DB SQL API SDK gebruiken

## <a name="using-azure-functions"></a>Azure-functies gebruiken

Azure Functions is de eenvoudigste en aanbevolen optie. Wanneer u een Azure Functions trigger voor Cosmos DB maakt, kunt u de container selecteren om verbinding te maken en de Azure-functie wordt geactiveerd wanneer er een wijziging is in de container. Triggers kunnen worden gemaakt met behulp van de Azure Functions Portal, de Azure Cosmos DB portal of programmatisch met Sdk's. Visual Studio en VS code bieden ondersteuning voor het schrijven van Azure Functions, en u kunt zelfs de Azure Functions CLI gebruiken voor het ontwikkelen van meerdere platforms. U kunt de code op uw bureau blad schrijven en fouten opsporen en de functie vervolgens met één klik implementeren. Zie [Data Base computing zonder server met behulp van Azure functions](serverless-computing-database.md) en [gebruik Azure functions](change-feed-functions.md)) artikelen voor meer informatie.

## <a name="using-the-change-feed-processor-library"></a>De processorbibliotheek voor de wijzigingenfeed gebruiken

De processor bibliotheek van de wijzigings feed verbergt de complexiteit en biedt nog steeds een volledige controle over de wijzigings feed. De bibliotheek volgt het onderhouds patroon, waarbij uw verwerkings functie wordt aangeroepen door de bibliotheek. Als u een wijzigings feed voor hoge door Voer hebt, kunt u meerdere clients instantiëren om de wijzigings feed te lezen. Omdat u de bibliotheek van de feed-processor wijzigt, wordt de belasting van de verschillende clients automatisch verdeeld zonder dat u deze logica hoeft te implementeren. De complexiteit wordt verwerkt door de bibliotheek. Als u uw eigen load balancer wilt hebben, kunt u implementeren `IPartitionLoadBalancingStrategy` voor een aangepaste partitie strategie voor het verwerken van wijzigingen in feeds. Zie voor meer informatie gebruiken de bibliotheek voor het [wijzigen van de feed-processor](change-feed-processor.md).

## <a name="using-the-azure-cosmos-db-sql-api-sdk"></a>Azure Cosmos DB SQL API SDK gebruiken

Met de SDK krijgt u een controle op laag niveau van de wijzigings feed. U kunt het controle punt beheren, toegang krijgen tot een bepaalde logische partitie sleutel, enzovoort. Als u meerdere lezers hebt, kunt u gebruiken `ChangeFeedOptions` om Lees belasting te distribueren naar verschillende threads of verschillende clients.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in Api's voor Cassandra en MongoDB

De functionaliteit voor het wijzigen van de feed wordt geoppereerd als een wijzigings stroom in de MongoDB-API en query met een predikaat in Cassandra-API. Zie voor meer informatie over de implementatie Details voor MongoDB API de [wijzigings stromen in de Azure Cosmos DB-API voor MongoDb](mongodb-change-streams.md).

Systeem eigen Apache Cassandra biedt change data capture (CDC), een mechanisme voor het markeren van specifieke tabellen voor archivering en het afwijzen van schrijf bewerkingen naar die tabellen wanneer een Configureer bare grootte-op-schijf voor het CDC-logboek wordt bereikt. De functie voor het wijzigen van de feed in Azure Cosmos DB-API voor Cassandra verbetert de mogelijkheid om query's uit te voeren op de wijzigingen met een predikaat via CQL. Zie [Change feed in de Azure Cosmos DB-API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatie details.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over wijzigings invoer in de volgende artikelen:

* [Overzicht van wijzigings feed](change-feed.md)
* [Change feed gebruiken met Azure Functions](change-feed-functions.md)
* [De processor bibliotheek voor Change feed gebruiken](change-feed-processor.md)
