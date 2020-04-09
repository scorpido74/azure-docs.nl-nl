---
title: Azure-services die Azure Data Lake Storage Gen2 ondersteunen | Microsoft Documenten
description: Meer informatie over welke Azure-services integreren met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878320"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure-services die Azure Data Lake Storage Gen2 ondersteunen

U Azure-services gebruiken om gegevens in te nemen, analyses uit te voeren en visuele weergaven te maken. In dit artikel vindt u een lijst met ondersteunde Azure-services, wordt hun ondersteuningsniveau bekendgemaakt en wordt u koppelingen aangeboden naar artikelen waarmee u deze services gebruiken met Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Ondersteunde Azure-services

In deze tabel worden de Azure-services weergegeven die u gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weergegeven, veranderen na verloop van tijd naarmate de ondersteuning wordt uitgebreid.

> [!NOTE]
> Ondersteuningsniveau verwijst alleen naar de manier waarop de service wordt ondersteund met Data Lake Storage Gen 2.

|Azure-service |Ondersteuningsniveau |Verwante artikelen: |
|---------------|-------------------|---|
|Azure Data Factory|Algemeen verkrijgbaar|[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Algemeen verkrijgbaar|[Gebruiken met Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Snelstart: gegevens analyseren in Azure Data Lake Storage Gen2 met Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Zelfstudie: Gegevens extraheren, transformeren en laden met Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Zelfstudie: Toegang tot Data Lake Storage Gen2-gegevens met Azure Databricks met Spark](data-lake-storage-use-databricks-spark.md)|
|Azure Event Hub|Algemeen verkrijgbaar|[Gebeurtenissen vastleggen via Azure Event Hubs in Azure Blob Storage of Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Algemeen verkrijgbaar|[Zelfstudie: Het patroon voor het vastleggen van gegevensmeer implementeren om een Tabel Met Gegevensbricks Delta bij te werken](data-lake-storage-events.md)|
|Azure Logic Apps|Algemeen verkrijgbaar|[Overzicht - Wat is Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Algemeen verkrijgbaar|[Toegang tot gegevens in Azure-opslagservices](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Stream Analytics|Algemeen verkrijgbaar|[Snelstart: Een Stream Analytics-taak maken via Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Uitgang naar Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Algemeen verkrijgbaar|[Azure Data Box gebruiken om gegevens van een on-premises HDFS-winkel te migreren naar Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Algemeen verkrijgbaar|[Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[De HDFS CLI gebruiken met Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Zelfstudie: Gegevens extraheren, transformeren en laden met Apache Hive op Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|IoT Hub |Algemeen verkrijgbaar|[IoT Hub-berichtroutering gebruiken om device-to-cloudberichten naar verschillende eindpunten te verzenden](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Algemeen verkrijgbaar|[Gegevens analyseren in Data Lake Storage Gen2 met Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Algemeen verkrijgbaar|[Gebruiken met Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS)|Algemeen verkrijgbaar|[Azure Storage-verbindingsbeheer](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Preview|[Azure Data Lake Storage Gen2-documenten indexeren en doorzoeken (voorbeeld)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Preview|[Querygegevens in Azure Data Lake met Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Nog niet ondersteund|[Azure Data Lake Storage Gen2-documenten indexeren en doorzoeken (voorbeeld)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Blob-opslagfuncties beschikbaar in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Open source-platforms die Azure Data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md)
- [Toegang met meerdere protocollen op Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)