---
title: Overzicht van Azure Storage in HDInsight
description: Overzicht van Azure Storage in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873379"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Overzicht van Azure Storage in HDInsight

Azure Storage is een robuuste opslagoplossing voor algemene doeleinden die naadloos integreert met HDInsight. HDInsight kan een blobcontainer in Azure Storage gebruiken als het standaardbestandssysteem voor het cluster. Via een HDFS-interface kan de volledige set componenten in HDInsight direct werken op gestructureerde of ongestructureerde gegevens die als blobs zijn opgeslagen.

We raden u aan afzonderlijke opslagcontainers te gebruiken voor uw standaardclusteropslag en uw bedrijfsgegevens. De scheiding is het isoleren van de HDInsight logs en tijdelijke bestanden van uw eigen bedrijfsgegevens. We raden ook aan om de standaardblobcontainer, die toepassings- en systeemlogboeken bevat, na elk gebruik te verwijderen om de opslagkosten te verlagen. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Als u ervoor kiest uw opslagaccount te beveiligen met de beperkingen **firewalls en virtuele netwerken** op **geselecteerde netwerken,** moet u de uitzondering **Vertrouwde Microsoft-services toestaan inschakelen...**. De uitzondering is dat HDInsight toegang heeft tot uw opslagaccount.

## <a name="hdinsight-storage-architecture"></a>HDInsight-opslagarchitectuur

Het volgende diagram biedt een abstracte weergave van de HDInsight-architectuur van Azure Storage:

!['HDInsight Storage Architecture'](./media/overview-azure-storage/storage-architecture.png "HDInsight-opslagarchitectuur")

HDInsight biedt toegang tot het Distributed File System dat lokaal wordt gekoppeld aan de rekenknooppunten. Dit bestandssysteem is toegankelijk via de volledig gekwalificeerde URI, bijvoorbeeld:

    hdfs://<namenodehost>/<path>

Via HDInsight hebt u ook toegang tot gegevens in Azure Storage. De syntaxis ziet er als volgt uit:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Houd rekening met de volgende beginselen bij het gebruik van een Azure Storage-account met HDInsight-clusters:

* **Containers in de opslagaccounts die zijn verbonden met een cluster:** omdat de accountnaam en de sleutel tijdens het maken worden gekoppeld aan het cluster, hebt u volledige toegang tot de blobs in deze containers.

* **Openbare containers of openbare blobs in opslagaccounts die niet zijn verbonden met een cluster:** U hebt alleen-lezen toestemming voor de blobs in de containers.
  
  > [!NOTE]  
  > Met openbare containers u een lijst krijgen van alle blobs die beschikbaar zijn in die container en containermetagegevens krijgen. Openbare blobs zijn alleen toegankelijk als u de exacte URL weet. Zie [Anonieme leestoegang tot containers en blobs beheren](../storage/blobs/storage-manage-access-to-resources.md)voor meer informatie.

* **Privécontainers in opslagaccounts die niet zijn verbonden met een cluster:** U hebt geen toegang tot de blobs in de containers, tenzij u het opslagaccount definieert wanneer u de WebHCat-taken verzendt.

