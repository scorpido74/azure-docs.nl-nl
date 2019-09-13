---
title: Gegevens opvragen uit HDFS-compatibele Azure-opslag - Azure HDInsight
description: Meer informatie over het opvragen van gegevens uit Azure Storage en Azure Data Lake Storage om de resultaten van uw analyse op te slaan.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 5d287165e77597943d298178689c216497361570
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879663"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure-opslag gebruiken met Azure HDInsight-clusters

Als u gegevens in een HDInsight-cluster wilt analyseren, kunt u de gegevens opslaan in [Azure Storage](../storage/common/storage-introduction.md), [Azure data Lake Storage gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure data Lake Storage gen 2](../storage/blobs/data-lake-storage-introduction.md)of een combi natie hiervan. Met deze opslag opties kunt u HDInsight-clusters die worden gebruikt voor berekeningen, veilig verwijderen zonder dat er gebruikers gegevens verloren gaan.

Apache Hadoop ondersteunt een principe van het standaard bestandssysteem. Het standaardbestandssysteem impliceert een standaardschema en instantie. De toepassing kan ook worden gebruikt om relatieve paden om te zetten. Tijdens het maken van het HDInsight-cluster kunt u een BLOB-container opgeven in Azure Storage als het standaard bestandssysteem, of met HDInsight 3,6, kunt u Azure Storage of Azure Data Lake Storage gen 1/Azure Data Lake Storage gen 2 als de standaard bestanden selecteren systeem met enkele uitzonde ringen. Zie [Beschik baarheid voor HDInsight-cluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)voor de ondersteuning van het gebruik van data Lake Storage gen 1 als standaard-en gekoppelde opslag.

