---
title: 'Snelstart: Query Apache HBase in Azure HDInsight - HBase Shell'
description: In deze quickstart leert u hoe u Apache HBase Shell gebruiken om Apache HBase-query's uit te voeren.
keywords: hdinsight, hadoop, HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371066"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Snelstart: Query Apache HBase in Azure HDInsight met HBase Shell

In deze snelstart leert u hoe u Apache HBase Shell gebruiken om een HBase-tabel te maken, gegevens in te voegen en vervolgens de tabel op te vragen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase cluster. Zie [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) om een HDInsight-cluster te maken.  Zorg ervoor dat u het **clustertype HBase** kiest.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Een tabel maken en gegevens manipuleren

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![HDInsight Apache HBase tabelgegevens](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

In HBase (een implementatie van [Cloud BigTable)](https://cloud.google.com/bigtable/)lijken dezelfde gegevens op:

![HDInsight Apache HBase BigTable-gegevens](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

U SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens Apache HBase Shell gebruiken om HBase-tabellen te maken, gegevens in te voegen en querygegevens in te voegen.

1. Gebruik `ssh` de opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door de naam van uw cluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Gebruik `hbase shell` de opdracht om de interactieve shell Van HBase te starten. Voer de volgende opdracht in uw SSH-verbinding in:

    ```bash
    hbase shell
    ```

3. Gebruik `create` de opdracht om een HBase-tabel met families met twee kolommen te maken. Voer de volgende opdracht in:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Gebruik `list` de opdracht om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```hbase
    list
    ```

5. De `put` opdracht gebruiken om waarden in een bepaalde kolom in een bepaalde rij in een bepaalde tabel in te voegen. Voer de volgende opdracht in:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Gebruik `scan` de opdracht om `Contacts` de tabelgegevens te scannen en terug te sturen. Voer de volgende opdracht in:

    ```hbase
    scan 'Contacts'
    ```

7. De `get` opdracht gebruiken om de inhoud van een rij op te halen. Voer de volgende opdracht in:

    ```hbase
    get 'Contacts', '1000'
    ```

    U ziet vergelijkbare resultaten `scan` als het gebruik van de opdracht omdat er slechts één rij is.

8. De `delete` opdracht gebruiken om een celwaarde in een tabel te verwijderen. Voer de volgende opdracht in:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Gebruik `disable` de opdracht om de tabel uit te schakelen. Voer de volgende opdracht in:

    ```hbase
    disable 'Contacts'
    ```

10. Gebruik `drop` de opdracht om een tabel van HBase te laten vallen. Voer de volgende opdracht in:

    ```hbase
    drop 'Contacts'
    ```

11. Gebruik `exit` de opdracht om de interactieve hbase-shell te stoppen. Voer de volgende opdracht in:

    ```hbase
    exit
    ```

Zie [Inleiding tot Apache HBase-schemaontwerp](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)voor meer informatie over het hbase-tabelschema. Raadpleeg de [Snelzoekgids voor Apache HBase](https://hbase.apache.org/book.html#quickstart) voor meer HBase-opdrachten.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u geleerd hoe u Apache HBase Shell gebruiken om een HBase-tabel te maken, gegevens in te voegen en vervolgens de tabel op te vragen. In het volgende artikel ziet u hoe u query's uitvoert met Apache Spark voor meer informatie over gegevens die zijn opgeslagen in HBase.

> [!div class="nextstepaction"]
> [Apache Spark gebruiken om Apache HBase-gegevens te lezen en schrijven](../hdinsight-using-spark-query-hbase.md)