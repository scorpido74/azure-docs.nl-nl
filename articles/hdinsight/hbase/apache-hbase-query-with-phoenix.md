---
title: 'Snelstartgids: Apache HBase & Apache Phoenix-Azure HDInsight'
description: In deze Quick Start leert u hoe u Apache Phoenix kunt gebruiken in HDInsight. U leert ook hoe u sqlline gebruiken op uw computer installeert en instelt om verbinding te maken met een HBase-cluster in HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370318"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>Quick Start: query Apache HBase in azure HDInsight met Apache Phoenix

In deze Quick Start leert u hoe u de Apache Phoenix kunt gebruiken om HBase-query's uit te voeren in azure HDInsight. Apache Phoenix is een SQL-query-engine voor Apache HBase. Deze is toegankelijk als een JDBC-stuurprogramma en maakt het uitvoeren van query's en beheren van HBase-tabellen SQL mogelijk. [Sqlline gebruiken](http://sqlline.sourceforge.net/) is een opdracht regel programma om SQL uit te voeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase-cluster. Zie [cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) om een HDInsight-cluster te maken.  Zorg ervoor dat u het cluster type **HBase** kiest.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="identify-a-zookeeper-node"></a>Een ZooKeeper-knoop punt identificeren

Wanneer u verbinding maakt met een HBase-cluster, moet u verbinding maken met een van de knoop punten van Apache ZooKeeper. Elk HDInsight-cluster heeft drie ZooKeeper-knoop punten. Krul kan worden gebruikt om snel een ZooKeeper-knoop punt te identificeren. Bewerk de onderstaande krul opdracht door `PASSWORD` te vervangen en `CLUSTERNAME` met de relevante waarden en voer de opdracht in een opdracht prompt in:

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

Een gedeelte van de uitvoer ziet er ongeveer als volgt uit:

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

Noteer de waarde voor `host_name` voor later gebruik.

## <a name="create-a-table-and-manipulate-data"></a>Een tabel maken en gegevens bewerken

U kunt SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens Apache Phoenix gebruiken om HBase-tabellen, gegevens in te voegen en gegevens op te vragen.

1. Gebruik `ssh` opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster en voer vervolgens de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Wijzig de map naar de Phoenix-client. Voer de volgende opdracht in:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. Start [sqlline gebruiken](http://sqlline.sourceforge.net/). Bewerk de onderstaande opdracht door `ZOOKEEPER` te vervangen door het knoop punt ZooKeeper dat eerder is geïdentificeerd, en voer vervolgens de volgende opdracht in:

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. Maak een HBase-tabel. Voer de volgende opdracht in:

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. Gebruik de sqlline gebruiken-opdracht `!tables` om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```sqlline
    !tables
    ```

6. Waarden in de tabel invoegen. Voer de volgende opdracht in:

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. Query uitvoeren op de tabel. Voer de volgende opdracht in:

    ```sql
    SELECT * FROM Company;
    ```

8. Een record verwijderen. Voer de volgende opdracht in:

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. De tabel verwijderen. Voer de volgende opdracht in:

    ```hbase
    DROP TABLE Company;
    ```

10. Gebruik de opdracht sqlline gebruiken `!quit` om sqlline gebruiken af te sluiten. Voer de volgende opdracht in:

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de Apache Phoenix kunt gebruiken om HBase-query's uit te voeren in azure HDInsight. Als u meer wilt weten over Apache Phoenix, is het volgende artikel een dieper onderzoek.

> [!div class="nextstepaction"]
> [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
