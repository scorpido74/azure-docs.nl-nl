---
title: Gegevens uploaden voor Apache Hadoop-taken in HDInsight
description: Meer informatie over het uploaden en openen van gegevens voor Apache Hadoop-taken in HDInsight met behulp van de klassieke Azure-CLI, Azure Storage Explorer, Azure PowerShell, de Hadoop-opdracht regel of Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdiseo17may2017
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 7eb1f7e1ce02a30f84cb520438f60fcbcfa3a965
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100131"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Gegevens uploaden voor Apache Hadoop-taken in HDInsight

Azure HDInsight biedt een volledig functionele Hadoop Distributed File System (HDFS) over Azure Storage en Azure Data Lake Storage (gen1 en Gen2). Azure Storage en Data Lake Storage Gen1 en Gen2 zijn ontworpen als HDFS-uitbrei dingen om klanten een naadloze ervaring te bieden. Ze kunnen de volledige set onderdelen in het Hadoop-ecosysteem rechtstreeks op de door IT beheerde gegevens gebruiken. Azure Storage, Data Lake Storage Gen1 en Gen2 zijn afzonderlijke bestands systemen die geoptimaliseerd zijn voor opslag van gegevens en berekeningen voor die gegevens. Zie [Azure Storage gebruiken met hdinsight](hdinsight-hadoop-use-blob-storage.md), [Data Lake Storage gen1 met hdinsight](hdinsight-hadoop-use-data-lake-store.md)gebruiken en [Data Lake Storage Gen2 gebruiken met hdinsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)voor meer informatie over de voor delen van het gebruik van Azure Storage.

## <a name="prerequisites"></a>Vereisten

Houd rekening met de volgende vereisten voordat u begint:

