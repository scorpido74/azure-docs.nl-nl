---
title: Giraph installeren en gebruiken in azure HDInsight
description: Meer informatie over het installeren van Giraph in HDInsight-clusters met behulp van script acties. U kunt Giraph gebruiken voor het verwerken van grafieken in Apache Hadoop in de Azure-Cloud.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: f1ca536ffa2166df4ef6cf51654b7b410e72ea66
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962075"
---
# <a name="install-apache-giraph-on-hdinsight-hadoop-clusters-and-use-giraph-to-process-large-scale-graphs"></a>Installeer Apache Giraph op HDInsight Hadoop-clusters en gebruik Giraph om grootschalige grafieken te verwerken

Meer informatie over het installeren van Apache Giraph op een HDInsight-cluster. Met de script actie functie van HDInsight kunt u uw cluster aanpassen door een bash-script uit te voeren. Scripts kunnen worden gebruikt voor het aanpassen van clusters tijdens en na het maken van het cluster.

## <a name="whatis"></a>Wat is Giraph

[Apache Giraph](https://giraph.apache.org/) biedt u de mogelijkheid om grafiek verwerking uit te voeren met behulp van Hadoop en kan worden gebruikt met Azure HDInsight. Diagram model relaties tussen objecten. Bijvoorbeeld, de verbindingen tussen routers op een groot netwerk zoals Internet, of relaties tussen personen op sociale netwerken. Met grafiek verwerking kunt u de relaties tussen objecten in een grafiek, zoals:

* Mogelijke vrienden identificeren op basis van uw huidige relaties.

* De kortste route tussen twee computers in een netwerk identificeren.

* De pagina positie van webpagina's wordt berekend.

> [!WARNING]  
> Onderdelen die worden meegeleverd met het HDInsight-cluster, worden volledig ondersteund-Microsoft Ondersteuning helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste onderdelen, zoals Giraph, ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Microsoft Ondersteuning kunt het probleem mogelijk oplossen. Als dat niet het geval is, raadpleegt u open source-community's waar diep gaande expertise voor die technologie wordt gevonden. Er zijn bijvoorbeeld veel community-sites die kunnen worden gebruikt, zoals: [MSDN-forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Ook Apache-projecten hebben project sites [https://apache.org](https://apache.org)op, bijvoorbeeld: [Hadoop](https://hadoop.apache.org/).


## <a name="what-the-script-does"></a>Wat het script doet

Met dit script worden de volgende acties uitgevoerd:

* Installeert Giraph naar`/usr/hdp/current/giraph`

* Hiermee kopieert `giraph-examples.jar` u het bestand naar de standaard opslag (WASB) voor uw cluster:`/example/jars/giraph-examples.jar`

## <a name="install"></a>Giraph installeren met behulp van script acties

Een voorbeeld script om Giraph te installeren op een HDInsight-cluster is beschikbaar op de volgende locatie:

    https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

In deze sectie vindt u instructies voor het gebruik van het voorbeeld script bij het maken van het cluster met behulp van de Azure Portal.

> [!NOTE]  
> Een script actie kan worden toegepast met behulp van een van de volgende methoden:
> * Azure PowerShell
> * De Azure CLI
> * De HDInsight .NET SDK
> * Azure Resource Manager-sjablonen
> 
> U kunt ook script acties Toep assen op reeds actieve clusters. Zie [HDInsight-clusters aanpassen met script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie.

1. Begin met het maken van een cluster met behulp van de stappen in [HDInsight-clusters op basis van Linux maken](hdinsight-hadoop-create-linux-clusters-portal.md), maar het maken niet volt ooien.

2. Selecteer in de sectie **optionele configuratie** de optie **script acties**en geef de volgende informatie op:

   * **NAAM**: Voer een beschrijvende naam in voor de script actie.

   * **SCRIPT-URI**: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh

   * **KOP**: Controleer dit item.

   * **WERK NEMER**: Geef deze vermelding niet op.

   * **ZOOKEEPER**: Geef deze vermelding niet op.

   * **PARA METERS**: Laat dit veld leeg.

3. Gebruik de knop **selecteren** aan de onderkant van de **script acties**om de configuratie op te slaan. Gebruik tot slot de knop **selecteren** onder aan de sectie **optionele configuratie** om de optionele configuratie gegevens op te slaan.

4. Ga door met het maken van het cluster zoals beschreven in [HDInsight-clusters op basis van Linux maken](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usegiraph"></a>Giraph in HDInsight Hoe kan ik gebruiken?

Nadat het cluster is gemaakt, voert u de volgende stappen uit om het SimpleShortestPathsComputation-voor beeld uit te voeren dat is opgenomen in Giraph. In dit voor beeld wordt de Basic- [Pregel](https://people.apache.org/~edwardyoon/documents/pregel.pdf) -implementatie gebruikt voor het vinden van het kortste pad tussen objecten in een grafiek.

1. Maak verbinding met het HDInsight-cluster via SSH:

    ```bash
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

2. Gebruik de volgende opdracht om een bestand met de naam **tiny_graph. txt**te maken:

    ```bash
    nano tiny_graph.txt
    ```

    Gebruik de volgende tekst als de inhoud van dit bestand:

    ```text
    [0,0,[[1,1],[3,3]]]
    [1,0,[[0,1],[2,2],[3,1]]]
    [2,0,[[1,2],[4,4]]]
    [3,0,[[0,3],[1,1],[4,4]]]
    [4,0,[[3,4],[2,4]]]
    ```

    Met deze gegevens wordt een relatie tussen objecten in een gerichte grafiek beschreven, met behulp van de-indeling `[source_id, source_value,[[dest_id], [edge_value],...]]`. Elke regel vertegenwoordigt een relatie tussen een `source_id` object en een of meer `dest_id` objecten. De `edge_value` kan worden beschouwd als de sterkte of afstand van de verbinding tussen `source_id` en `dest\_id`.

    De gegevens worden getekend en met behulp van de waarde (of het gewicht) als de afstand tussen objecten kunnen er als volgt uitzien:

    ![tiny_graph. txt getekend als cirkels met verschillende afstanden tussen](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph.png)

3. Als u het bestand wilt opslaan, gebruikt u **CTRL + X**, vervolgens **Y**en tenslotte **Enter** om de bestands naam te accepteren.

4. Gebruik de volgende informatie om de gegevens op te slaan in primaire opslag voor uw HDInsight-cluster:

    ```bash
    hdfs dfs -put tiny_graph.txt /example/data/tiny_graph.txt
    ```

5. Voer het SimpleShortestPathsComputation-voor beeld uit met de volgende opdracht:

    ```bash
    yarn jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnodehost:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2
    ```

    De para meters die met deze opdracht worden gebruikt, worden in de volgende tabel beschreven:

   | Parameter | Wat het doet |
   | --- | --- |
   | `jar` |Het jar-bestand met de voor beelden. |
   | `org.apache.giraph.GiraphRunner` |De klasse die wordt gebruikt om de voor beelden te starten. |
   | `org.apache.giraph.examples.SimpleShortestPathsCoputation` |Het voor beeld dat wordt gebruikt. In dit voor beeld berekent het het kortste pad tussen ID 1 en alle andere Id's in de grafiek. |
   | `-ca mapred.job.tracker` |De hoofd knooppunt voor het cluster. |
   | `-vif` |De invoer indeling die moet worden gebruikt voor de invoer gegevens. |
   | `-vip` |Het invoer gegevensbestand. |
   | `-vof` |De uitvoer indeling. In dit voor beeld is ID en waarde als tekst zonder opmaak. |
   | `-op` |De uitvoer locatie. |
   | `-w 2` |Het aantal werk nemers dat moet worden gebruikt. In dit voor beeld 2. |

    Voor meer informatie over deze en andere para meters die worden gebruikt met Giraph-voor beelden, raadpleegt u de [Giraph Quick](https://giraph.apache.org/quick_start.html)start.

6. Zodra de taak is voltooid, worden de resultaten opgeslagen in de **/example/out/shortestpaths** -map. De namen van de uitvoer bestanden beginnen met een **deel-m-** en eindigen met een getal dat het eerste, tweede, etc. bestand aangeeft. Gebruik de volgende opdracht om de uitvoer weer te geven:

    ```bash
    hdfs dfs -text /example/output/shortestpaths/*
    ```

    De uitvoer ziet er ongeveer als volgt uit:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    Het SimpleShortestPathComputation-voor beeld is vastgelegd om te beginnen met object ID 1 en het kortste pad naar andere objecten te vinden. De uitvoer heeft de indeling van `destination_id` en. `distance` De `distance` is de waarde (of het gewicht) van de randen tussen object-id 1 en de doel-id.

    Door deze gegevens te visualiseren, kunt u de resultaten controleren door de kortste paden tussen ID 1 en alle andere objecten te reizen. Het kortste pad tussen ID 1 en ID 4 is 5. Deze waarde is de totale afstand tussen <span style="color:orange">id 1 en 3</span>, en vervolgens de <span style="color:red">id 3 en 4</span>.

    ![Tekening van objecten als cirkels met kortste paden tussen](./media/hdinsight-hadoop-giraph-install-linux/hdinsight-giraph-graph-out.png)

## <a name="next-steps"></a>Volgende stappen

* [Kleur Toon op HDInsight-clusters installeren en gebruiken](hdinsight-hadoop-hue-linux.md).
