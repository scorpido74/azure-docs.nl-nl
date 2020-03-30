---
title: Gegevens uploaden voor Apache Hadoop-taken in HDInsight
description: Meer informatie over het uploaden en openen van gegevens voor Apache Hadoop-taken in HDInsight met behulp van de Azure-klassieker CLI, Azure Storage Explorer, Azure PowerShell, de hadoop-opdrachtregel of Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73100131"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Gegevens uploaden voor Apache Hadoop-taken in HDInsight

Azure HDInsight biedt een volledig uitgerust Hadoop distributed file system (HDFS) via Azure Storage en Azure Data Lake Storage (Gen1 en Gen2). Azure Storage and Data Lake Storage Gen1 en Gen2 zijn ontworpen als HDFS-extensies om klanten een naadloze ervaring te bieden. Ze stellen de volledige set componenten in het Hadoop-ecosysteem in staat om direct te werken op de gegevens die het beheert. Azure Storage, Data Lake Storage Gen1 en Gen2 zijn afzonderlijke bestandssystemen die zijn geoptimaliseerd voor de opslag van gegevens en berekeningen op die gegevens. Zie [Azure Storage gebruiken met HDInsight,](hdinsight-hadoop-use-blob-storage.md)Data Lake [Storage Gen1 met HDInsight](hdinsight-hadoop-use-data-lake-store.md)en [Data Lake Storage Gen2 gebruiken met HDInsight en Data Lake Storage Gen2 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie over de voordelen van het gebruik van Azure Storage.

## <a name="prerequisites"></a>Vereisten

Let op de volgende vereisten voordat u begint:

* Een Azure HDInsight-cluster. Zie Aan [de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) of [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)voor instructies.
* Kennis van de volgende artikelen:
    * [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Data Lake Storage Gen1 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Data Lake Storage Gen2 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Gegevens uploaden naar Azure Storage

## <a name="utilities"></a>Hulpprogramma's

Microsoft biedt de volgende hulpprogramma's om te werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy (AzCopy)](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop, opdracht](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> De opdracht Hadoop is alleen beschikbaar op het HDInsight-cluster. Met de opdracht kunnen alleen gegevens uit het lokale bestandssysteem worden geladen in Azure Storage.  

## <a name="hadoop-command-line"></a><a id="commandline"></a>Hadoop-opdrachtregel

De opdrachtregel Hadoop is alleen handig voor het opslaan van gegevens in Azure-opslagblob wanneer de gegevens al aanwezig zijn op het clusterhoofdknooppunt.

Om de opdracht Hadoop te gebruiken, moet u eerst verbinding maken met de headnode met [SSH of PuTTY.](hdinsight-hadoop-linux-use-ssh-unix.md)

Nadat u verbinding hebt gemaakt, u de volgende syntaxis gebruiken om een bestand naar opslag te uploaden.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Bijvoorbeeld: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Omdat het standaardbestandssysteem voor HDInsight zich in Azure Storage bevindt, bevindt /example/data/data.txt zich in Azure Storage. U het bestand ook als:

    wasbs:///example/data/data.txt

of

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Zie voor een lijst met andere Hadoop-opdrachten die met bestanden werken,[https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Op Apache HBase-clusters is de standaardblokgrootte die wordt gebruikt bij het schrijven van gegevens 256 KB. Hoewel dit prima werkt bij het gebruik van HBase API's of REST API's, resulteert het gebruik van de `hadoop` of `hdfs dfs` opdrachten om gegevens te schrijven groter dan ~ 12 GB in een fout. Zie de [opslaguitzondering voor schrijf op blobsectie](#storageexception) in dit artikel voor meer informatie.

## <a name="graphical-clients"></a>Grafische clients

Er zijn ook verschillende toepassingen die een grafische interface bieden voor het werken met Azure Storage. De volgende tabel is een lijst van enkele van deze toepassingen:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-hulpprogramma's voor HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea (Cerulea)](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer voor Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage als lokaal station monteren

Zie [Azure Storage monteren als lokaal station](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Uploaden met behulp van services

### <a name="azure-data-factory"></a>Azure Data Factory

De Azure Data Factory-service is een volledig beheerde service voor het samenstellen van gegevensopslag-, gegevensverwerkings- en gegevensverplaatsingsservices tot gestroomlijnde, schaalbare en betrouwbare pijplijnen voor gegevensproductie.

|Opslagtype|Documentatie|
|----|----|
|Azure Blob Storage|[Gegevens kopiëren naar of vanuit Azure Blob-opslag met Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Gegevens kopiëren naar of van Azure Data Lake Storage Gen1 met Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a><a id="sqoop"></a>Apache Sqoop

Sqoop is een tool ontworpen om gegevens over te dragen tussen Hadoop en relationele databases. U het gebruiken om gegevens uit een relationeel databasebeheersysteem (RDBMS) te importeren, zoals SQL Server, MySQL of Oracle in het Hadoop distributed file system (HDFS), de gegevens in Hadoop om te zetten met MapReduce of Hive en de gegevens vervolgens terug te exporteren naar een RDBMS.

Zie [Sqoop gebruiken met HDInsight](hadoop/hdinsight-use-sqoop.md)voor meer informatie.

### <a name="development-sdks"></a>Ontwikkeling SDKs

Azure Storage is ook toegankelijk via een Azure SDK vanuit de volgende programmeertalen:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Zie [Azure-downloads](https://azure.microsoft.com/downloads/) voor meer informatie over het installeren van de Azure SDKs

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="storage-exception-for-write-on-blob"></a><a id="storageexception"></a>Opslaguitzondering voor schrijven op blob

**Symptomen:** Wanneer u `hadoop` `hdfs dfs` de of opdrachten gebruikt om bestanden te schrijven die ~ 12 GB of groter zijn op een HBase-cluster, u de volgende fout tegenkomen:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Oorzaak**: HBase op HDInsight-clusters standaard tot een blokgrootte van 256 KB bij het schrijven naar Azure-opslag. Hoewel het werkt voor HBase API's of REST API's, resulteert dit in een fout bij het gebruik van de `hadoop` of `hdfs dfs` command-line hulpprogramma's.

**Resolutie:** `fs.azure.write.request.size` gebruik een grotere blokgrootte opgeven. U dit per gebruik doen met `-D` behulp van de parameter. De volgende opdracht is een voorbeeld `hadoop` met deze parameter met de opdracht:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

U ook de `fs.azure.write.request.size` waarde van wereldwijd verhogen met Apache Ambari. De volgende stappen kunnen worden gebruikt om de waarde in de Ambari-webgebruikersinterface te wijzigen:

1. Ga in uw browser naar de Gebruikersinterface van Ambari Web voor uw cluster. Dit `https://CLUSTERNAME.azurehdinsight.net`is `CLUSTERNAME` , waar is de naam van uw cluster.

    Voer de naam en het wachtwoord voor het cluster in wanneer u daarom wordt gevraagd.
2. Selecteer **hdfs**aan de linkerkant van het scherm en selecteer vervolgens het tabblad **Configs.**
3. Voer in het veld `fs.azure.write.request.size` **Filter...** de enter in . Hiermee worden het veld en de huidige waarde in het midden van de pagina weergegeven.
4. Wijzig de waarde van 262144 (256 KB) naar de nieuwe waarde. Bijvoorbeeld 4194304 (4 MB).

    ![Afbeelding van het wijzigen van de waarde via de Gebruikersinterface van Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Zie [HDInsight-clusters beheren met behulp van de Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)voor meer informatie over het gebruik van Ambari.

## <a name="next-steps"></a>Volgende stappen

Nu u begrijpt hoe u gegevens in HDInsight krijgen, leest u de volgende artikelen om te leren hoe u analyses uitvoeren:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-taken programmatisch indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hadoop/hdinsight-use-pig.md)
