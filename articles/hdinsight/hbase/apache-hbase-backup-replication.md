---
title: Back-up & replicatie voor Apache HBase, Phoenix - Azure HDInsight
description: Back-up en replicatie instellen voor Apache HBase en Apache Phoenix in Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495944"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Back-up en replicatie instellen voor Apache HBase en Apache Phoenix op HDInsight

Apache HBase ondersteunt verschillende benaderingen voor het beschermen tegen gegevensverlies:

* De `hbase` map kopiëren
* Exporteren en vervolgens importeren
* Tabellen kopiëren
* Momentopnamen
* Replicatie

> [!NOTE]  
> Apache Phoenix slaat zijn metadata op in HBase-tabellen, zodat er een back-up wordt gemaakt van metagegevens wanneer u een back-up maakt van de catalogustabellen van het HBase-systeem.

In de volgende secties wordt het gebruiksscenario voor elk van deze benaderingen beschreven.

## <a name="copy-the-hbase-folder"></a>De hbase-map kopiëren

Met deze benadering kopieert u alle HBase-gegevens, zonder dat u een subset van tabellen of kolomfamilies selecteren. Latere benaderingen zorgen voor meer controle.

HBase in HDInsight maakt gebruik van de standaardopslag die is geselecteerd bij het maken van het cluster, azure storage blobs of Azure Data Lake Storage. In beide gevallen slaat HBase zijn gegevens- en metagegevensbestanden op onder het volgende pad:

    /hbase

* In een Azure `hbase` Storage-account bevindt de map zich aan de hoofdmap van de blobcontainer:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* In Azure Data Lake `hbase` Storage bevindt de map zich onder het hoofdpad dat u hebt opgegeven bij het inrichten van een cluster. Dit hoofdpad heeft `clusters` meestal een map, met een submap die is vernoemd naar uw HDInsight-cluster:

    ```
    /clusters/<clusterName>/hbase
    ```

In beide gevallen `hbase` bevat de map alle gegevens die HBase naar de schijf heeft gespoeld, maar mogelijk bevat deze de gegevens in het geheugen niet. Voordat u op deze map vertrouwen als een nauwkeurige weergave van de HBase-gegevens, moet u het cluster afsluiten.

Nadat u het cluster hebt verwijderd, u de gegevens op hun plaats laten of de gegevens naar een nieuwe locatie kopiëren:

* Maak een nieuwe HDInsight-instantie die naar de huidige opslaglocatie wijst. De nieuwe instantie wordt gemaakt met alle bestaande gegevens.