* Een Azure HDInsight-cluster. Zie [aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md) of HDInsight- [clusters maken](hdinsight-hadoop-provision-linux-clusters.md)voor instructies.
* Kennis van de volgende artikelen:
    * [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Data Lake Storage Gen1 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-store.md)
    * [Data Lake Storage Gen2 gebruiken met HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Gegevens uploaden naar Azure Storage

## <a name="utilities"></a>Nutsbedrijven

Micro soft biedt de volgende hulpprogram ma's voor het werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-opdracht](#commandline) |✔ |✔ |✔ |

> [!NOTE]  
> De Hadoop-opdracht is alleen beschikbaar op het HDInsight-cluster. Met deze opdracht kunt u alleen gegevens laden van het lokale bestands systeem naar Azure Storage.  

## <a id="commandline"></a>Hadoop-opdracht regel

De Hadoop-opdracht regel kan alleen worden gebruikt voor het opslaan van gegevens in een Azure Storage-BLOB wanneer de gegevens al aanwezig zijn op het hoofd knooppunt van het cluster.

Als u de Hadoop-opdracht wilt gebruiken, moet u eerst verbinding maken met de hoofd knooppunt met behulp van [SSH of putty](hdinsight-hadoop-linux-use-ssh-unix.md).

Als de verbinding tot stand is gebracht, kunt u de volgende syntaxis gebruiken om een bestand te uploaden naar de opslag.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Bijvoorbeeld: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Omdat het standaard bestandssysteem voor HDInsight zich in Azure Storage bevindt, is/example/data/data.txt daad werkelijk in Azure Storage. U kunt ook naar het bestand verwijzen als:

    wasbs:///example/data/data.txt

of

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Zie [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html) voor een lijst met andere Hadoop-opdrachten die met bestanden werken.

> [!WARNING]  
> In Apache HBase-clusters is de standaard blok grootte die wordt gebruikt voor het schrijven van gegevens 256 KB. Dit werkt prima wanneer u HBase-Api's of REST-Api's gebruikt, met behulp van de `hadoop`-of `hdfs dfs`-opdrachten voor het schrijven van gegevens die groter zijn dan ~ 12 GB resulteert in een fout. Zie de sectie [opslag uitzondering voor schrijven op BLOB](#storageexception) in dit artikel voor meer informatie.

## <a name="graphical-clients"></a>Grafische clients

Er zijn ook verschillende toepassingen die een grafische interface bieden voor het werken met Azure Storage. De volgende tabel bevat een lijst met enkele van deze toepassingen:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Micro soft Visual Studio Tools voor HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure-opslagverkenner](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [Cerulea](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer voor Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Azure Storage koppelen als lokale station

Zie [Azure Storage koppelen als lokale schijf](https://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="upload-using-services"></a>Uploaden met Services

### <a name="azure-data-factory"></a>Azure Data Factory

De Azure Data Factory-service is een volledig beheerde service voor het samen stellen van services voor gegevens opslag, gegevens verwerking en gegevens verplaatsing naar gestroomlijnde, schaal bare en betrouw bare gegevens productie pijplijnen.

|Opslagtype|Documentatie|
|----|----|
|Azure Blob Storage|[Gegevens kopiëren naar of van Azure Blob-opslag met behulp van Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Gegevens kopiëren van of naar Azure Data Lake Storage Gen1 met behulp van Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Gegevens laden in Azure Data Lake Storage Gen2 met Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a id="sqoop"></a>Apache Sqoop

Sqoop is een hulp programma dat is ontworpen om gegevens over te dragen tussen Hadoop en relationele data bases. U kunt dit gebruiken om gegevens te importeren uit een relationele Database Management System (RDBMS), zoals SQL Server, MySQL of Oracle in het Hadoop Distributed File System (HDFS), de gegevens in Hadoop te transformeren met MapReduce of Hive en vervolgens de gegevens terug te exporteren naar een RDBMS.

Zie [Sqoop gebruiken met HDInsight](hadoop/hdinsight-use-sqoop.md)voor meer informatie.

### <a name="development-sdks"></a>Ontwikkel aars van Sdk's

Azure Storage kan ook worden geopend met behulp van een Azure SDK vanuit de volgende programmeer talen:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Voor meer informatie over het installeren van de Azure-Sdk's raadpleegt u [Azure down loads](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Problemen oplossen

### <a id="storageexception"></a>Opslag uitzondering voor schrijven op BLOB

**Symptomen**: wanneer u de `hadoop`-of `hdfs dfs`-opdrachten gebruikt om bestanden te schrijven die ~ 12 GB of groter zijn op een HBase-cluster, kunt u de volgende fout tegen komen:

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

**Oorzaak**: HBase op HDInsight-clusters standaard ingesteld op een blok grootte van 256 kB bij het schrijven naar Azure Storage. Hoewel het werkt voor HBase-Api's of REST-Api's, resulteert dit in een fout bij het gebruik van de `hadoop` of `hdfs dfs` opdracht regel Programma's.

**Oplossing**: gebruik `fs.azure.write.request.size` om een grotere blok grootte op te geven. U kunt dit doen op basis van het gebruik van de para meter `-D`. De volgende opdracht is een voor beeld van het gebruik van deze para meter met de opdracht `hadoop`:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

U kunt ook de waarde van `fs.azure.write.request.size` globaal verhogen door gebruik te maken van Apache Ambari. De volgende stappen kunnen worden gebruikt om de waarde in de Ambari-webgebruikersinterface te wijzigen:

1. Ga in uw browser naar de Ambari-webgebruikersinterface voor uw cluster. Dit is `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van het cluster is.

    Wanneer u hierom wordt gevraagd, voert u de naam en het wacht woord van de beheerder voor het cluster in.
2. Klik aan de linkerkant van het scherm op **HDFS**en selecteer vervolgens het tabblad **configuratie** .
3. Voer `fs.azure.write.request.size`in het veld **filter...** in. Hiermee worden het veld en de huidige waarde in het midden van de pagina weer gegeven.
4. Wijzig de waarde van 262144 (256 KB) in de nieuwe waarde. Bijvoorbeeld 4194304 (4 MB).

    ![Afbeelding van het wijzigen van de waarde via Ambari Web UI](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)voor meer informatie over het gebruik van Ambari.

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u gegevens in HDInsight kunt ophalen, lees dan de volgende artikelen voor meer informatie over het uitvoeren van analyses:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-taken via een programma indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hadoop/hdinsight-use-pig.md)
