---
title: Gegevens opvragen uit HDFS-compatibele Azure-opslag - Azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure Storage en Azure Data Lake Storage om de resultaten van uw analyse op te slaan.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: f496f6c06d36f817b0a933bdc68d5c53f308e3f2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78192622"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure-opslag gebruiken met Azure HDInsight-clusters

Als u gegevens in een HDInsight-cluster wilt analyseren, kunt u de gegevens opslaan in [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure data Lake Storage gen 2](../storage/blobs/data-lake-storage-introduction.md)of een combi natie hiervan. Met deze opslag opties kunt u HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikers gegevens verloren gaan.

Apache Hadoop ondersteunt een principe van het standaard bestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het maken van het HDInsight-cluster kunt u een BLOB-container opgeven in Azure Storage als het standaard bestandssysteem, of met HDInsight 3,6, kunt u Azure Storage of Azure Data Lake Storage gen 1/Azure Data Lake Storage gen 2 selecteren als het standaard bestanden systeem met enkele uitzonde ringen. Zie [Beschik baarheid voor HDInsight-cluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)voor de ondersteuning van het gebruik van data Lake Storage gen 1 als standaard-en gekoppelde opslag.

In dit artikel wordt uitgelegd hoe Azure Storage werkt met HDInsight-clusters. Zie [Azure data Lake Storage gebruiken met Azure hdinsight-clusters](hdinsight-hadoop-use-data-lake-store.md)voor meer informatie over hoe data Lake Storage gen 1 werkt met hdinsight-clusters. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

> [!IMPORTANT]  
> Type opslag account **BlobStorage** kan alleen worden gebruikt als secundaire opslag voor HDInsight-clusters.

| Type opslag account | Ondersteunde services | Ondersteunde prestatie lagen | Ondersteunde toegangs lagen |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (general-purpose v2)  | Blob     | Standard                    | Hot, cool, Archive\*   |
| Opslag (algemeen gebruik v1)   | Blob     | Standard                    | N.v.t.                    |
| BlobStorage                    | Blob     | Standard                    | Hot, cool, Archive\*   |

Het is niet raadzaam om de standaard BLOB-container te gebruiken voor het opslaan van Bedrijfs gegevens. Het is een goede gewoonte om de standaard-blobcontainer na ieder gebruik te verwijderen om de opslagkosten te verlagen. De standaard container bevat toepassings-en systeem Logboeken. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Het delen van één BLOB-container als het standaard bestandssysteem voor meerdere clusters wordt niet ondersteund.

> [!NOTE]  
> De Access-laag voor archivering is een offline laag met een aantal latentie voor het ophalen van uur en wordt niet aanbevolen voor gebruik met HDInsight. Zie [Access-laag archiveren](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)voor meer informatie.

## <a name="access-files-from-within-cluster"></a>Toegang tot bestanden in het cluster

Er zijn verschillende manieren om toegang te krijgen tot de bestanden in Data Lake Storage vanuit een HDInsight-cluster. Het URI-schema biedt niet-versleutelde toegang (met het voorvoegsel *wasb:* ) en SSL-versleutelde toegang (met *wasbs*). Waar mogelijk kunt u het beste *wasbs* gebruiken, zelfs voor de toegang tot gegevens die zich in dezelfde regio in Azure bevinden.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **De verkorte padnotatie gebruiken**. Met deze methode vervangt u het pad naar de hoofdmap van het cluster met:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voor beelden van gegevens toegang

