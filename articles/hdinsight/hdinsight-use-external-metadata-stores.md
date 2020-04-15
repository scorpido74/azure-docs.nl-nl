---
title: Externe metagegevensopslag gebruiken - Azure HDInsight
description: Gebruik externe metagegevensopslag met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/03/2020
ms.openlocfilehash: e53164d1e25f8a8d0a14d21c0544d95cf912fe9f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313942"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Externe metagegevensopslag gebruiken in Azure HDInsight

Met HDInsight u uw gegevens en metadata beheren met externe datastores. Deze functie is beschikbaar voor [Apache Hive metastore,](#custom-metastore) [Apache Oozie metastore](#apache-oozie-metastore), en [Apache Ambari database](#custom-ambari-db).

De Apache Hive metastore in HDInsight is een essentieel onderdeel van de Apache Hadoop architectuur. Een metastore is de centrale schemarepository. De metastore wordt gebruikt door andere tools voor big data-toegang, zoals Apache Spark, Interactive Query (LLAP), Presto of Apache Pig. HDInsight gebruikt een Azure SQL Database als de Hive-metastore.

![HDInsight Hive Metadata Store-architectuur](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Er zijn twee manieren waarop u een metastore instellen voor uw HDInsight-clusters:

* [Standaardmetastore](#default-metastore)
* [Aangepaste metastore](#custom-metastore)

## <a name="default-metastore"></a>Standaardmetastore

HDInsight maakt standaard een metastore met elk clustertype. U in plaats daarvan een aangepaste metastore opgeven. De standaardmetastore bevat de volgende overwegingen:

* Geen extra kosten. HDInsight maakt een metastore met elk clustertype zonder extra kosten voor u.

* Elke standaardmetastore maakt deel uit van de clusterlevenscyclus. Wanneer u een cluster verwijdert, worden ook de bijbehorende metastore en metagegevens verwijderd.

* U de standaardmetastore niet delen met andere clusters.

* De standaardmetastore maakt gebruik van de basisAzure SQL DB, die een limiet van vijf DTU -(databasetransactie-eenheid) heeft.
Deze standaardmetastore wordt meestal gebruikt voor relatief eenvoudige workloads. Workloads waarvoor geen meerdere clusters nodig zijn en die geen metagegevens nodig hebben die behouden blijven buiten de levenscyclus van het cluster.

## <a name="custom-metastore"></a>Aangepaste metastore

HDInsight ondersteunt ook aangepaste metastores, die worden aanbevolen voor productieclusters:

* U geeft uw eigen Azure SQL Database op als metastore.

* De levenscyclus van de metastore is niet gekoppeld aan een levenscyclus van clusters, zodat u clusters maken en verwijderen zonder metadata te verliezen. Metadata zoals uw Hive-schema's blijven bestaan, zelfs nadat u het HDInsight-cluster hebt verwijderd en opnieuw hebt gemaakt.

* Met een aangepaste metastore u meerdere clusters en clustertypen aan die metastore koppelen. Eén metastore kan bijvoorbeeld worden gedeeld in interactieve query-, hive- en sparkclusters in HDInsight.

* U betaalt voor de kosten van een metastore (Azure SQL DB) op basis van het prestatieniveau dat u kiest.

* U de metastore naar behoefte opschalen.

* Het cluster en de externe metastore moeten in dezelfde regio worden gehost.

![Gebruikscase voor hdinsight Hive-metagegevensarchief](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Azure SQL-database maken en configen voor de aangepaste metastore

Maak of heb een bestaande Azure SQL Database voordat u een aangepaste Hive-metastore instelt voor een HDInsight-cluster.  Zie [Snelstart: Maak één database in Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)voor meer informatie.

Tijdens het maken van het cluster moet de HDInsight-service verbinding maken met de externe metastore en uw referenties verifiëren. Configureer Azure SQL Database firewallregels zodat Azure-services en -bronnen toegang krijgen tot de server. Schakel deze optie in de Azure-portal in door **Serverfirewall instellen te**selecteren. Selecteer vervolgens **Nee** onder **Toegang tot openbare netwerken weigeren**en **Ja** onder **Azure-services en -bronnen toestaan toegang te krijgen tot deze server** voor de Azure SQL Database-server of -database. Zie [IP-firewallregels maken en beheren](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) voor meer informatie

![knop serverfirewall instellen](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![azure services toegang verlenen](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Een aangepaste metastore selecteren tijdens het maken van een cluster

U uw cluster op elk gewenst moment naar een eerder gemaakte Azure SQL Database wijzen. Voor het maken van clusteren via de portal wordt de optie opgegeven vanuit de **instellingen opslag > Metastore.**

![Azure-portal voor HDInsight Hive-metagegevensarchief](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Hive metastore richtlijnen

* Gebruik waar mogelijk een aangepaste metastore om compute resources (uw running cluster) en metadata (opgeslagen in de metastore) te scheiden.

* Begin met een S2-laag, die 50 DTU en 250 GB opslag biedt. Als u een knelpunt ziet, u de database opschalen.

* Als u meerdere HDInsight-clusters wilt openen om toegang te krijgen tot afzonderlijke gegevens, gebruikt u een aparte database voor de metastore op elk cluster. Als u een metastore deelt in meerdere HDInsight-clusters, betekent dit dat de clusters dezelfde metagegevens en onderliggende gebruikersgegevensbestanden gebruiken.

* Een back-up van uw aangepaste metastore periodiek. Azure SQL Database genereert automatisch back-ups, maar de tijdsperiode voor back-upretentie varieert. Zie [Meer informatie over automatische SQL Database-back-ups](../sql-database/sql-database-automated-backups.md)voor meer informatie.

* Zoek uw metastore- en HDInsight-cluster in dezelfde regio. Deze configuratie zorgt voor de hoogste prestaties en de laagste netwerkuitgangskosten.

* Controleer uw metastore op prestaties en beschikbaarheid met Azure SQL Database Monitoring-hulpprogramma's of Azure Monitor-logboeken.

* Wanneer een nieuwe, hogere versie van Azure HDInsight wordt gemaakt ten opzichte van een bestaande aangepaste metastoredatabase, wordt het schema van de metastore bijgewerkt. De upgrade is onomkeerbaar zonder de database te herstellen van back-up.

* Als u een metastore deelt in meerdere clusters, moet u ervoor zorgen dat alle clusters dezelfde HDInsight-versie hebben. Verschillende Hive-versies maken gebruik van verschillende metastoredatabaseschema's. U bijvoorbeeld geen metastore delen in clusters in Hive 2.1 en Hive 3.1.

* In HDInsight 4.0 gebruiken Spark en Hive onafhankelijke catalogi voor toegang tot SparkSQL- of Hive-tabellen. Een tabel die door Spark is gemaakt, leeft in de Spark-catalogus. Een tabel gemaakt door Hive leeft in de Hive catalogus. Dit gedrag is anders dan HDInsight 3.6 waar Hive en Spark een gemeenschappelijke catalogus hebben gedeeld. Hive en Spark Integration in HDInsight 4.0 is gebaseerd op Hive Warehouse Connector (HWC). HWC werkt als brug tussen Spark en Hive. [Meer informatie over Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie is een coördinatiesysteem voor werkstromen waarmee Hadoop-taken worden beheerd. Oozie ondersteunt Hadoop-taken voor Apache MapReduce, Pig, Hive en anderen.  Oozie gebruikt een metastore om details over workflows op te slaan. Als u de prestaties wilt verhogen bij het gebruik van Oozie, u Azure SQL Database gebruiken als een aangepaste metastore. De metastore biedt toegang tot oozie-taakgegevens nadat u uw cluster hebt verwijderd.

Zie Apache Oozie gebruiken [voor werkstromen voor](hdinsight-use-oozie-linux-mac.md)instructies voor het maken van een Oozie-metastore met Azure SQL Database.

## <a name="custom-ambari-db"></a>Aangepaste Ambari-database

Zie [Aangepaste Apache Ambari-database](hdinsight-custom-ambari-db.md)als u uw eigen externe database wilt gebruiken met Apache Ambari op HDInsight.

## <a name="next-steps"></a>Volgende stappen

* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](./hdinsight-hadoop-provision-linux-clusters.md)
