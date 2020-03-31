---
title: 'Quickstart: Apache Hive in Azure HDInsight met Apache Zeppelin'
description: In deze quickstart leert u hoe u Apache Zeppelin gebruiken om Apache Hive-query's uit te voeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive
ms.date: 12/03/2019
ms.openlocfilehash: 49b576fd511d17616880e5d981fd3f649de797df
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79367921"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Snelstart: Apache Hive-query's uitvoeren in Azure HDInsight met Apache Zeppelin

In deze quickstart leert u hoe u Apache Zeppelin gebruiken om Apache Hive-query's uit te voeren in Azure HDInsight. [Apache Hive](https://hive.apache.org/) HDInsight Interactive Query-clusters bevatten [Apache Zeppelin-notitieblokken](https://zeppelin.apache.org/) die u gebruiken om interactieve Hive-query's uit te voeren.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Een HDInsight Interactive Query-cluster. Zie [Cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md) om een HDInsight-cluster te maken.  Kies het clustertype **Interactieve query.**

## <a name="create-an-apache-zeppelin-note"></a>Een Apache Zeppelin-notitie maken

1. Vervang `CLUSTERNAME` de naam van uw cluster `https://CLUSTERNAME.azurehdinsight.net/zeppelin`in de volgende URL . Voer vervolgens de URL in een webbrowser in.

2. Voer de gebruikersnaam en het wachtwoord van uw clusterlogin in. Vanaf de Zeppelin-pagina u een nieuwe notitie maken of bestaande notities openen. **HiveSample** bevat enkele voorbeeldzoekopdrachten van Hive.HiveSample contains some sample Hive queries.  

    ![HDInsight Interactive Query zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecteer **Nieuwe notitie maken**.

4. Typ of selecteer in het dialoogvenster **Nieuwe notitie maken** de volgende waarden:

    - Opmerkingsnaam: voer een naam voor de notitie in.
    - Standaardtolk: Selecteer **jdbc** in de vervolgkeuzelijst.

5. Selecteer **Notitie maken**.

6. Voer de volgende Hive-query in de codesectie in en druk op **Shift + Enter:**

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![HDInsight Interactive Query zeppelin voert query uit](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    De **instructie %jdbc(hive)** in de eerste regel vertelt het notitieblok om de Hive JDBC-tolk te gebruiken.

    De query retourneert één Hive-tabel met de naam **hivesampletable**.

    Hieronder volgen twee extra Hive-query's die u uitvoeren tegen **de hivesampletabel:**

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    In vergelijking met de traditionele Hive moeten de queryresultaten sneller terugkomen.

### <a name="additional-examples"></a>Aanvullende voorbeelden

1. Maak een tabel. Voer de onderstaande code uit in het Zeppelin-notitieblok:

    ```hql
    %jdbc(hive)
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED
    FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE;
    ```

1. Gegevens in de nieuwe tabel laden. Voer de onderstaande code uit in het Zeppelin-notitieblok:

    ```hql
    %jdbc(hive)
    LOAD DATA
    INPATH 'wasbs:///example/data/sample.log'
    INTO TABLE log4jLogs;
    ```

1. Voeg één record in. Voer de onderstaande code uit in het Zeppelin-notitieblok:

    ```hql
    %jdbc(hive)
    INSERT INTO TABLE log4jLogs2
    VALUES ('A', 'B', 'C', 'D', 'E', 'F', 'G');
    ```

Controleer de [taalhandleiding Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) voor aanvullende syntaxis.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. Er worden ook kosten in rekening gebracht voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u Apache Zeppelin gebruiken om Apache Hive-query's uit te voeren in Azure HDInsight. In het volgende artikel ziet u hoe u query's uitvoert met Visual Studio voor meer informatie over Hive-query's.

> [!div class="nextstepaction"]
> [Maak verbinding met Azure HDInsight en voer Apache Hive-query's uit met Data Lake-hulpprogramma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
