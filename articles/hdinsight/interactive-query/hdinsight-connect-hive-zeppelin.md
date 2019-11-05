---
title: 'Snelstartgids: Apache Hive in azure HDInsight met Apache Zeppelin'
description: In deze Quick Start leert u hoe u Apache Zeppelin gebruikt om Apache Hive query's uit te voeren.
keywords: hdinsight, Hadoop, Hive, Interactive query, LLAP
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 36d9e9b34deb4bc6cd5f599cfe2d09a12f680730
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494287"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Snelstartgids: Apache Hive query's uitvoeren in azure HDInsight met Apache Zeppelin

In deze Quick Start leert u hoe u Apache Zeppelin gebruikt om [Apache Hive](https://hive.apache.org/) query's uit te voeren in azure HDInsight. HDInsight Interactive query-clusters bevatten [Apache Zeppelin](https://zeppelin.apache.org/) -notebooks die u kunt gebruiken om interactieve Hive-query's uit te voeren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

An HDInsight interactieve query cluster. Zie [cluster maken](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) om een HDInsight-cluster te maken.  Zorg ervoor dat u het **interactieve query** cluster type kiest.

## <a name="create-an-apache-zeppelin-note"></a>Een Apache Zeppelin-opmerking maken

1. Vervang `CLUSTERNAME` door de naam van uw cluster in de volgende URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Voer vervolgens de URL in een webbrowser in.

2. Voer de gebruikers naam en het wacht woord voor uw cluster aanmelding in. Op de pagina Zeppelin kunt u een nieuwe notitie maken of bestaande notities openen. **HiveSample** bevat een aantal voor beelden van Hive-query's.  

    ![Zeppelin voor HDInsight interactieve query](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Selecteer **nieuwe notitie maken**.

4. Typ of Selecteer in het dialoog venster **nieuwe notitie maken** de volgende waarden:

    - Notitie naam: Voer een naam in voor de opmerking.
    - Standaard-interpreter: Selecteer **JDBC** in de vervolg keuzelijst.

5. Selecteer **notitie maken**.

6. Voer de volgende Hive-query in de code sectie in en druk op **SHIFT + ENTER**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Query voor uitvoering van HDInsight Interactive query Zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    De instructie **% JDBC (Hive)** in de eerste regel geeft aan dat het notitie blok de Hive JDBC-interpreter moet gebruiken.

    De query retourneert een Hive-tabel met de naam **hivesampletable**.

    Hieronder vindt u twee extra Hive-query's die u kunt uitvoeren op **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Vergelijking met de traditionele component, worden de query resultaten sneller weer gegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u Apache Zeppelin gebruikt om Apache Hive query's uit te voeren in azure HDInsight. Meer informatie over Hive-query's vindt u in het volgende artikel wordt uitgelegd hoe u query's kunt uitvoeren met Visual Studio.

> [!div class="nextstepaction"]
> [Verbinding maken met Azure HDInsight en Apache Hive query's uitvoeren met behulp van Data Lake-Hulpprogram Ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
