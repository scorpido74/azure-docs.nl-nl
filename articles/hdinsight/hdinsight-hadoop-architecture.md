---
title: 'Apache Hadoop architectuur: Azure HDInsight'
description: Beschrijft Apache Hadoop opslag en verwerking op Azure HDInsight-clusters.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: d41d671cf773bdab20c3f105c7d1abb6c7bde840
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810230"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-architectuur in HDInsight

[Apache Hadoop](https://hadoop.apache.org/) bevat twee kern onderdelen: de [Apache HADOOP Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) die opslag biedt en [Apache Hadoop nog een andere resource-onderhandel (garens)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) die verwerkings mogelijkheden bieden. Met de opslag-en verwerkings functionaliteit wordt een cluster in staat om [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) -Program ma's uit te voeren om de gewenste gegevens verwerking uit te voeren.

> [!NOTE]  
> Een HDFS wordt doorgaans niet geïmplementeerd in het HDInsight-cluster om opslag te bieden. In plaats daarvan wordt een met HDFS compatibele interface laag gebruikt door Hadoop-onderdelen. De werkelijke opslag capaciteit wordt gegeven door Azure Storage of Azure Data Lake Storage. Voor Hadoop worden MapReduce-taken die worden uitgevoerd op het HDInsight-cluster uitgevoerd alsof er een HDFS aanwezig was, waardoor er geen wijzigingen nodig zijn om de opslag behoeften te ondersteunen. In Hadoop op HDInsight is opslag uitbesteed, maar garen verwerking blijft een kern onderdeel. Zie [Introduction to Azure HDInsight](hadoop/apache-hadoop-introduction.md)(Engelstalig) voor meer informatie.

In dit artikel worden GARENs geïntroduceerd en hoe het de uitvoering van toepassingen op HDInsight coördineert.

## <a name="apache-hadoop-yarn-basics"></a>Basis beginselen van Apache Hadoop garen 

GARENs regelen en organiseren gegevens verwerking in Hadoop. GARENs hebben twee kern services die als processen worden uitgevoerd op knoop punten in het cluster: 

* ResourceManager 
* NodeManager

De Resource Manager verleent cluster Compute-resources aan toepassingen als MapReduce-taken. De Resource Manager geeft deze bronnen als containers, waarbij elke container bestaat uit een toewijzing van CPU-kernen en RAM-geheugen. Als u alle beschik bare resources in een cluster hebt gecombineerd en vervolgens de kernen en het geheugen in blokken hebt gedistribueerd, is elk bronnen blok een container. Elk knoop punt in het cluster heeft een capaciteit voor een bepaald aantal containers. Daarom heeft het cluster een vaste limiet voor het aantal beschik bare containers. De service-eenheid van resources in een container kan worden geconfigureerd. 

Wanneer een MapReduce-toepassing wordt uitgevoerd op een cluster, biedt de Resource Manager de toepassing de containers waarin moet worden uitgevoerd. De resource manager houdt de status van het uitvoeren van toepassingen, de beschik bare cluster capaciteit en houdt bij het bijhouden van de toepassingen op de markt. 

De Resource Manager voert ook een webserver proces uit dat een webgebruikersinterface biedt om de status van toepassingen te controleren.

Wanneer een gebruiker een MapReduce-toepassing indient om uit te voeren op het cluster, wordt de toepassing verzonden naar de Resource Manager. De resource manager wijst op zijn beurt een container toe op beschik bare NodeManager-knoop punten. De NodeManager-knoop punten zijn waar de toepassing daad werkelijk wordt uitgevoerd. De eerste toegewezen container voert een speciale toepassing uit met de naam ApplicationMaster. Deze ApplicationMaster is verantwoordelijk voor het verkrijgen van resources, in de vorm van volgende containers, die nodig zijn om de ingediende toepassing uit te voeren. De ApplicationMaster onderzoekt de fasen van de toepassing, zoals de kaart fase en de fase verminderen, en factoren in hoeveel gegevens er moeten worden verwerkt. De ApplicationMaster vervolgens aanvragen (*onderhandelt*) de bronnen uit de resource manager namens de toepassing. De Resource Manager geeft resources van de NodeManagers in het cluster door aan de ApplicationMaster om deze te gebruiken voor het uitvoeren van de toepassing. 

De NodeManagers voert de taken uit waaruit de toepassing is opgebouwd en rapporteert de voortgang en de status terug naar de ApplicationMaster. Met de ApplicationMaster in wordt de status van de toepassing weer gegeven in de Resource Manager. De Resource Manager retourneert alle resultaten naar de client.

## <a name="yarn-on-hdinsight"></a>GARENs op HDInsight

Alle typen HDInsight-clusters implementeren GARENs. De Resource Manager wordt geïmplementeerd voor hoge Beschik baarheid met een primair en secundair exemplaar, dat respectievelijk wordt uitgevoerd op de eerste en tweede hoofd knooppunten in het cluster. Er is slechts één exemplaar van de Resource Manager tegelijk actief. De NodeManager-instanties worden uitgevoerd op de beschik bare worker-knoop punten in het cluster.

![GARENs op HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Volgende stappen

* [MapReduce gebruiken in Apache Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Inleiding tot Azure HDInsight](hadoop/apache-hadoop-introduction.md)
