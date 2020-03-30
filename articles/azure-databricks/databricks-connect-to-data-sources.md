---
title: 'Verbinding maken met verschillende gegevensbronnen van Azure Databricks '
description: Meer informatie over hoe u verbinding maken met Azure SQL Database, Azure Data Lake Store, blob-opslag, Cosmos DB, Event Hubs en Azure SQL Data Warehouse van Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 80ec9b2bbf10d8f500e7f9e0369faca3b9663633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129385"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Verbinding maken met gegevensbronnen van Azure Databricks

In dit artikel vindt u koppelingen naar alle verschillende gegevensbronnen in Azure die kunnen worden gekoppeld aan Azure Databricks. Volg de voorbeelden in deze koppelingen om gegevens uit de Azure-gegevensbronnen (bijvoorbeeld Azure Blob Storage, Azure Event Hubs, enz.) naar een Azure Databricks-cluster te extraheren en er analytische taken op uit te voeren. 

## <a name="prerequisites"></a>Vereisten

* U moet beschikken over een Azure Databricks-werkruimte en een Spark-cluster. Volg de instructies bij [Aan de slag met Azure Databricks.](quickstart-create-databricks-workspace-portal.md)

## <a name="data-sources-for-azure-databricks"></a>Gegevensbronnen voor Azure Databricks

In de volgende lijst worden de gegevensbronnen in Azure weergegeven die u gebruiken met Azure Databricks. Zie [Gegevensbronnen voor Azure Databricks voor](/azure/databricks/data/data-sources/index)een volledige lijst met gegevensbronnen die met Azure Databricks kunnen worden gebruikt.

- [Azure SQL-database](/azure/databricks/data/data-sources/sql-databases)

    Deze koppeling biedt de DataFrame API voor het verbinden met SQL-databases met JDBC en hoe u het parallellisme van reads via de JDBC-interface beheren. Dit onderwerp bevat gedetailleerde voorbeelden met behulp van de Scala API, met verkorte Python- en Spark SQL-voorbeelden aan het einde.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Deze koppeling bevat voorbeelden over het gebruik van de Azure Active Directory-serviceprincipal om te verifiëren met Azure Data Lake Storage. Het bevat ook instructies over hoe u toegang krijgt tot de gegevens in Azure Data Lake Storage vanuit Azure Databricks.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Deze koppeling bevat voorbeelden over hoe u rechtstreeks toegang krijgt tot Azure Blob Storage vanuit Azure Databricks met behulp van toegangssleutel of de SAS voor een bepaalde container. De koppeling bevat ook informatie over hoe u toegang krijgt tot de Azure Blob Storage vanuit Azure Databricks met behulp van de RDD-API.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Deze koppeling bevat instructies voor het gebruik van de [Azure Cosmos DB Spark-connector](https://github.com/Azure/azure-cosmosdb-spark) van Azure Databricks om toegang te krijgen tot gegevens in Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Deze koppeling bevat instructies voor het gebruik van de [Azure Event Hubs Spark-connector](https://github.com/Azure/azure-event-hubs-spark) van Azure Databricks om toegang te krijgen tot gegevens in Azure Event Hubs.

- [Azure SQL-gegevensmagazijn](/azure/databricks/data/data-sources/azure/sql-data-warehouse)

    Deze koppeling bevat instructies voor het gebruik van de Azure SQL Data Warehouse-connector om verbinding te maken vanuit Azure Databricks.
    

## <a name="next-steps"></a>Volgende stappen

Zie [Gegevensbronnen voor Azure Databricks voor](/azure/databricks/data/data-sources/index)meer informatie over bronnen van waaruit u gegevens importeren in Azure Databricks.


