---
title: Azure Data Lake Storage Gen1 integreren met andere Azure-services | Microsoft Documenten
description: Begrijpen hoe Azure Data Lake Storage Gen1 integreert met andere Azure-services
documentationcenter: ''
services: data-lake-store
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 48a5d1f4-3850-4c22-bbc4-6d1d394fba8a
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 43024b63a355646f607adbb6623cc6c349374ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535592"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Azure Data Lake Storage Gen1 integreren met andere Azure-services
Azure Data Lake Storage Gen1 kan worden gebruikt in combinatie met andere Azure-services om een breder scala aan scenario's mogelijk te maken. In het volgende artikel worden de services weergegeven waarmee Data Lake Storage Gen1 kan worden geïntegreerd.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Data Lake Storage Gen1 gebruiken met Azure HDInsight
U een [Azure HDInsight-cluster](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) inrichten dat Data Lake Storage Gen1 gebruikt als de HDFS-compatibele opslag. Voor deze release, voor Hadoop en Storm clusters op Windows en Linux, u Data Lake Storage Gen1 alleen gebruiken als een extra opslag. Dergelijke clusters gebruiken nog steeds Azure Storage (WASB) als standaardopslag. Voor HBase-clusters op Windows en Linux u Echter Data Lake Storage Gen1 gebruiken als standaardopslag, extra opslag of beide.

Zie voor instructies over het inrichten van een HDInsight-cluster met Data Lake Storage Gen1:

* [Een HDInsight-cluster inrichten met Data Lake Storage Gen1 met Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Een HDInsight-cluster met Data Lake Storage Gen1 inrichten als standaardopslag met Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Een HDInsight-cluster met Data Lake Storage Gen1 inrichten als extra opslag met Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Data Lake Storage Gen1 gebruiken met Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) stelt u in staat om met Big Data te werken op cloudschaal. Het bevat dynamisch resources en laat u analyses uitvoeren op terabytes of zelfs exabytes aan gegevens die kunnen worden opgeslagen in een aantal ondersteunde gegevensbronnen, waaronder Data Lake Storage Gen1. Data Lake Analytics is speciaal geoptimaliseerd om te werken met Data Lake Storage Gen1 - het hoogste niveau van prestaties, doorvoer en parallelisatie voor u big data workloads.

Zie Aan de slag met Data Lake Analytics met Data Lake Storage Gen1 voor instructies over het gebruik van Data Lake Analytics met Data [Lake Storage Gen1.](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Data Lake Storage Gen1 gebruiken met Azure Data Factory
U [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) gebruiken om gegevens in te nemen uit Azure-tabellen, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage Blobs en on-premises databases. Als eerste klas burger in het Azure-ecosysteem kan Azure Data Factory worden gebruikt om de opname van gegevens uit deze bron te orkestreren naar Data Lake Storage Gen1.

Zie [Gegevens verplaatsen van en naar Data Lake Storage Gen1 met Data Lake Storage Gen1](../data-factory/connector-azure-data-lake-store.md)voor instructies over het gebruik van Azure Data Factory met Data Lake Storage Gen1.

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Gegevens uit Azure Storage Blobs kopiëren naar Data Lake Storage Gen1
Azure Data Lake Storage Gen1 biedt een command-line tool, AdlCopy, waarmee u gegevens uit Azure Blob Storage kopiëren naar een Data Lake Storage Gen1-account. Zie [Gegevens van Azure Storage Blobs kopiëren naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)voor meer informatie.

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Gegevens kopiëren tussen Azure SQL Database en Data Lake Storage Gen1
U Apache Sqoop gebruiken om gegevens te importeren en exporteren tussen Azure SQL Database en Data Lake Storage Gen1. Zie [Gegevens kopiëren tussen Data Lake Storage Gen1 en Azure SQL-database met Sqoop](data-lake-store-data-transfer-sql-sqoop.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Data Lake Storage Gen1 gebruiken met Stream Analytics
U Data Lake Storage Gen1 gebruiken als een van de uitvoerom gegevens op te slaan die worden gestreamd met Azure Stream Analytics. Zie [Gegevens uit Azure Storage Blob streamen naar Data Lake Storage Gen1 met Azure Stream Analytics](data-lake-store-stream-analytics.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Data Lake Storage Gen1 gebruiken met Power BI
U Power BI gebruiken om gegevens uit een Data Lake Storage Gen1-account te importeren om de gegevens te analyseren en te visualiseren. Zie [Gegevens analyseren in Data Lake Storage Gen1 met Power BI](data-lake-store-power-bi.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Data Lake Storage Gen1 gebruiken met gegevenscatalogus
U gegevens van Data Lake Storage Gen1 registreren in de Azure Data Catalog om de gegevens in de hele organisatie vindbaar te maken. Zie Gegevens [registreren van Data Lake Storage Gen1 in Azure Data Catalog](data-lake-store-with-data-catalog.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Data Lake Storage Gen1 gebruiken met SQL Server Integration Services (SSIS)
U de Data Lake Storage Gen1-verbindingsmanager in SSIS gebruiken om een SSIS-pakket te verbinden met Data Lake Storage Gen1. Zie [Data Lake Storage Gen1 gebruiken met SSIS](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-sql-data-warehouse"></a>Data Lake Storage Gen1 gebruiken met SQL Data Warehouse
U PolyBase gebruiken om gegevens van Data Lake Storage Gen1 te laden in SQL Data Warehouse. Zie Data [Lake Storage Gen1 gebruiken met SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)voor meer informatie.

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Data Lake Storage Gen1 gebruiken met Azure Event Hubs
U Azure Data Lake Storage Gen1 gebruiken om gegevens die zijn ontvangen door Azure Event Hubs te archiveren en vast te leggen. Zie Data [Lake Storage Gen1 gebruiken met Azure Event Hubs](data-lake-store-archive-eventhub-capture.md)voor meer informatie.

## <a name="see-also"></a>Zie ook
* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Aan de slag met Data Lake Storage Gen1 met Portal](data-lake-store-get-started-portal.md)
* [Aan de slag met Data Lake Storage Gen1 met PowerShell](data-lake-store-get-started-powershell.md)  

