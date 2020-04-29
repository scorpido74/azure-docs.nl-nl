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
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548798"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Verbinding maken met gegevens bronnen vanuit Azure Databricks

Dit artikel bevat koppelingen naar alle verschillende gegevens bronnen in azure die kunnen worden verbonden met Azure Databricks. Volg de voor beelden in deze koppelingen om gegevens te extra heren uit de Azure-gegevens bronnen (bijvoorbeeld Azure Blob Storage, Azure Event Hubs, enzovoort) in een Azure Databricks-cluster en om analytische taken uit te voeren. 

## <a name="prerequisites"></a>Vereisten

* U moet beschikken over een Azure Databricks-werk ruimte en een Spark-cluster. Volg de instructies in aan de [slag met Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Gegevens bronnen voor Azure Databricks

De volgende lijst bevat de gegevens bronnen in azure die u kunt gebruiken met Azure Databricks. Zie [gegevens bronnen voor Azure Databricks](/azure/databricks/data/data-sources/index)voor een volledige lijst met gegevens bronnen die kunnen worden gebruikt met Azure Databricks.

- [Azure SQL database](/azure/databricks/data/data-sources/sql-databases)

    Deze koppeling biedt de data frame-API voor het maken van verbinding met SQL-data bases met JDBC en het beheren van de parallellisme van Lees bewerkingen via de JDBC-interface. Dit onderwerp bevat gedetailleerde voor beelden van het gebruik van de scala-API, met verkorte python-en Spark SQL-voor beelden aan het einde.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Deze koppeling bevat voor beelden over het gebruik van de Azure Active Directory Service-Principal voor verificatie met Azure Data Lake Storage. Het bevat ook instructies voor het openen van toegang tot de gegevens in Azure Data Lake Storage van Azure Databricks.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Deze koppeling biedt voor beelden over hoe u rechtstreeks toegang krijgt tot Azure Blob Storage van Azure Databricks met behulp van toegangs sleutel of de SAS voor een bepaalde container. De koppeling bevat ook informatie over het openen van de Azure-Blob Storage van Azure Databricks met behulp van de RDD-API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Deze koppeling bevat instructies over het gebruik van de [Azure Cosmos DB Spark-connector](https://github.com/Azure/azure-cosmosdb-spark) van Azure Databricks om toegang te krijgen tot gegevens in azure Cosmos db.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Deze koppeling bevat instructies over het gebruik van de [azure Event hubs Spark-connector](https://github.com/Azure/azure-event-hubs-spark) van Azure Databricks om toegang te krijgen tot gegevens in azure Event hubs.

- [Azure SQL Data Warehouse](/azure/synapse-analytics/sql-data-warehouse/)

    Deze koppeling bevat instructies over het gebruik van de Azure SQL Data Warehouse connector om verbinding te maken vanuit Azure Databricks.
    

## <a name="next-steps"></a>Volgende stappen

Zie [gegevens bronnen voor Azure Databricks](/azure/databricks/data/data-sources/index)voor meer informatie over de bronnen waaruit u gegevens kunt importeren in azure Databricks.