In dit artikel wordt uitgelegd hoe Azure Storage werkt met HDInsight-clusters. Zie [Azure data Lake Storage gebruiken met Azure hdinsight-clusters](hdinsight-hadoop-use-data-lake-store.md)voor meer informatie over hoe data Lake Storage gen 1 werkt met hdinsight-clusters. Zie [Apache Hadoop clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een HDInsight-cluster.

Azure Storage is een robuuste, algemene opslagoplossing die naadloos kan worden geïntegreerd met HDInsight. HDInsight kan een blobcontainer in Azure Storage gebruiken als het standaardbestandssysteem voor het cluster. Via een HDFS-interface (Hadoop Distributed File System) kan de volledige set onderdelen in HDInsight rechtstreeks als blobs op gestructureerde of ongestructureerde gegevens worden uitgevoerd.

> [!WARNING]  
> Type opslag account **BlobStorage** kan alleen worden gebruikt als secundaire opslag voor HDInsight-clusters.

| Type opslag account | Ondersteunde services | Ondersteunde prestatie lagen | Ondersteunde toegangs lagen |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (general-purpose v2)  | Blob     | Standard                    | Hot, cool, Archive\*   |
| Opslag (algemeen gebruik v1)   | Blob     | Standard                    | N/A                    |
| BlobStorage                    | Blob     | Standard                    | Hot, cool, Archive\*   |

Het wordt afgeraden om de standaard- blobcontainer te gebruiken voor het opslaan van bedrijfsgegevens. Het is een goede gewoonte om de standaard-blobcontainer na ieder gebruik te verwijderen om de opslagkosten te verlagen. De standaard container bevat toepassings-en systeem Logboeken. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Het delen van een blobcontainer als het standaardbestandssysteem voor meerdere clusters wordt niet ondersteund.

> [!NOTE]  
> De Access-laag voor het archief is een offline laag met een aantal latentie voor het ophalen van uur en wordt niet aanbevolen voor gebruik met HDInsight. Zie [Access-laag archiveren](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)voor meer informatie.

Als u ervoor kiest om uw opslag account te beveiligen met de **firewalls en beperkingen voor virtuele netwerken** op **geselecteerde netwerken**, moet u de uitzonde ring inschakelen **vertrouwde micro soft-Services toestaan...** zodat HDInsight toegang kan krijgen tot uw opslag account.

## <a name="hdinsight-storage-architecture"></a>HDInsight-opslagarchitectuur
Het volgende diagram biedt een abstracte weergave van de HDInsight-opslagarchitectuur bij gebruik van Azure Storage:

![Hadoop-clusters gebruiken de HDFS-API voor toegang tot en opslag van gestructureerde en ongestructureerde gegevens in Blob Storage.](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "HDInsight Storage-architectuur")

HDInsight biedt toegang tot het Distributed File System dat lokaal wordt gekoppeld aan de rekenknooppunten. Dit bestandssysteem is toegankelijk via de volledig gekwalificeerde URI, bijvoorbeeld:

    hdfs://<namenodehost>/<path>

Daarnaast biedt HDInsight toegang tot gegevens die zijn opgeslagen in Azure Storage. De syntaxis is:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Hier volgen enkele overwegingen bij het gebruik van een Azure Storage-account met HDInsight-clusters.

* **Containers in de opslag accounts die zijn verbonden met een cluster:** Omdat de account naam en-sleutel zijn gekoppeld aan het cluster tijdens het maken, hebt u volledige toegang tot de blobs in deze containers.

* **Open bare containers of open bare blobs in opslag accounts die niet zijn verbonden met een cluster:** U hebt alleen-lezen-machtiging voor de blobs in de containers.
  
> [!NOTE]  
> Met openbare containers kunt u een lijst met alle beschikbare blobs in de desbetreffende container en containermetagegevens ophalen. Openbare blobs zijn alleen toegankelijk als u de exacte URL weet. Zie [toegang tot containers en blobs beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.

* **Persoonlijke containers in opslag accounts die niet zijn verbonden met een cluster:** U hebt geen toegang tot de blobs in de containers, tenzij u het opslag account definieert wanneer u WebHCat-taken verzendt. Dit wordt verderop in dit artikel uitgelegd.

De opslag accounts die zijn gedefinieerd in het proces van maken en hun sleutels worden opgeslagen `%HADOOP_HOME%/conf/core-site.xml` op de cluster knooppunten. HDInsight gebruikt standaard de opslagaccounts die zijn gedefinieerd in het bestand core-site.xml. U kunt deze instelling wijzigen met [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Meerdere WebHCat-taken, waaronder Apache Hive, MapReduce, Apache Hadoop streaming en Apache varken, kunnen een beschrijving van opslag accounts en meta gegevens bevatten. (Dit werkt momenteel voor Pig met opslagaccounts, maar niet voor metagegevens.) Zie [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx) (Een HDInsight-cluster gebruiken met alternatieve opslagaccounts en metastores) voor meer informatie.

Blobs kunnen worden gebruikt voor gestructureerde en ongestructureerde gegevens. De gegevens in blobcontainers worden opgeslagen als sleutel-waardeparen en er is geen maphiërarchie. Maar het slash-teken (/) kan echter worden gebruikt binnen de sleutelnaam deze weergegeven, zodat het lijkt alsof een bestand is opgeslagen in een mapstructuur. De sleutel van de blob kan bijvoorbeeld *input/log1.txt* zijn. Er is niet echt een *invoermap* aanwezig, maar als gevolg van de aanwezigheid van het slash-teken in de naam van de sleutel ziet dit eruit als een bestandspad.

## <a id="benefits"></a>Voordelen van Azure Storage
De impliciete prestaties van het niet volgen van reken clusters en opslag bronnen worden beperkt door de manier waarop de berekenings clusters worden gemaakt dicht bij de resources van het opslag account in de Azure-regio, waar het netwerk met hoge snelheid het efficiënt maakt voor de Reken knooppunten voor toegang tot de gegevens in azure Storage.

Het opslaan van gegevens in Azure Storage in plaats van HDFS heeft enkele voordelen:

* **Hergebruik en delen van gegevens:** De gegevens in HDFS bevinden zich in het berekenings cluster. Alleen de toepassingen die toegang tot het rekencluster hebben, kunnen de gegevens met HDFS API's gebruiken. De gegevens in azure Storage zijn toegankelijk via de HDFS-Api's of via de [Blob Storage rest api's](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API). Zo kan een grotere set toepassingen (inclusief andere HDInsight-clusters) en hulpprogramma's worden gebruikt om de gegevens te produceren en te gebruiken.

* **Gegevens archivering:** Door gegevens op te slaan in azure Storage, kunnen de HDInsight-clusters die worden gebruikt voor de berekening veilig worden verwijderd zonder dat er gebruikers gegevens verloren gaan.

* **Kosten voor gegevens opslag:** Het opslaan van gegevens in DFS voor de lange termijn is kostbaarer dan de opslag van de gegevens in azure Storage, omdat de kosten van een reken cluster hoger zijn dan de kosten van Azure Storage. Daarnaast bespaart u op de kosten voor het laden van gegevens omdat de gegevens niet opnieuw hoeven te worden geladen voor elk rekencluster dat wordt gegenereerd.

* **Elastisch uitschalen:** Hoewel HDFS u een scale-out bestands systeem biedt, wordt de schaal bepaald door het aantal knoop punten dat u voor uw cluster maakt. Het wijzigen van de schaal is mogelijk een complexer proces dan. Vertrouw hiervoor niet zonder meer op de elastische schalingsmogelijkheden waarover u automatisch beschikt in Azure Storage.

* **Geo-replicatie:** Uw Azure-opslag kan geografisch worden gerepliceerd. Hoewel u dit de mogelijkheid van geografisch herstel en gegevensredundantie biedt, zal een failover naar de geografisch gerepliceerde locatie van grote invloed zijn op uw prestaties en kan dit tot extra kosten leiden. U wordt daarom aangeraden de keuze van geo-replicatie goed te overwegen en alleen te gebruiken als de waarde van de gegevens de extra kosten waard zijn.

Bepaalde MapReduce-taken en -pakketten kunnen tussenliggende resultaten genereren die u niet wilt opslaan in Azure Storage. In dat geval kunt u ervoor kiezen de gegevens op te slaan in de lokale HDFS. HDInsight gebruikt DFS voor verschillende tussenliggende resultaten in Hive-taken en andere processen.

> [!NOTE]  
> De meeste HDFS-opdrachten (bijvoorbeeld `ls` `copyFromLocal` , en `mkdir`) werken gewoon zoals verwacht. Alleen de opdrachten die specifiek zijn voor de systeem eigen HDFS-implementatie (aangeduid als DFS), zoals `fschk` en `dfsadmin`, tonen een ander gedrag in azure Storage.

## <a name="address-files-in-azure-storage"></a>Bestanden in Azure Storage adresseren
Het URI-schema om bestanden in Azure Storage vanuit HDInsight te openen:

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Het URI-schema biedt niet-versleutelde toegang (met het voorvoegsel *wasb:* ) en SSL-versleutelde toegang (met *wasbs*). Waar mogelijk kunt u het beste *wasbs* gebruiken, zelfs voor de toegang tot gegevens die zich in dezelfde regio in Azure bevinden.

`<BlobStorageContainerName>` Hiermee wordt de naam van de BLOB-container in azure Storage aangeduid.
`<StorageAccountName>` Hiermee wordt de naam van het Azure Storage-account aangeduid. Een FQDN (Fully Qualified Domain Name) is vereist.

Als geen `<BlobStorageContainerName>` van `<StorageAccountName>` beide of is opgegeven, wordt het standaard bestandssysteem gebruikt. Voor de bestand op het standaardbestandssysteem kunt u een relatief of een absoluut pad gebruiken. U kunt bijvoorbeeld als volgt verwijzen naar het bestand *hadoop-mapreduce-examples.jar* dat bij HDInsight-clusters wordt geleverd:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> De bestands naam bevindt zich `hadoop-examples.jar` in de HDInsight-versies 2,1 en 1,6.

Het pad is de naam van het bestand of de map HDFS pad. Omdat containers in azure Storage sleutel-Stores zijn, is er geen echt hiërarchisch bestands systeem. Een slash (/) in een blob-sleutel wordt geïnterpreteerd als een teken voor mapscheiding. De blob-naam voor bijvoorbeeld *hadoop-mapreduce-examples.jar* is:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Als u buiten HDInsight met blobs werkt, zullen de meeste hulpprogramma's de indeling WASB niet herkennen en wordt er in plaats daarvan een standaardpadindeling verwacht, zoals `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Blobcontainers
Als u blobs wilt gebruiken, maakt u eerst een [Azure Storage-account](../storage/common/storage-create-storage-account.md). Als onderdeel hiervan geeft u een Azure-regio op waar het opslagaccount wordt gemaakt. Het cluster en het opslagaccount moeten worden gehost in dezelfde regio. De Hive-metastore SQL Server-Data Base en Apache Oozie-meta Store SQL Server-Data Base moeten zich ook in dezelfde regio bevinden.

Elke blob die u maakt, behoort tot een container in uw Azure Storage-account, ongeacht de locatie van de blob. Deze container kan een bestaande blob zijn die buiten HDInsight is gemaakt. Het kan echter ook een container zijn die is gemaakt voor een HDInsight-cluster.

In de standaard blobcontainer worden clusterspecifieke gegevens opgeslagen, zoals taakgeschiedenis en logboeken. Deel een standaard blob-container niet met meerdere HDInsight-clusters. Hierdoor kan de taakgeschiedenis beschadigd raken. U kunt voor elk cluster het beste een andere container gebruiken en de gedeelde gegevens in een gekoppeld opslagaccount plaatsen dat is opgegeven in de implementatie van alle relevante cluster, in plaats van het standaardopslagaccount. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het configureren van gekoppelde opslag accounts. U kunt een standaardopslagcontainer echter opnieuw gebruiken nadat het oorspronkelijke HDInsight-cluster is verwijderd. Voor HBase-clusters kunt u het HBase-tabelschema en de bijbehorende gegevens behouden door een nieuw HBase-cluster te maken met de standaard-blobcontainer die wordt gebruikt door een verwijderd HBase-cluster.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interactie met Azure Storage

Micro soft biedt de volgende hulpprogram ma's voor het werken met Azure Storage:

| Hulpprogramma | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

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
* [Apache Pig gebruiken met HDInsight](hadoop/hdinsight-use-pig.md)
* [Azure Storage-hand tekeningen voor gedeelde toegang gebruiken om de toegang tot gegevens te beperken met HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](hdinsight-hadoop-use-data-lake-storage-gen2.md)