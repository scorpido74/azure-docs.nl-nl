---
title: Gegevens opvragen uit HDFS-compatibele Azure-opslag - Azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure-opslag en Azure Data Lake Storage om resultaten van uw analyse op te slaan.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 6a4ae2932f8d294ecf71de0ae405204a1f4d7b4d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436951"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure-opslag gebruiken met Azure HDInsight-clusters

Als u gegevens wilt analyseren in het HDInsight-cluster, u de gegevens opslaan in [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)of een combinatie. Met deze opslagopties u HDInsight-clusters die worden gebruikt voor berekening veilig verwijderen zonder gebruikersgegevens te verliezen.

Apache Hadoop ondersteunt een notie van het standaardbestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het proces voor het maken van HDInsight-cluster u een blobcontainer in Azure Storage opgeven als het standaardbestandssysteem of met HDInsight 3.6 u Azure Storage of Azure Data Lake Storage Gen 1/ Azure Data Lake Storage Gen 2 selecteren als het standaardbestandssysteem met een paar uitzonderingen. Zie [Beschikbaarheid voor het HDInsight-cluster voor](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)de ondersteuning van het gebruik van Data Lake Storage Gen 1 als zowel de standaardopslag als de gekoppelde opslag.

In dit artikel wordt uitgelegd hoe Azure Storage werkt met HDInsight-clusters. Zie [Azure Data Lake Storage gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-store.md)voor meer informatie over hoe Data Lake Storage Gen 1 werkt met HDInsight-clusters. Zie [Apache Hadoop-clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

> [!IMPORTANT]  
> **OpslagaccountblobStorage** kan alleen worden gebruikt als secundaire opslag voor HDInsight-clusters.

| Soort opslagaccount | Ondersteunde services | Ondersteunde prestatielagen |Niet ondersteunde prestatielagen| Ondersteunde toegangsniveaus |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (general-purpose v2)  | Blob     | Standard                    |Premium| Hot, Cool, Archief\*   |
| Storage (algemene doeleinden v1)   | Blob     | Standard                    |Premium| N.v.t.                    |
| BlobOpslag                    | Blob     | Standard                    |Premium| Hot, Cool, Archief\*   |

We raden u af de standaardblobcontainer te gebruiken voor het opslaan van bedrijfsgegevens. Het is een goede gewoonte om de standaard-blobcontainer na ieder gebruik te verwijderen om de opslagkosten te verlagen. De standaardcontainer bevat toepassings- en systeemlogboeken. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Het delen van één blobcontainer als het standaardbestandssysteem voor meerdere clusters wordt niet ondersteund.

> [!NOTE]  
> De categorie Archieftoegang is een offlinelaag met een latentie van enkele uren ophalen en wordt niet aanbevolen voor gebruik met HDInsight. Zie [Archieftoegangslaag](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)voor meer informatie .

## <a name="access-files-from-within-cluster"></a>Bestanden openen vanuit het cluster

Er zijn verschillende manieren waarop u toegang hebt tot de bestanden in Data Lake Storage vanuit een HDInsight-cluster. Het URI-schema biedt onversleutelde toegang (met de *wasb:* voorvoegsel) en TLS-versleutelde toegang (met *wasbs).* Waar mogelijk kunt u het beste *wasbs* gebruiken, zelfs voor de toegang tot gegevens die zich in dezelfde regio in Azure bevinden.

* **De volledig gekwalificeerde naam gebruiken**. Met deze methode geeft u het volledige pad op naar het bestand dat u wilt openen.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **De verkorte padnotatie gebruiken**. Met deze benadering vervangt u het pad naar de clusterroot door:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **Het relatieve pad gebruiken**. Met deze methode geeft u alleen het volledige relatieve pad op naar het bestand dat u wilt openen.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Voorbeelden van gegevenstoegang

Voorbeelden zijn gebaseerd op een [ssh-verbinding](./hdinsight-hadoop-linux-use-ssh-unix.md) met het hoofdknooppunt van het cluster. De voorbeelden maken gebruik van alle drie de URI-schema's. De `CONTAINERNAME` `STORAGEACCOUNT` relevante waarden vervangen en doorde desbetreffende waarden

#### <a name="a-few-hdfs-commands"></a>Een paar hdfs-opdrachten

1. Maak een eenvoudig bestand over lokale opslag.

    ```bash
    touch testFile.txt
    ```

1. Maak mappen over clusteropslag.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopieer gegevens uit lokale opslag naar clusteropslag.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Lijst inhoud van de map op clusteropslag.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Als u buiten HDInsight met blobs werkt, zullen de meeste hulpprogramma's de indeling WASB niet herkennen en wordt er in plaats daarvan een standaardpadindeling verwacht, zoals `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Een Hive-tabel maken

Drie bestandslocaties worden weergegeven voor illustratieve doeleinden. Gebruik voor de daadwerkelijke uitvoering `LOCATION` slechts één van de vermeldingen.

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

## <a name="access-files-from-outside-cluster"></a>Bestanden van buiten het cluster openen

Microsoft biedt de volgende hulpprogramma's om met Azure Storage te werken:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy (AzCopy)](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Opslagpad identificeren vanuit Ambari

* Als u het volledige pad naar het geconfigureerde standaardarchief wilt identificeren, navigeert u naar:

    **HDFS** > **Configs** `fs.defaultFS` en voer het filterinvoervak in.

* Als u wilt controleren of het wasb-archief is geconfigureerd als secundaire opslag, navigeert u naar:

    **HDFS** > **Configs** `blob.core.windows.net` en voer het filterinvoervak in.

Zie [De standaardopslag ophalen](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)als u het pad wilt verkrijgen met de Ambari REST API.

## <a name="blob-containers"></a>Blobcontainers

Als u blobs wilt gebruiken, maakt u eerst een [Azure Storage-account](../storage/common/storage-create-storage-account.md). Als onderdeel hiervan geeft u een Azure-regio op waar het opslagaccount wordt gemaakt. Het cluster en het opslagaccount moeten worden gehost in dezelfde regio. De Hive metastore SQL Server-database en Apache Oozie metastore SQL Server-database moeten zich ook in dezelfde regio bevinden.

Elke blob die u maakt, behoort tot een container in uw Azure Storage-account, ongeacht de locatie van de blob. Deze container kan een bestaande blob zijn die buiten HDInsight is gemaakt. Het kan echter ook een container zijn die is gemaakt voor een HDInsight-cluster.

In de standaard blobcontainer worden clusterspecifieke gegevens opgeslagen, zoals taakgeschiedenis en logboeken. Deel een standaard blob-container niet met meerdere HDInsight-clusters. Hierdoor kan de taakgeschiedenis beschadigd raken. Het wordt aanbevolen om voor elk cluster een andere container te gebruiken en gedeelde gegevens op een gekoppeld opslagaccount te plaatsen dat is opgegeven bij de implementatie van alle relevante clusters in plaats van het standaardopslagaccount. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie over het configureren van gekoppelde opslagaccounts. U kunt een standaardopslagcontainer echter opnieuw gebruiken nadat het oorspronkelijke HDInsight-cluster is verwijderd. Voor HBase-clusters u het schema en de gegevens van de HBase-tabel daadwerkelijk behouden door een nieuw HBase-cluster te maken met behulp van de standaardblobcontainer die wordt gebruikt door een HBase-cluster dat is verwijderd.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>Extra opslagaccounts gebruiken

Tijdens het maken van een HDInsight-cluster geeft u het Azure Storage-account op dat u ermee wilt koppelen. Naast dit opslagaccount kunt u tijdens het maakproces of nadat een cluster is gemaakt extra opslagaccounts toevoegen uit hetzelfde Azure-abonnement of uit andere Azure-abonnementen. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor instructies over het toevoegen van extra opslagaccounts.

> [!WARNING]  
> Het gebruik van een extra opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u HDFS-compatibele Azure-opslag kunt gebruiken met HDInsight. Zodoende kunt u een schaalbare, duurzame, archiveringsoplossing voor gegevensverzameling bouwen en HDInsight gebruiken om de informatie te ontsluiten in de opgeslagen gestructureerde en ongestructureerde gegevens.

Zie voor meer informatie:

* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Aan de slag met Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Handtekeningen voor gedeelde toegang van Azure Storage gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Zelfstudie: Gegevens extraheren, transformeren en laden met interactieve query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
