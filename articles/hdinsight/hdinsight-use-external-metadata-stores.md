---
title: Externe meta gegevens archieven gebruiken-Azure HDInsight
description: Gebruik externe meta gegevens archieven met Azure HDInsight-clusters en aanbevolen procedures.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: a876269b2746a1065cee2639cfc5804aff0b3446
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027707"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Externe meta gegevensopslag plaatsen gebruiken in azure HDInsight

De Apache Hive-meta Store in HDInsight is een essentieel onderdeel van de Apache Hadoop architectuur. Een meta Store is de centrale schema opslagplaats die kan worden gebruikt door andere big data Access-hulpprogram ma's, zoals Apache Spark, interactieve query (LLAP), Presto of Apache varken. HDInsight gebruikt een Azure SQL Database als de Hive-metastore.

![Architectuur van het meta gegevens archief van HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Er zijn twee manieren waarop u een meta Store voor uw HDInsight-clusters kunt instellen:

* [Standaard-META Store](#default-metastore)
* [Aangepaste meta Store](#custom-metastore)

## <a name="default-metastore"></a>Standaard-META Store

HDInsight maakt standaard een meta Store met elk cluster type. U kunt in plaats daarvan een aangepaste meta Store opgeven. De standaard-META Store bevat de volgende overwegingen:

* Geen extra kosten. HDInsight maakt een meta Store met elk cluster type zonder extra kosten voor u.

* Elke standaard-META Store maakt deel uit van de cluster levenscyclus. Wanneer u een cluster verwijdert, worden ook de corresponderende meta Store-gegevens verwijderd.

* U kunt de standaard META Store niet delen met andere clusters.

* In de standaard-META Store wordt gebruikgemaakt van de Basic Azure SQL-data base, die een limiet heeft van vijf DTU (data base Trans Action Unit).
Deze standaard META Store wordt doorgaans gebruikt voor relatief eenvoudige werk belastingen waarvoor geen meerdere clusters zijn vereist en waarvoor geen meta gegevens hoeven te worden bewaard buiten de levens cyclus van het cluster.

## <a name="custom-metastore"></a>Aangepaste meta Store

HDInsight biedt ook ondersteuning voor aangepaste meta Stores, die worden aanbevolen voor productie clusters:

* U geeft uw eigen Azure SQL Database op als de meta Store.

* De levens cyclus van de meta Store is niet gebonden aan een cluster levenscyclus, zodat u clusters kunt maken en verwijderen zonder dat meta gegevens verloren gaan. Meta gegevens zoals uw Hive-schema's blijven behouden, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

* Met een aangepaste meta Store kunt u meerdere clusters en cluster typen aan die meta Store koppelen. Een voor beeld: een enkele meta Store kan worden gedeeld tussen interactieve query-, Hive-en Spark-clusters in HDInsight.

* U betaalt voor de kosten van een meta Store (Azure SQL DB) op basis van het prestatie niveau dat u kiest.

* U kunt de meta Store naar behoefte omhoog schalen.

![Use-case van het meta gegevens archief van HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Een aangepaste meta Store selecteren tijdens het maken van het cluster

U kunt uw cluster naar een eerder gemaakte Azure SQL Database laten wijzen tijdens het maken van het cluster of u kunt de SQL Database configureren nadat het cluster is gemaakt. Deze optie is opgegeven bij de **opslag > Meta Store-instellingen** tijdens het maken van een nieuw Hadoop-, Spark-of Interactive Hive-cluster van Azure Portal.

![Meta gegevens archief van HDInsight-Hive Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

U kunt ook extra clusters toevoegen aan een aangepaste meta Store van Azure Portal of van Ambari-configuraties (Hive > Geavanceerd)

![Meta gegevens archief van HDInsight Hive Ambari](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Aanbevolen procedures Hive-metastore

Hier volgen enkele algemene HDInsight-Hive-metastore aanbevolen procedures:

* Gebruik waar mogelijk een aangepaste meta Store om reken resources (uw actieve cluster) en meta gegevens te scheiden (opgeslagen in het meta Store).

* Begin met een S2-laag, die 50 DTU en 250 GB opslag biedt. Als er een knel punt wordt weer geven, kunt u de data base omhoog schalen.

* Als u van plan bent meerdere HDInsight-clusters te gebruiken voor toegang tot afzonderlijke gegevens, gebruikt u een afzonderlijke Data Base voor de meta Store op elk cluster. Als u een meta Store deelt op meerdere HDInsight-clusters, betekent dit dat de clusters dezelfde data-en onderliggende gebruikers gegevens bestanden gebruiken.

* Maak regel matig een back-up van uw aangepaste meta Store. Azure SQL Database maakt automatisch back-ups, maar de periode voor het bewaren van back-ups varieert. Zie [informatie over automatische SQL database back-ups](../sql-database/sql-database-automated-backups.md)voor meer informatie.

* Zoek uw meta Store-en HDInsight-cluster in dezelfde regio, voor de hoogste prestaties en de laagste kosten voor het uitbrengen van het netwerk.

* Bewaak uw meta Store voor prestaties en beschik baarheid met behulp van Azure SQL Database controle hulpprogramma's, zoals de Azure Portal of Azure Monitor Logboeken.

* Wanneer een nieuwe, hogere versie van Azure HDInsight wordt gemaakt op basis van een bestaande aangepaste meta store-data base, wordt het schema van de meta Store bijgewerkt, wat onomkeerbaar is zonder de data base te herstellen vanuit een back-up.

* Als u een meta Store in meerdere clusters deelt, moet u ervoor zorgen dat alle clusters dezelfde HDInsight-versie zijn. Verschillende Hive-versies gebruiken verschillende meta Store-Database schema's. U kunt bijvoorbeeld geen meta Store delen via hive 2,1 en Hive 3,1 versie-clusters.

* In HDInsight 4,0 gebruiken Spark en Hive onafhankelijke catalogi om toegang te krijgen tot SparkSQL of Hive-tabellen. Een tabel die door Spark is gemaakt, bevindt zich in de Spark-catalogus. Een tabel die door Hive is gemaakt, bevindt zich in de Hive-catalogus. Dit wijkt af van HDInsight 3,6 waarbij een gemeen schappelijke catalogus van Hive en Spark is gedeeld. De Hive-en Spark-integratie in HDInsight 4,0 is afhankelijk van Hive Warehouse connector (HWC). HWC werkt als een brug tussen Spark en Hive. [Meer informatie over Hive Warehouse connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie-meta Store

Apache Oozie is een coördinatie systeem voor werk stromen waarmee Hadoop-taken worden beheerd.  Oozie ondersteunt Hadoop-taken voor Apache MapReduce, Pig, Hive en anderen.  Oozie maakt gebruik van een meta Store voor het opslaan van gegevens over huidige en voltooide werk stromen. Als u de prestaties wilt verbeteren wanneer u Oozie gebruikt, kunt u Azure SQL Database als een aangepaste meta Store gebruiken. De meta Store kan ook toegang bieden tot Oozie-taak gegevens nadat u uw cluster hebt verwijderd.

Zie [Apache Oozie gebruiken voor werk stromen](hdinsight-use-oozie-linux-mac.md)voor instructies over het maken van een Oozie-meta store met Azure SQL database.

## <a name="next-steps"></a>Volgende stappen

* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](./hdinsight-hadoop-provision-linux-clusters.md)
