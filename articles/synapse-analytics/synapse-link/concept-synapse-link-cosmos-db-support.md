---
title: Ondersteunde functies van Azure Synapse Link voor Azure Cosmos DB
description: Krijg meer informatie over de huidige lijst met acties die worden ondersteund in Azure Synapse Link voor Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1b94d7677026f3695d07be4d83a5059373078c2e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599030"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Ondersteunde functies van Azure Synapse Link voor Azure Cosmos DB

In dit artikel worden de functies beschreven die momenteel worden ondersteund in Azure Synapse Link voor Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Ondersteuning in Azure Synapse

Er zijn twee typen containers in Azure Cosmos DB:
* HTAP-container: een container waarvoor Synapse Link is ingeschakeld. Deze container heeft zowel een transactionele opslag als een analytische opslag. 
* OLTP-container: een container met alleen een transactieopslag, zonder dat Synapse Link is ingeschakeld. 

U kunt verbinding maken met een Azure Cosmos DB-container zonder Synapse Link in te schakelen. In dit geval kunt u alleen lezen/schrijven naar de transactionele opslag. Hier volgt een lijst met de functies die momenteel worden ondersteund in Synapse Link voor Azure Cosmos DB. 

| Categorie              | Beschrijving |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Serverloze SQL](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Ondersteuning tijdens uitvoeringen** |Ondersteuning voor lees- of schrijfbewerkingen tijdens een Azure Synapse-uitvoering| ✓ | [Contact opnemen](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **API-ondersteuning voor Azure Cosmos DB** |API-ondersteuning als Synapse Link| SQL / MongoDB | SQL / MongoDB |
| **Object**  |Objecten, zoals een tabel, die kunnen worden gemaakt en rechtstreeks naar de Azure Cosmos DB-container verwijzen| Weergave, tabel | Weergave |
| **Lezen**    |Gegevens lezen uit een Azure Cosmos DB-container| OLTP / HTAP | HTAP  |
| **Schrijven**   |Gegevens vanuit de uitvoering naar een Azure Cosmos DB-container schrijven| OLTP | N.v.t. |

* Als u gegevens in een Azure Cosmos DB-container schrijft vanuit Spark, vindt dit proces plaats via de transactionele opslag van Azure Cosmos DB. Dit heeft invloed op de transactionele prestaties van Azure Cosmos DB, omdat aanvraageenheden worden verbruikt.
* Integratie van SQL-pools via externe tabellen wordt momenteel niet ondersteund.

## <a name="supported-code-generated-actions-for-spark"></a>Ondersteunde met code gegenereerde acties voor Spark

| Bewegen              | Beschrijving |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Laden in Dataframe** |Gegevens laden en lezen in een Spark-dataframe |X| ✓ |
| **Spark-tabel maken** |Een tabel maken die verwijst naar een Azure Cosmos DB-container|X| ✓ |
| **Dataframe naar een container schrijven** |Gegevens naar een container schrijven|✓| ✓ |
| **Streaming-dataframe laden vanuit een container** |Gegevens streamen met behulp van de wijzigingenfeed in Azure Cosmos DB|✓| ✓ |
| **Streaming-dataframe naar een container schrijven** |Gegevens streamen met behulp van de wijzigingenfeed in Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Ondersteunde met code gegenereerde acties voor serverloze SQL

| Bewegen              | Beschrijving |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Top 100 selecteren** |Een voorbeeld van de belangrijkste 100 items uit een container bekijken|X| ✓ |
| **Weergave maken** |Een weergave maken om rechtstreeks BI-toegang tot een container te hebben via Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Volgende stappen

* [Verbinding maken met Synapse Link voor Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Leer hoe u de analytische opslag doorzoekt met Spark](how-to-query-analytical-store-spark.md)