---
title: Apache Hadoop-architectuur - Azure HDInsight
description: Beschrijft Apache Hadoop-opslag en -verwerking op Azure HDInsight-clusters.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 3feacd94558ba275c81469827993aef106ae633c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162205"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-architectuur in HDInsight

[Apache Hadoop](https://hadoop.apache.org/) bevat twee kerncomponenten: het [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) dat opslag biedt, en [Apache Hadoop Yet Another Resource Negotiator (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) die verwerking biedt. Met opslag- en verwerkingsmogelijkheden kan een cluster [MapReduce-programma's](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) uitvoeren om de gewenste gegevensverwerking uit te voeren.

> [!NOTE]  
> Een HDFS wordt doorgaans niet geïmplementeerd binnen het HDInsight-cluster om opslag te bieden. In plaats daarvan wordt een HDFS-compatibele interfacelaag gebruikt door Hadoop-componenten. De werkelijke opslagcapaciteit wordt geleverd door Azure Storage of Azure Data Lake Storage. Voor Hadoop worden mapreduce-taken die worden uitgevoerd op het HDInsight-cluster uitgevoerd alsof er een HDFS aanwezig is en vereisen daarom geen wijzigingen om hun opslagbehoeften te ondersteunen. In Hadoop op HDInsight wordt opslag uitbesteed, maar YARN-verwerking blijft een kerncomponent. Zie [Inleiding tot Azure HDInsight](hadoop/apache-hadoop-introduction.md)voor meer informatie.

Dit artikel introduceert YARN en hoe het coördineert de uitvoering van applicaties op HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Apache Hadoop YARN basics

YARN regelt en orkestreert de gegevensverwerking in Hadoop. YARN heeft twee kernservices die worden uitgevoerd als processen op knooppunten in het cluster:

* ResourceManager
* NodeManager

Met ResourceManager worden clustercomputeresources toegewezen aan toepassingen zoals MapReduce-taken. De ResourceManager verleent deze resources als containers, waarbij elke container bestaat uit een toewijzing van CPU-cores en RAM-geheugen. Als u alle beschikbare resources in een cluster hebt gecombineerd en vervolgens de kernen en het geheugen in blokken hebt verdeeld, is elk blok resources een container. Elk knooppunt in het cluster heeft een capaciteit voor een bepaald aantal containers, daarom heeft het cluster een vaste limiet op het aantal beschikbare containers. De toewijzing van resources in een container is configureerbaar.

Wanneer een MapReduce-toepassing op een cluster wordt uitgevoerd, biedt resourcemanager de toepassing de containers waarin deze moet worden uitgevoerd. De ResourceManager houdt de status bij van het uitvoeren van toepassingen, beschikbare clustercapaciteit en houdt toepassingen bij wanneer ze hun resources voltooien en vrijgeven.

De ResourceManager voert ook een webserverproces uit dat een webgebruikersinterface biedt om de status van toepassingen te controleren.

Wanneer een gebruiker een MapReduce-toepassing indient die op het cluster moet worden uitgevoerd, wordt de toepassing ingediend bij ResourceManager. Op zijn beurt wijst ResourceManager een container toe aan beschikbare NodeManager-knooppunten. De Knooppunten Van NodeManager zijn waar de toepassing daadwerkelijk wordt uitgevoerd. De eerste toegewezen container voert een speciale toepassing uit, de ApplicationMaster. Deze ApplicationMaster is verantwoordelijk voor het verkrijgen van resources, in de vorm van volgende containers, die nodig zijn om de ingediende aanvraag uit te voeren. De ApplicationMaster onderzoekt de fasen van de toepassing, zoals de kaartfase en de fase te verminderen, en factoren in hoeveel gegevens moeten worden verwerkt. De ApplicationMaster vraagt vervolgens (*onderhandelt*) de middelen van de ResourceManager namens de toepassing. De ResourceManager verleent op zijn beurt resources van de NodeManagers in het cluster aan de ApplicationMaster die het kan gebruiken bij het uitvoeren van de toepassing.

De NodeManagers voeren de taken uit die deel uitmaken van de toepassing en rapporteren vervolgens hun voortgang en status terug aan de ApplicationMaster. De ApplicationMaster rapporteert op zijn beurt de status van de toepassing terug naar de ResourceManager. De ResourceManager retourneert alle resultaten aan de client.

## <a name="yarn-on-hdinsight"></a>GAREN op HDInsight

Alle HDInsight-clustertypen implementeren YARN. De ResourceManager wordt geïmplementeerd voor hoge beschikbaarheid met een primaire en secundaire instantie, die wordt uitgevoerd op respectievelijk de eerste en tweede hoofdknooppunten binnen het cluster. Slechts één exemplaar van de ResourceManager is tegelijk actief. De nodemanager-exemplaren worden uitgevoerd op de beschikbare werkknooppunten in het cluster.

![Apache GAREN op Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Zachte verwijderen

Zie het nodig zijn om een bestand uit uw opslagaccount te verwijderen:

### <a name="azure-storage"></a>Azure Storage

* [Voorlopig verwijderen voor Azure Storage-blobs](../storage/blobs/storage-blob-soft-delete.md)
* [Blob verwijderen](https://docs.microsoft.com/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Herstel-AzDataLakeStoreVerwijderdItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Bekende problemen met Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Prullenbak zuivering

De `fs.trash.interval` eigenschap van **HDFS** > **Advanced-kernsite** moet `0` de standaardwaarde blijven, omdat u geen gegevens op het lokale bestandssysteem moet opslaan. Deze waarde heeft geen invloed op externe opslagaccounts (WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Volgende stappen

* [MapReduce gebruiken in Apache Hadoop in HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Inleiding tot Azure HDInsight](hadoop/apache-hadoop-introduction.md)
