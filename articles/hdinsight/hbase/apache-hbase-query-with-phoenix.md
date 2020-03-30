---
title: 'Snelstart: Apache HBase & Apache Phoenix - Azure HDInsight'
description: In deze quickstart leer je hoe je Apache Phoenix gebruiken in HDInsight. Lees ook hoe u SQLLine op uw computer installeert en instelt om verbinding te maken met een HBase-cluster in HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370318"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Snelstart: Query Apache HBase in Azure HDInsight met Apache Phoenix

In deze quickstart leert u hoe u de Apache Phoenix gebruiken om HBase-query's uit te voeren in Azure HDInsight. Apache Phoenix is een SQL-queryengine voor Apache HBase. Deze is toegankelijk als een JDBC-stuurprogramma en maakt het uitvoeren van query's en beheren van HBase-tabellen SQL mogelijk. [SQLLine](http://sqlline.sourceforge.net/) is een command-line hulpprogramma om SQL uit te voeren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase cluster. Zie [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) om een HDInsight-cluster te maken.  Zorg ervoor dat u het **clustertype HBase** kiest.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Een ZooKeeper-knooppunt identificeren

Wanneer u verbinding maakt met een HBase-cluster, moet u verbinding maken met een van de Apache ZooKeeper-knooppunten. Elk HDInsight-cluster heeft drie ZooKeeper-knooppunten. Krul kan worden gebruikt om snel te identificeren een ZooKeeper knooppunt. Bewerk de opdracht Krul `PASSWORD` hieronder `CLUSTERNAME` door de relevante waarden te vervangen en met deze waarden, en voer de opdracht in een opdrachtprompt in:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Een deel van de uitvoer lijkt op:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Let op de `host_name` waarde voor later gebruik.

## <a name="create-a-table-and-manipulate-data"></a>Een tabel maken en gegevens manipuleren

U SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens Apache Phoenix gebruiken om HBase-tabellen te maken, gegevens in te voegen en querygegevens in te voegen.

1. Gebruik `ssh` de opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door de naam van uw cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Directory wijzigen in de Phoenix-client. Voer de volgende opdracht in:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. [SQLLine starten](http://sqlline.sourceforge.net/). Bewerk de opdracht hieronder `ZOOKEEPER` door te vervangen door het eerder geïdentificeerde ZooKeeper-knooppunt en voer vervolgens de opdracht in:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Maak een HBase-tabel. Voer de volgende opdracht in:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Gebruik de `!tables` opdracht SQLLine om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```sqlline
    !tables
    ```

6. Waarden invoegen in de tabel. Voer de volgende opdracht in:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. De tabel opvragen. Voer de volgende opdracht in:

    ```sql
    SELECT * FROM Company;
    ```

8. Een record verwijderen. Voer de volgende opdracht in:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. Laat de tafel vallen. Voer de volgende opdracht in:

    ```hbase
    DROP TABLE Company;
    ```

10. Gebruik de `!quit` opdracht SQLLine om SQLLine af te sluiten. Voer de volgende opdracht in:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Apache Phoenix gebruiken om HBase-query's uit te voeren in Azure HDInsight. Om meer te weten te komen over Apache Phoenix, zal het volgende artikel een dieper onderzoek bieden.

> [!div class="nextstepaction"]
> [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
