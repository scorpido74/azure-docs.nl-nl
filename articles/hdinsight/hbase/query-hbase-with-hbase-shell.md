---
title: 'Snelstartgids: query Apache HBase in azure HDInsight-HBase shell'
description: In deze Quick Start leert u hoe u Apache HBase shell kunt gebruiken om Apache HBase-query's uit te voeren.
keywords: hdinsight,hadoop,HBase
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 572262cbece26171f9a67bf073906fa2dfd4d8e1
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371066"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-hbase-shell"></a>Snelstartgids: query Apache HBase in azure HDInsight met HBase shell

In deze Quick Start leert u hoe u Apache HBase shell kunt gebruiken om een HBase-tabel te maken, gegevens in te voegen en vervolgens een query uit te voeren op de tabel.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase-cluster. Zie [cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) om een HDInsight-cluster te maken.  Zorg ervoor dat u het cluster type **HBase** kiest.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-table-and-manipulate-data"></a>Een tabel maken en gegevens bewerken

Voor de meeste mensen worden de gegevens weergegeven in een tabelindeling:

![Gegevens in tabel vorm van HDInsight Apache HBase](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-tabular.png)

In HBase (een implementatie van [Bigtable](https://cloud.google.com/bigtable/)), zien dezelfde gegevens er als volgt uit:

![Gegevens van HDInsight Apache HBase BigTable](./media/query-hbase-with-hbase-shell/hdinsight-hbase-contacts-bigtable.png)

U kunt SSH gebruiken om verbinding te maken met HBase-clusters en vervolgens Apache HBase shell gebruiken om HBase-tabellen te maken, gegevens in te voegen en gegevens op te vragen.

1. Gebruik `ssh` opdracht om verbinding te maken met uw HBase-cluster. Bewerk de onderstaande opdracht door `CLUSTERNAME` te vervangen door de naam van uw cluster en voer vervolgens de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Gebruik `hbase shell` opdracht om de HBase-interactieve shell te starten. Voer de volgende opdracht in voor uw SSH-verbinding:

    ```bash
    hbase shell
    ```

3. Gebruik `create` opdracht om een HBase-tabel met twee kolom families te maken. Voer de volgende opdracht in:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Gebruik `list` opdracht om alle tabellen in HBase weer te geven. Voer de volgende opdracht in:

    ```hbase
    list
    ```

5. Gebruik `put` opdracht om in een opgegeven rij in een bepaalde tabel waarden in te voegen voor een opgegeven kolom. Voer de volgende opdracht in:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

6. Gebruik `scan` opdracht om de `Contacts` tabel gegevens te scannen en te retour neren. Voer de volgende opdracht in:

    ```hbase
    scan 'Contacts'
    ```

7. Gebruik `get` opdracht om de inhoud van een rij op te halen. Voer de volgende opdracht in:

    ```hbase
    get 'Contacts', '1000'
    ```

    U ziet vergelijk bare resultaten als met de `scan` opdracht omdat er slechts één rij is.

8. Gebruik `delete` opdracht voor het verwijderen van een celwaarde in een tabel. Voer de volgende opdracht in:

    ```hbase
    delete 'Contacts', '1000', 'Office:Address'
    ```

9. Gebruik `disable` opdracht om de tabel uit te scha kelen. Voer de volgende opdracht in:

    ```hbase
    disable 'Contacts'
    ```

10. Gebruik `drop` opdracht om een tabel uit HBase te verwijderen. Voer de volgende opdracht in:

    ```hbase
    drop 'Contacts'
    ```

11. Gebruik `exit` opdracht om de HBase-interactieve shell te stoppen. Voer de volgende opdracht in:

    ```hbase
    exit
    ```

Zie [Introduction to Apache HBase schema design](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf)(Engelstalig) voor meer informatie over het HBase-tabel schema. Zie de [Naslag Gids voor Apache HBase](https://hbase.apache.org/book.html#quickstart)voor meer HBase-opdrachten.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u Apache HBase shell kunt gebruiken om een HBase-tabel te maken, gegevens in te voegen en vervolgens een query uit te voeren op de tabel. Voor meer informatie over gegevens die zijn opgeslagen in HBase, leert u in het volgende artikel hoe u query's kunt uitvoeren met Apache Spark.

> [!div class="nextstepaction"]
> [Apache Spark gebruiken om Apache HBase-gegevens te lezen en te schrijven](../hdinsight-using-spark-query-hbase.md)