De opslagaccounts die worden gedefinieerd tijdens het creatieproces en de bijbehorende sleutels worden opgeslagen in %HADOOP_HOME%/conf/core-site.xml op de clusterknooppunten. HDInsight maakt standaard gebruik van de opslagaccounts die zijn gedefinieerd in het bestand core-site.xml. U deze instelling wijzigen met [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Meerdere WebHCat-taken, waaronder Apache Hive. En MapReduce, Apache Hadoop streaming, en Apache Pig, dragen een beschrijving van opslagaccounts en metadata. (Dit aspect geldt momenteel voor Pig met opslagaccounts, maar niet voor metagegevens.) Zie [Een HDInsight-cluster gebruiken met alternatieve opslagaccounts en metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)voor meer informatie.

Blobs kunnen worden gebruikt voor gestructureerde en ongestructureerde gegevens. Blob-containers slaan gegevens op als sleutel-/waardeparen en hebben geen directoryhiërarchie. De sleutelnaam kan echter een slash-teken (/ ) bevatten om het te laten lijken alsof een bestand is opgeslagen in een mapstructuur. De sleutel van een blob `input/log1.txt`kan bijvoorbeeld . Er `input` bestaat geen werkelijke map, maar vanwege het slash-teken in de sleutelnaam lijkt de sleutel op een bestandspad.

## <a name="benefits-of-azure-storage"></a>Voordelen van Azure Storage

Compute clusters en opslagresources die niet zijn ondergebracht, hebben impliciete prestatiekosten. Deze kosten worden beperkt door de manier waarop de compute clusters worden gemaakt in de buurt van de opslagaccountbronnen binnen het Azure-gebied. In deze regio hebben de compute nodes efficiënt toegang tot de gegevens via het hogesnelheidsnetwerk in Azure Storage.

Wanneer u de gegevens opslaat in Azure Storage in plaats van HDFS, krijgt u verschillende voordelen:

* **Hergebruik en delen van gegevens:** de gegevens in HDFS bevinden zich in het rekencluster. Alleen de toepassingen die toegang tot het rekencluster hebben, kunnen de gegevens met HDFS API's gebruiken. De gegevens in Azure Storage kunnen daarentegen worden geopend via de HDFS-API's of de REST-API's voor Blob-opslag. Door deze opstelling kan een grotere set toepassingen (waaronder andere HDInsight-clusters) en tools worden gebruikt om de gegevens te produceren en te consumeren.

* **Gegevensarchivering:** Wanneer gegevens worden opgeslagen in Azure Storage, kunnen de HDInsight-clusters die worden gebruikt voor berekening veilig worden verwijderd zonder gebruikersgegevens te verliezen.

* **Kosten voor gegevensopslag:** Het opslaan van gegevens in DFS voor de lange termijn is duurder dan het opslaan van de gegevens in Azure Storage. Omdat de kosten van een compute cluster hoger zijn dan de kosten van Azure Storage. Omdat de gegevens niet opnieuw hoeven te worden geladen voor elke compute cluster generatie, bespaart u ook kosten voor het laden van gegevens.

* **Elastisch uitbreiden:** hoewel HDFS u een uitgebreid bestandssysteem biedt, wordt de schaal bepaald door het aantal knooppunten dat u voor het cluster maakt. Het wijzigen van de schaal kan ingewikkelder zijn dan de elastische schalingmogelijkheden die u automatisch in Azure Storage krijgt.

* **Geo-replicatie:** Uw Azure Storage kan geo-gerepliceerd worden. Hoewel geo-replicatie u geografische herstel- en gegevensredundantie biedt, heeft een fail-over naar de geo-gerepliceerde locatie ernstige gevolgen voor uw prestaties en kan dit extra kosten met zich meebrengen. Kies dus voorzichtig en alleen voor geo-replicatie als de waarde van de gegevens de extra kosten rechtvaardigt.

Bepaalde mapWerk verminderen, taken en pakketten verminderen, kunnen tussentijdse resultaten opleveren die u niet in Azure Storage wilt opslaan. In dat geval u ervoor kiezen om de gegevens op te slaan in de lokale HDFS. HDInsight gebruikt DFS voor een aantal van deze tussentijdse resultaten in Hive-taken en andere processen.

> [!NOTE]  
> De meeste HDFS-opdrachten `ls`(bijvoorbeeld , en `copyFromLocal` `mkdir`) werken zoals verwacht in Azure Storage. Alleen de opdrachten die specifiek zijn voor de oorspronkelijke HDFS-implementatie `fschk` (die wordt aangeduid als DFS), zoals en `dfsadmin`tonen ander gedrag in Azure Storage.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
* [Overzicht van Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)