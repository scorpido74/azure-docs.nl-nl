---
title: 'Snelstartgids: Apache Storm topologie maken/beheren-Azure HDInsight'
description: In de Quick Start leert u hoe u een Apache Storm topologie kunt maken en bewaken in azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 24173b553f30f652caf20b1ec7500fd9c4d2f7a0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73241224"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Snelstartgids: een Apache Storm topologie in azure HDInsight maken en bewaken

Apache Storm is een gedistribueerd, schaalbaar, fouttolerant en realtime berekeningssysteem voor het verwerken van gegevensstromen. Met Storm in Azure HDInsight kunt u een op een cloud gebaseerd Storm-cluster maken dat in realtime big data-analyses uitvoert.

In deze Snelstartgids gebruikt u een voor beeld van het Apache [Storm-starter-](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) project om een Apache Storm topologie te maken en te bewaken naar een bestaand Apache Storm cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Storm cluster in HDInsight. Zie [Apache Hadoop clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **Storm** voor het **cluster type**.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>De topologie maken

1. Maak verbinding met uw Storm-cluster. Bewerk de onderstaande opdracht door de `CLUSTERNAME` naam van uw Storm-cluster te vervangen en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Het **WordCount** -voor beeld is opgenomen in uw HDInsight `/usr/hdp/current/storm-client/contrib/storm-starter/`-cluster op. De topologie genereert wille keurige zinnen en telt het aantal keren dat er woorden optreden. Gebruik de volgende opdracht om de **WordCount** -topologie op het cluster te starten:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>De topologie bewaken

Storm biedt een webinterface voor het werken met actieve topologieën en is opgenomen in uw HDInsight-cluster.

Voer de volgende stappen uit voor het bewaken van de topologie met behulp van de Storm-gebruikersinterface:

1. Als u de Storm-gebruikersinterface wilt weergeven, opent u `https://CLUSTERNAME.azurehdinsight.net/stormui` in een webbrowser. Vervang `CLUSTERNAME` door de naam van uw cluster.

2. Onder **topologie samenvatting**selecteert u de vermelding **WordCount** in de kolom **naam** . Er wordt informatie over de topologie weergegeven.

    ![Storm-dashboard met informatie over de Storm-Starter WordCount-topologie.](./media/apache-storm-quickstart/hdi-topology-summary.png)

    De nieuwe pagina bevat de volgende informatie:

    |Eigenschap | Beschrijving |
    |---|---|
    |Topologie statistieken|Basis informatie over de topologie prestaties, geordend in tijd Vensters. Wanneer er een specifiek tijdvenster wordt geselecteerd, verandert het tijdvenster voor informatie die wordt weergegeven in andere gedeelten van de pagina.|
    |Spouts|Basis informatie over spouts, met inbegrip van de laatste fout die door elke Spout wordt geretourneerd.|
    |Bolts|Basis informatie over schichten.|
    |Topologie configuratie|Gedetailleerde informatie over de topologie configuratie.|
    |Activeren|Hervat de verwerking van een gedeactiveerde topologie.|
    |Deactiveren|Hiermee wordt een actieve topologie onderbroken.|
    |Opnieuw verdelen|Hiermee past u de parallellisme van de topologie aan. Nadat u het aantal knooppunten in het cluster hebt gewijzigd, moet u actieve topologieën opnieuw verdelen. Met het opnieuw verdelen wordt de parallelle uitvoering aangepast om te compenseren voor het toegenomen/afgenomen aantal knooppunten in het cluster. Zie [informatie over de parallellisme van een Apache Storm topologie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)voor meer informatie.|
    |Verwijderen|Hiermee wordt een storm-topologie beëindigd na de opgegeven time-out.|

3. Selecteer op deze pagina een item in de sectie **Spouts** of **Bolts**. Er wordt informatie over het geselecteerde onderdeel weergegeven.

    ![Storm-dashboard met informatie over de geselecteerde onderdelen.](./media/apache-storm-quickstart/hdi-component-summary.png)

    Op de pagina Nieuw wordt de volgende informatie weer gegeven:

    |Eigenschap | Beschrijving |
    |---|---|
    |Spout/flits statistieken|Basis informatie over de prestaties van het onderdeel, geordend in tijd Vensters. Wanneer er een specifiek tijdvenster wordt geselecteerd, verandert het tijdvenster voor informatie die wordt weergegeven in andere gedeelten van de pagina.|
    |Invoer statistieken (alleen flits)|Informatie over onderdelen die gegevens produceren die door de schicht worden verbruikt.|
    |Uitvoer statistieken|Informatie over gegevens die door deze schicht worden verzonden.|
    |Executor|Informatie over exemplaren van dit onderdeel.|
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

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een voor beeld van het Apache [Storm-starter-](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) project gebruikt om een Apache Storm topologie te maken en te bewaken naar een bestaand Apache Storm cluster. Ga naar het volgende artikel voor meer informatie over de basis principes van het beheer en de bewaking van Apache Storm-topologieën.

> [!div class="nextstepaction"]
>[Apache Storm topologieën implementeren en beheren in azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)