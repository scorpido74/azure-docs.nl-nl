---
title: Externe metagegevensopslag gebruiken - Azure HDInsight
description: Gebruik externe metagegevensopslag met Azure HDInsight-clusters en aanbevolen procedures.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/02/2020
ms.openlocfilehash: edb2d256d3e5d98c52dbdff1162e0e030ebe2be3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272160"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Externe metagegevensopslag gebruiken in Azure HDInsight

MET HDInsight u de controle over uw gegevens en metadata overnemen door belangrijke metadataoplossingen en beheerdatabases te implementeren in externe gegevensopslag. Deze functie is momenteel beschikbaar voor [Apache Hive metastore,](#custom-metastore) [Apache Oozie metastore](#apache-oozie-metastore) en [Apache Ambari database](#custom-ambari-db).

De Apache Hive metastore in HDInsight is een essentieel onderdeel van de Apache Hadoop architectuur. Een metastore is de centrale schemaopslagplaats die kan worden gebruikt door andere tools voor big data-toegang, zoals Apache Spark, Interactive Query (LLAP), Presto of Apache Pig. HDInsight gebruikt een Azure SQL Database als de Hive-metastore.

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
Deze standaardmetastore wordt meestal gebruikt voor relatief eenvoudige workloads waarvoor geen meerdere clusters nodig zijn en die geen metagegevens nodig hebben die buiten de levenscyclus van het cluster zijn bewaard.

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

U moet een bestaande Azure SQL-database maken of hebben voordat u een aangepaste Hive-metastore instelt voor een HDInsight-cluster.  Zie [Snelstart: Maak één database in Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started?tabs=azure-portal)voor meer informatie.

Als u ervoor wilt zorgen dat uw HDInsight-cluster toegang heeft tot de verbonden Azure SQL Database, configureert u Azure SQL Database-firewallregels zodat Azure-services en -bronnen toegang krijgen tot de server.

U deze optie inschakelen in de Azure-portal door op **Serverfirewall instellen**te klikken en **onder** **Azure-services en -bronnen toestaan op deze server voor** de Azure SQL Database-server of -database toe te staan. Zie [IP-firewallregels maken en beheren](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#use-the-azure-portal-to-manage-server-level-ip-firewall-rules) voor meer informatie

![knop serverfirewall instellen](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![azure services toegang verlenen](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Een aangepaste metastore selecteren tijdens het maken van een cluster

U uw cluster tijdens het maken van een Azure SQL-database naar een eerder gemaakte Azure SQL-database wijzen of de SQL-database configureren nadat het cluster is gemaakt. Deze optie is opgegeven met de **instellingen voor Opslag > Metastore** terwijl u een nieuw Hadoop-, Spark- of interactief Hive-cluster maakt vanuit Azure-portal.

![Azure-portal voor HDInsight Hive-metagegevensarchief](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-best-practices"></a>Beste praktijken van Hive metastore

Hier zijn enkele algemene HDInsight Hive metastore best practices:

* Gebruik waar mogelijk een aangepaste metastore om compute resources (uw running cluster) en metadata (opgeslagen in de metastore) te scheiden.

* Begin met een S2-laag, die 50 DTU en 250 GB opslag biedt. Als u een knelpunt ziet, u de database opschalen.

* Als u meerdere HDInsight-clusters wilt openen om toegang te krijgen tot afzonderlijke gegevens, gebruikt u een aparte database voor de metastore op elk cluster. Als u een metastore deelt in meerdere HDInsight-clusters, betekent dit dat de clusters dezelfde metagegevens en onderliggende gebruikersgegevensbestanden gebruiken.

* Een back-up van uw aangepaste metastore periodiek. Azure SQL Database genereert automatisch back-ups, maar de tijdsperiode voor back-upretentie varieert. Zie [Meer informatie over automatische SQL Database-back-ups](../sql-database/sql-database-automated-backups.md)voor meer informatie.

* Zoek uw metastore- en HDInsight-cluster in dezelfde regio, voor de hoogste prestaties en de laagste netwerkuitgangskosten.

* Controleer uw metastore op prestaties en beschikbaarheid met azure SQL Database Monitoring-hulpprogramma's, zoals de Azure-portal of Azure Monitor-logboeken.

* Wanneer een nieuwe, hogere versie van Azure HDInsight wordt gemaakt ten opzichte van een bestaande aangepaste metastoredatabase, verbetert het systeem het schema van de metastore, wat onomkeerbaar is zonder de database te herstellen van back-up.

* Als u een metastore deelt in meerdere clusters, moet u ervoor zorgen dat alle clusters dezelfde HDInsight-versie hebben. Verschillende Hive-versies maken gebruik van verschillende metastoredatabaseschema's. U bijvoorbeeld geen metastore delen in clusters in Hive 2.1 en Hive 3.1.

* In HDInsight 4.0 gebruiken Spark en Hive onafhankelijke catalogi voor toegang tot SparkSQL- of Hive-tabellen. Een tabel die door Spark is gemaakt, bevindt zich in de Spark-catalogus. Een tabel gemaakt door Hive bevindt zich in de Hive-catalogus. Dit is anders dan HDInsight 3.6 waar Hive en Spark een gemeenschappelijke catalogus deelden. Hive en Spark Integration in HDInsight 4.0 is gebaseerd op Hive Warehouse Connector (HWC). HWC werkt als brug tussen Spark en Hive. [Meer informatie over Hive Warehouse Connector](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

## <a name="apache-oozie-metastore"></a>Apache Oozie metastore

Apache Oozie is een coördinatiesysteem voor werkstromen waarmee Hadoop-taken worden beheerd.  Oozie ondersteunt Hadoop-taken voor Apache MapReduce, Pig, Hive en anderen.  Oozie gebruikt een metastore om details over huidige en voltooide workflows op te slaan. Als u de prestaties wilt verhogen bij het gebruik van Oozie, u Azure SQL Database gebruiken als een aangepaste metastore. De metastore kan ook toegang bieden tot oozie-taakgegevens nadat u uw cluster hebt verwijderd.

Zie Apache Oozie gebruiken [voor werkstromen voor](hdinsight-use-oozie-linux-mac.md)instructies voor het maken van een Oozie-metastore met Azure SQL Database.

## <a name="custom-ambari-db"></a>Aangepaste Ambari-database

Zie [Aangepaste Apache Ambari-database](hdinsight-custom-ambari-db.md)als u uw eigen externe database wilt gebruiken met Apache Ambari op HDInsight.

## <a name="next-steps"></a>Volgende stappen

* [Clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](./hdinsight-hadoop-provision-linux-clusters.md)