Voor beelden zijn gebaseerd op een [SSH-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofd knooppunt van het cluster. De voor beelden gebruiken alle drie de URI-schema's. `CONTAINERNAME` en `STORAGEACCOUNT` vervangen door de relevante waarden

#### <a name="a-few-hdfs-commands"></a>Enkele hdfs-opdrachten

1. Maak een eenvoudig bestand op lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen in de cluster opslag.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Gegevens uit de lokale opslag kopiëren naar de cluster opslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Mapinhoud weer geven in cluster opslag.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Als u buiten HDInsight met blobs werkt, zullen de meeste hulpprogramma's de indeling WASB niet herkennen en wordt er in plaats daarvan een standaardpadindeling verwacht, zoals `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Er worden drie bestands locaties weer gegeven voor illustratie doeleinden. Gebruik slechts één van de `LOCATION` vermeldingen voor daad werkelijke uitvoering.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Toegang tot bestanden buiten het cluster

Micro soft biedt de volgende hulpprogram ma's voor het werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Pad naar opslag van Ambari identificeren

* Als u het volledige pad naar het geconfigureerde standaard archief wilt identificeren, gaat u naar:

    **HDFS** > **configuraties** en voer `fs.defaultFS` in het vak invoer filteren.

* Als u wilt controleren of de wasb-opslag is geconfigureerd als secundaire opslag, gaat u naar:

    **HDFS** > **configuraties** en voer `blob.core.windows.net` in het vak invoer filteren.

Zie [de standaard opslag ophalen](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)om het pad op te halen met behulp van Ambari rest API.

## <a name="blob-containers"></a>Blobcontainers

Als u blobs wilt gebruiken, maakt u eerst een [Azure Storage-account](../storage/common/storage-create-storage-account.md). Als onderdeel hiervan geeft u een Azure-regio op waar het opslagaccount wordt gemaakt. Het cluster en het opslagaccount moeten worden gehost in dezelfde regio. De Hive-metastore SQL Server-Data Base en Apache Oozie-meta Store SQL Server-Data Base moeten zich ook in dezelfde regio bevinden.

Elke blob die u maakt, behoort tot een container in uw Azure Storage-account, ongeacht de locatie van de blob. Deze container kan een bestaande blob zijn die buiten HDInsight is gemaakt. Het kan echter ook een container zijn die is gemaakt voor een HDInsight-cluster.

In de standaard blobcontainer worden clusterspecifieke gegevens opgeslagen, zoals taakgeschiedenis en logboeken. Deel een standaard blob-container niet met meerdere HDInsight-clusters. Hierdoor kan de taakgeschiedenis beschadigd raken. Het is raadzaam om voor elk cluster een andere container te gebruiken en gedeelde gegevens te plaatsen op een gekoppeld opslag account dat is opgegeven in de implementatie van alle relevante clusters in plaats van het standaard opslag account. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het configureren van gekoppelde opslag accounts. U kunt een standaardopslagcontainer echter opnieuw gebruiken nadat het oorspronkelijke HDInsight-cluster is verwijderd. Voor HBase-clusters kunt u het HBase-tabel schema en de gegevens in werkelijkheid blijven door een nieuw HBase-cluster te maken met behulp van de standaard-BLOB-container die wordt gebruikt door een HBase-cluster dat is verwijderd.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Extra opslagaccounts gebruiken

Tijdens het maken van een HDInsight-cluster geeft u het Azure Storage-account op dat u ermee wilt koppelen. Naast dit opslagaccount kunt u tijdens het maakproces of nadat een cluster is gemaakt extra opslagaccounts toevoegen uit hetzelfde Azure-abonnement of uit andere Azure-abonnementen. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor instructies over het toevoegen van extra opslagaccounts.

> [!WARNING]  
> Het gebruik van een extra opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u HDFS-compatibele Azure-opslag kunt gebruiken met HDInsight. Zodoende kunt u een schaalbare, duurzame, archiveringsoplossing voor gegevensverzameling bouwen en HDInsight gebruiken om de informatie te ontsluiten in de opgeslagen gestructureerde en ongestructureerde gegevens.

Ga voor meer informatie naar:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Aan de slag met Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Azure Storage-hand tekeningen voor gedeelde toegang gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Zelf studie: gegevens uitpakken, transformeren en laden met interactieve Query's in azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