* Kopieer `hbase` de map naar een andere Azure Storage blobcontainer of Data Lake Storage-locatie en start vervolgens een nieuw cluster met die gegevens. Gebruik Voor Azure Storage [AzCopy](../../storage/common/storage-use-azcopy.md)en voor Data Lake Storage [adlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exporteren en vervolgens importeren

Gebruik op het bron-HDInsight-cluster het [hulpprogramma Exporteren](https://hbase.apache.org/book.html#export) (inbegrepen bij HBase) om gegevens uit een brontabel te exporteren naar de standaardgekoppelde opslag. U de geëxporteerde map vervolgens kopiëren naar de locatie voor doelopslag en het [hulpprogramma Importeren](https://hbase.apache.org/book.html#import) uitvoeren op het hdinsight-cluster van bestemming.

Als u tabelgegevens wilt exporteren, voert u eerst SSH naar het `hbase` hoofdknooppunt van het HDInsight-cluster en voert u de volgende opdracht uit:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

De exportmap mag nog niet bestaan. De tabelnaam is hoofdlettergevoelig.

Als u tabelgegevens wilt importeren, voert SSH het hoofdknooppunt van `hbase` uw hdinsight-cluster van bestemming in en voert u de volgende opdracht uit:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

De tafel moet al bestaan.

Geef het volledige exportpad op naar de standaardopslag of naar een van de gekoppelde opslagopties. Bijvoorbeeld in Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

In Azure Data Lake Storage Gen2 is de syntaxis:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

In Azure Data Lake Storage Gen1 is de syntaxis:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Deze aanpak biedt granulariteit op tabelniveau. U ook een datumbereik opgeven waarop de rijen moeten worden opgenomen, waarmee u het proces stapsgewijs uitvoeren. Elke datum is in milliseconden sinds het Unix-tijdperk.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Houd er rekening mee dat u het aantal versies van elke rij moet opgeven om te exporteren. Als u alle versies in `<numberOfVersions>` het datumbereik wilt opnemen, stelt u een waarde in die groter is dan uw maximaal mogelijke rijversies, zoals 100000.

## <a name="copy-tables"></a>Tabellen kopiëren

Het [hulpprogramma CopyTable](https://hbase.apache.org/book.html#copy.table) kopieert gegevens uit een brontabel, rij voor rij, naar een bestaande doeltabel met hetzelfde schema als de bron. De doeltabel kan zich op hetzelfde cluster of een ander HBase-cluster bevinden. De tabelnamen zijn hoofdlettergevoelig.

Als u CopyTable in een cluster wilt gebruiken, gaat SSH naar `hbase` het hoofdknooppunt van het HDInsight-cluster en voert u de opdracht uit:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Als u CopyTable wilt gebruiken om naar een `peer` tabel in een ander cluster te kopiëren, voegt u de schakelaar toe met het adres van het doelcluster:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Het adres van de bestemming bestaat uit de volgende drie delen:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`is een door komma's gescheiden lijst van Apache ZooKeeper-knooppunten, bijvoorbeeld:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`op HDInsight standaard tot 2181, en `<ZnodeParent>` is `/hbase-unsecure`, dus de volledige `<destinationAddress>` zou zijn:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500 xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Zie [De Apache ZooKeeper Quorum List](#manually-collect-the-apache-zookeeper-quorum-list) handmatig verzamelen in dit artikel voor meer informatie over het ophalen van deze waarden voor uw HDInsight-cluster.

Het hulpprogramma CopyTable ondersteunt ook parameters om het tijdsbereik van rijen op te geven die moeten worden gekopieerd en om de subset van kolomfamilies op te geven in een tabel die u wilt kopiëren. Voer CopyTable zonder parameters uit om de volledige lijst met parameters te bekijken die door CopyTable worden ondersteund:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable scant de volledige inhoud van de brontabel die naar de doeltabel wordt gekopieerd. Dit kan de prestaties van uw HBase-cluster verminderen terwijl CopyTable wordt uitgevoerd.

> [!NOTE]  
> Zie het script in de `hdi_copy_table.sh` Azure [HBase Utils-opslagplaats](https://github.com/Azure/hbase-utils/tree/master/replication) op GitHub om het kopiëren van gegevens tussen tabellen te automatiseren.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Verzamel handmatig de Apache ZooKeeper quorumLijst

Wanneer beide HDInsight-clusters zich in hetzelfde virtuele netwerk bevinden, zoals eerder beschreven, is de interne hostnaamresolutie automatisch. Als u CopyTable voor HDInsight-clusters wilt gebruiken in twee afzonderlijke virtuele netwerken die zijn verbonden door een VPN-gateway, moet u de IP-adressen van de host-IP-adressen van de Zookeeper-knooppunten in het quorum opgeven.

Voer de volgende opdracht krul uit om de namen van quorumhostte te verkrijgen:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Met de opdracht Krullen wordt een JSON-document met HBase-configuratiegegevens opgehaald en de grep-opdracht retourneert alleen de vermelding 'hbase.zookeeper.quorum':

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

De waarde van quorumhostnamen is de volledige tekenreeks rechts van de dubbele punt.

Als u de IP-adressen voor deze hosts wilt ophalen, gebruikt u de volgende opdracht krul voor elke host in de vorige lijst:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

In deze opdracht `<zookeeperHostFullName>` krul is de volledige DNS-naam van `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`een ZooKeeper-host, zoals het voorbeeld . De uitvoer van de opdracht bevat het IP-adres voor de opgegeven host, bijvoorbeeld:

    100    "ip" : "10.0.0.9",

Nadat u de IP-adressen voor alle ZooKeeper-knooppunten in uw quorum hebt verzameld, bouwt u het doeladres opnieuw op:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

In ons voorbeeld:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Momentopnamen

[Met momentopnamen](https://hbase.apache.org/book.html#ops.snapshots) u een point-in-time back-up van gegevens maken in uw HBase-gegevensarchief. Momentopnamen hebben minimale overhead en voltooien binnen enkele seconden, omdat een momentopnamebewerking in feite een metagegevensbewerking is die de namen van alle bestanden die op dat moment worden opgeslagen, vastlegt. Op het moment van een momentopname worden geen werkelijke gegevens gekopieerd. Momentopnamen zijn gebaseerd op de onveranderlijke aard van de gegevens die zijn opgeslagen in HDFS, waarbij updates, verwijderingen en inserts allemaal worden weergegeven als nieuwe gegevens. U een momentopname op hetzelfde cluster herstellen (*klonen)* of een momentopname exporteren naar een ander cluster.

Om een momentopname te maken, ssh in het hoofdknooppunt van `hbase` uw HDInsight HBase cluster en start de shell:

    hbase shell

Gebruik in de hbase-shell de opdracht momentopname met de namen van de tabel en van deze momentopname:

    snapshot '<tableName>', '<snapshotName>'

Als u een momentopname `hbase` op naam in de shell wilt herstellen, schakelt u eerst de tabel uit, herstelt u de momentopname en schakelt u de tabel opnieuw in:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Als u een momentopname wilt herstellen naar een nieuwe tabel, gebruikt u clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Als u een momentopname wilt exporteren naar HDFS voor gebruik door een ander cluster, maakt u eerst de momentopname zoals eerder beschreven en gebruikt u vervolgens het hulpprogramma ExporterenMomentopname. Voer dit hulpprogramma uit vanuit de SSH-sessie naar `hbase` het hoofdknooppunt, niet binnen de shell:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

Het `<hdfsHBaseLocation>` kan een van de opslaglocaties zijn die toegankelijk zijn voor uw broncluster en moet worden aanwijs op de hbase-map die door uw doelcluster wordt gebruikt. Als u bijvoorbeeld een secundair Azure Storage-account hebt gekoppeld aan uw broncluster en dat account toegang biedt tot de container die wordt gebruikt door de standaardopslag van het doelcluster, u deze opdracht gebruiken:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

Nadat de momentopname is geëxporteerd, ssh in het hoofdknooppunt van het doelcluster en de momentopname te herstellen met behulp van de restore_snapshot opdracht zoals eerder beschreven.

Momentopnamen bieden een volledige back-up van `snapshot` een tabel op het moment van de opdracht. Momentopnamen bieden niet de mogelijkheid om incrementele momentopnamen uit te voeren op tijdsvensters en evenmin om subsets van kolommenfamilies op te geven die in de momentopname moeten worden opgenomen.

## <a name="replication"></a>Replicatie

[HBase-replicatie](https://hbase.apache.org/book.html#_cluster_replication) duwt transacties automatisch van een broncluster naar een doelcluster, met behulp van een asynchrone mechanisme met minimale overhead op het broncluster. In HDInsight u replicatie tussen clusters instellen waarbij:

* De bron- en doelclusters bevinden zich in hetzelfde virtuele netwerk.
* De bron- en bestemmingenclusters bevinden zich in verschillende virtuele netwerken die zijn verbonden door een VPN-gateway, maar beide clusters bestaan op dezelfde geografische locatie.
* Het broncluster en de bestemmingenclusters bevinden zich in verschillende virtuele netwerken die zijn verbonden door een VPN-gateway en elk cluster bestaat op een andere geografische locatie.

De algemene stappen voor het instellen van replicatie zijn:

1. Maak in het broncluster de tabellen en vul gegevens in.
2. Maak op het doelcluster lege doeltabellen met het schema van de brontabel.
3. Registreer het doelcluster als peer naar het broncluster.
4. Replicatie inschakelen op de gewenste brontabellen.
5. Kopieer bestaande gegevens uit de brontabellen naar de doeltabellen.
6. Replicatie kopieert automatisch nieuwe gegevenswijzigingen in de brontabellen naar de doeltabellen.

Als u replicatie op HDInsight wilt inschakelen, past u een Scriptactie toe op het hdinsight-cluster met uitvoerende bron. Zie [Apache HBase-replicatie configureren](apache-hbase-replication.md)voor een walkthrough van het inschakelen van replicatie in uw cluster of om te experimenteren met replicatie op voorbeeldclusters die in virtuele netwerken zijn gemaakt met Azure Resource Management-sjablonen. Dat artikel bevat ook instructies voor het inschakelen van replicatie van Phoenix-metagegevens.

## <a name="next-steps"></a>Volgende stappen

* [Apache HBase-replicatie configureren](apache-hbase-replication.md)
* [Werken met het Hulpprogramma voor importeren en exporteren van HBase](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)
