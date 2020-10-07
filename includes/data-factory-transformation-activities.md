---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 5e7bdce3a195d8171fef1963f2e389bfeec5fffb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91571872"
---
Azure Data Factory ondersteunt de volgende transformaties, die individueel of gekoppeld aan andere activiteiten kunnen worden toegevoegd aan pijplijnen.

| Activiteiten voor gegevenstransformatie | Compute-omgeving |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop-streaming](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Machine Learning-activiteiten: batchuitvoering en resources bijwerken](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Opgeslagen procedure](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics of SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] of Azure Batch |

> [!NOTE]
> U kunt de MapReduce-activiteit gebruiken om Spark-programma's uit te voeren op uw HDInsight Spark-cluster. Zie [Spark-programma's aanroepen vanuit Azure Data Factory](../articles/data-factory/v1/data-factory-spark.md) voor meer informatie.
> U kunt een aangepaste activiteit maken om R-scripts uit te voeren op uw HDInsight-cluster waarop R is geïnstalleerd. Zie [R-script uitvoeren met behulp van Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

