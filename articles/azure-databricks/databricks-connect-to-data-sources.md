---
title: 'Verbinding maken met verschillende gegevens bronnen vanuit Azure Databricks '
description: Informatie over het maken van verbinding met Azure SQL Database, Azure Data Lake Store, Blob Storage, Cosmos DB, Event Hubs en Azure SQL Data Warehouse van Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 9b44db3e8ffc02d211f7f97404f0cdd8d319fe03
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597493"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Verbinding maken met gegevens bronnen vanuit Azure Databricks

Dit artikel bevat koppelingen naar alle verschillende gegevens bronnen in azure die kunnen worden verbonden met Azure Databricks. Volg de voor beelden in deze koppelingen om gegevens te extra heren uit de Azure-gegevens bronnen (bijvoorbeeld Azure Blob Storage, Azure Event Hubs, enzovoort) in een Azure Databricks-cluster en om analytische taken uit te voeren. 

## <a name="prerequisites"></a>Vereisten

* U moet beschikken over een Azure Databricks-werk ruimte en een Spark-cluster. Volg de instructies in aan de [slag met Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Gegevens bronnen voor Azure Databricks

De volgende lijst bevat de gegevens bronnen in azure die u kunt gebruiken met Azure Databricks. Zie [gegevens bronnen voor Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html)voor een volledige lijst met gegevens bronnen die kunnen worden gebruikt met Azure Databricks.

- [Azure SQL database](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Deze koppeling biedt de data frame-API voor het maken van verbinding met SQL-data bases met JDBC en het beheren van de parallellisme van Lees bewerkingen via de JDBC-interface. Dit onderwerp bevat gedetailleerde voor beelden van het gebruik van de scala-API, met verkorte python-en Spark SQL-voor beelden aan het einde.
- [Azure Data Lake Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Deze koppeling bevat voor beelden over het gebruik van de Azure Active Directory Service-Principal voor verificatie met Azure Data Lake Storage. Het bevat ook instructies voor het openen van toegang tot de gegevens in Azure Data Lake Storage van Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Deze koppeling biedt voor beelden over hoe u rechtstreeks toegang krijgt tot Azure Blob Storage van Azure Databricks met behulp van toegangs sleutel of de SAS voor een bepaalde container. De koppeling bevat ook informatie over het openen van de Azure-Blob Storage van Azure Databricks met behulp van de RDD-API.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Deze koppeling bevat instructies over het gebruik van de [Azure Cosmos DB Spark-connector](https://github.com/Azure/azure-cosmosdb-spark) van Azure Databricks om toegang te krijgen tot gegevens in azure Cosmos db.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Deze koppeling bevat instructies over het gebruik van de [azure Event hubs Spark-connector](https://github.com/Azure/azure-event-hubs-spark) van Azure Databricks om toegang te krijgen tot gegevens in azure Event hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Deze koppeling bevat instructies over het gebruik van de Azure SQL Data Warehouse connector om verbinding te maken vanuit Azure Databricks.
    

## <a name="next-steps"></a>Volgende stappen

Zie [gegevens bronnen voor Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#)voor meer informatie over de bronnen waaruit u gegevens kunt importeren in azure Databricks.


