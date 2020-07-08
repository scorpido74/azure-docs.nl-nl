---
title: Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de Azure-Services die met Azure Data Lake Storage Gen2 worden geïntegreerd
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1854c6f86e1a3578b6314b072f104174032344e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907581"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2

U kunt Azure-Services gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. In dit artikel vindt u een lijst met ondersteunde Azure-Services, wordt het bijbehorende ondersteunings niveau afgesloten en vindt u koppelingen naar artikelen die u helpen bij het gebruik van deze services met Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Ondersteunde Azure-services

Deze tabel geeft een lijst van de Azure-Services die u kunt gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weer gegeven, worden in de loop van de tijd gewijzigd, omdat de ondersteuning blijft toenemen.

> [!NOTE]
> Ondersteunings niveau verwijst alleen naar de manier waarop de service wordt ondersteund met Data Lake Storage gen 2.

|Azure-service |Ondersteunings niveau |Verwante artikelen: |
|---------------|-------------------|---|
|Azure Data Factory|Algemeen verkrijgbaar|[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Algemeen verkrijgbaar|[Gebruiken met Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Snelstartgids: gegevens in Azure Data Lake Storage Gen2 analyseren met behulp van Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Zelfstudie: Gegevens extraheren, transformeren en laden met Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Zelf studie: toegang tot Data Lake Storage Gen2 gegevens met Azure Databricks met Spark](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Algemeen verkrijgbaar|[Gebeurtenissen vastleggen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Algemeen verkrijgbaar|[Zelf studie: het data Lake Capture-patroon implementeren om een Databricks Delta tabel bij te werken](data-lake-storage-events.md)|
|Azure Logic Apps|Algemeen verkrijgbaar|[Overzicht - Wat is Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Algemeen verkrijgbaar|[Toegang tot gegevens in azure Storage-services](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Algemeen verkrijgbaar|[Snelstart: Een Stream Analytics-taak maken via Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Uitgaand naar Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Algemeen verkrijgbaar|[Gebruik Azure Data Box om gegevens van een on-premises HDFS-Store te migreren naar Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Algemeen verkrijgbaar|[Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[De HDFS CLI gebruiken met Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Zelf studie: gegevens extra heren, transformeren en laden met behulp van Apache Hive in azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Algemeen verkrijgbaar|[IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Algemeen verkrijgbaar|[Gegevens analyseren in Data Lake Storage Gen2 met behulp van Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)|Algemeen verkrijgbaar|[Gebruiken met Azure Synapse Analytics (voorheen SQL Data Warehouse)](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Algemeen verkrijgbaar|[Verbindings beheer Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Preview|[Index-en zoek Azure Data Lake Storage Gen2 documenten (preview-versie)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Preview|[Query's uitvoeren op gegevens in Azure Data Lake met behulp van Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Nog niet ondersteund|[Index-en zoek Azure Data Lake Storage Gen2 documenten (preview-versie)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open-source platforms die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Toegang met meerdere protocollen in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)