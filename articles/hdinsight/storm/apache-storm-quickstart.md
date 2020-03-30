---
title: 'Snelstart: Apache Storm-topologie maken/beheren - Azure HDInsight'
description: Leer in de snelstart hoe u een Apache Storm-topologie maakt en monitort in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73241224"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Snelstart: een Apache Storm-topologie maken en bewaken in Azure HDInsight

Apache Storm is een gedistribueerd, schaalbaar, fouttolerant en realtime berekeningssysteem voor het verwerken van gegevensstromen. Met Storm in Azure HDInsight kunt u een op een cloud gebaseerd Storm-cluster maken dat in realtime big data-analyses uitvoert.

In deze quickstart gebruikt u een voorbeeld van het Apache [storm-starter-project](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) om een Apache Storm-topologie te maken en te bewaken naar een bestaand Cluster apache storm.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en Selecteer **Storm** voor **clustertype**.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>De topologie maken

1. Maak verbinding met je Stormcluster. Bewerk de onderstaande `CLUSTERNAME` opdracht door de naam van uw stormcluster te vervangen en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Het **voorbeeld WordCount** is opgenomen in `/usr/hdp/current/storm-client/contrib/storm-starter/`uw HDInsight-cluster op . De topologie genereert willekeurige zinnen en telt hoe vaak woorden voorkomen. Gebruik de volgende opdracht om de **wordcount-topologie** in het cluster te starten:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>De topologie bewaken

Storm biedt een webinterface voor het werken met hardlooptopologieën en is opgenomen in uw HDInsight-cluster.

Voer de volgende stappen uit voor het bewaken van de topologie met behulp van de Storm-gebruikersinterface:

1. Als u de Storm-gebruikersinterface wilt weergeven, opent u `https://CLUSTERNAME.azurehdinsight.net/stormui` in een webbrowser. Vervang `CLUSTERNAME` door de naam van uw cluster.

2. Selecteer **onder Topologiesamenvatting**de **vermelding van het aantal woorden** in de kolom **Naam.** Er wordt informatie over de topologie weergegeven.

    ![Storm-dashboard met informatie over de Storm-Starter WordCount-topologie.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    De nieuwe pagina bevat de volgende informatie:

    |Eigenschap | Beschrijving |
    |---|---|
    |Topologiestatistieken|Basisinformatie over de topologieprestaties, ingedeeld in tijdvensters. Wanneer er een specifiek tijdvenster wordt geselecteerd, verandert het tijdvenster voor informatie die wordt weergegeven in andere gedeelten van de pagina.|
    |Tuiten|Basisinformatie over tuiten, inclusief de laatste fout die door elke tuit wordt geretourneerd.|
    |Bouten|Basisinformatie over bouten.|
    |Topologieconfiguratie|Gedetailleerde informatie over de topologieconfiguratie.|
    |Activeren|Hervat de verwerking van een gedeactiveerde topologie.|
    |Deactiveren|Hiermee wordt een hardlooptopologie onderbroken.|
    |Opnieuw in evenwicht brengen|Hiermee past u het parallellisme van de topologie aan. Nadat u het aantal knooppunten in het cluster hebt gewijzigd, moet u actieve topologieën opnieuw verdelen. Met het opnieuw verdelen wordt de parallelle uitvoering aangepast om te compenseren voor het toegenomen/afgenomen aantal knooppunten in het cluster. Zie [Het begrijpen van de parallellisme van een Apache Storm-topologie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)voor meer informatie.|
    |Doden|Beëindigt een stormtopologie na de opgegeven time-out.|

3. Selecteer op deze pagina een item in de sectie **Spouts** of **Bolts**. Er wordt informatie over het geselecteerde onderdeel weergegeven.

    ![Storm-dashboard met informatie over de geselecteerde onderdelen.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Op de nieuwe pagina worden de volgende gegevens weergegeven:

    |Eigenschap | Beschrijving |
    |---|---|
    |Spout/Bolt statistieken|Basisinformatie over de prestaties van de component, ingedeeld in tijdvensters. Wanneer er een specifiek tijdvenster wordt geselecteerd, verandert het tijdvenster voor informatie die wordt weergegeven in andere gedeelten van de pagina.|
    |Invoerstatistieken (alleen bout)|Informatie over onderdelen die gegevens produceren die door de bout worden verbruikt.|
    |Uitvoerstatistieken|Informatie over gegevens die door deze bout worden uitgestoten.|
    |Executeurs|Informatie over exemplaren van dit onderdeel.|
    |Fouten|Fouten die door dit onderdeel worden geproduceerd.|

4. Als u details voor een specifiek exemplaar van het onderdeel wilt weergeven, selecteert u een item in de kolom **Port** in de sectie **Executor** terwijl u de details van een spout of bolt bekijkt.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    In dit voorbeeld komt het woord **seven** 1.493.957 keer voor. Dit getal is het aantal keer dat het woord is gevonden sinds deze topologie is gestart.

## <a name="stop-the-topology"></a>De topologie stoppen

Ga terug naar de pagina **Topology summary** voor de word-count-topologie en klik vervolgens op de knop **Kill** in de sectie **Topology actions**. Wanneer hierom wordt gevraagd, voert u 10 in voor het aantal seconden dat moet worden gewacht voordat de topologie wordt gestopt. Na de time-outperiode wordt de topologie niet meer weergegeven als u het gedeelte **Storm UI** van het dashboard bezoekt.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een voorbeeld van het Apache [storm-starter-project](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) gebruikt om een Apache Storm-topologie te maken en te bewaken naar een bestaand Cluster apache storm. Ga naar het volgende artikel om de basisprincipes van het beheren en bewaken van Apache Storm topologieën te leren.

> [!div class="nextstepaction"]
>[Apache Storm-topologieën implementeren en beheren op Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)