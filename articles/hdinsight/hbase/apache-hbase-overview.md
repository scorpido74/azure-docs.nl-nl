---
title: Wat is Apache HBase in Azure HDInsight?
description: Een inleiding tot Apache HBase in HDInsight, een NoSQL-database gebaseerd op Hadoop. Meer informatie over de toepassingsmogelijkheden en HBase vergelijken met andere Hadoop-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: e977d4d68a330f57c4b53da4270e4515d4e69ee0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729809"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Wat is Apache HBase in Azure HDInsight

[Apache HBase](https://hbase.apache.org/) is een open-source, NoSQL-database die is gebouwd op Apache Hadoop en gemodelleerd naar [Google BigTable.](https://cloud.google.com/bigtable/) HBase biedt willekeurige toegang en sterke consistentie voor grote hoeveelheden gegevens in een schemaloze database. De database wordt georganiseerd door kolomfamilies.

Vanuit het perspectief van de gebruiker is HBase vergelijkbaar met een database. Gegevens worden opgeslagen in de rijen en kolommen van een tabel en gegevens in een rij worden gegroepeerd op kolomfamilie. HBase is een schemaloze database. De kolommen en gegevenstypen kunnen niet worden gedefinieerd voordat u ze gebruikt. De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. Het kan vertrouwen op gegevensredundantie, batchverwerking en andere functies die worden geleverd door gedistribueerde toepassingen in de Hadoop-omgeving.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hoe wordt Apache HBase geïmplementeerd in Azure HDInsight?

HDInsight HBase wordt aangeboden als een beheerd cluster dat is geïntegreerd in de Azure-omgeving. De clusters zijn geconfigureerd om gegevens rechtstreeks op te slaan in [Azure Storage,](./../hdinsight-hadoop-use-blob-storage.md)wat een lage latentie en een grotere elasticiteit biedt in prestatie- en kostenkeuzes. Deze eigenschap stelt klanten in staat om interactieve websites te bouwen die werken met grote datasets. Services bouwen die sensor- en telemetriegegevens van miljoenen eindpunten opslaan. En om deze gegevens te analyseren met Hadoop jobs. HBase en Hadoop zijn goede uitgangspunten voor big data-projecten in Azure. De services kunnen real-time toepassingen in staat stellen om te werken met grote datasets.

De HDInsight-implementatie maakt gebruik van de scale-out architectuur van HBase om automatisch te sharden van tabellen. En sterke consistentie voor leest en schrijft, en automatische failover. De prestaties zijn verbeterd dankzij in-memory caching voor leesbewerkingen en streamen met een hoge gegevensdoorvoer voor schrijfbewerkingen. Een HBase-cluster kan worden gemaakt in het virtuele netwerk. Zie [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md) voor meer informatie.

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hoe worden gegevens in HDInsight HBase beheerd?

Gegevens kunnen worden beheerd in HBase met de opdrachten `create`, `get`, `put` en `scan` in de HBase-shell. Gegevens worden met `put` naar de database geschreven en gelezen met `get`. De opdracht `scan` wordt gebruikt om gegevens uit meerdere rijen in een tabel op te halen. Gegevens kunnen ook worden beheerd met de HBase C# API, die naast de HBase REST API een clientbibliotheek biedt. Een HBase-database kan ook worden opgevraagd met behulp van [Apache Hive](https://hive.apache.org/). Zie [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)voor een inleiding tot deze programmeermodellen. Er zijn ook coprocessors beschikbaar, waarmee gegevensverwerking mogelijk is in de knooppunten die de database hosten.

> [!NOTE]  
> Thrift wordt niet ondersteund door HBase in HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Use cases voor Apache HBase

De canonieke use case waarvoor BigTable (en bij uitbreiding, HBase) is gemaakt op basis van zoeken op het web. Zoekmachines bouwen indexen die termen koppelen aan de webpagina's die deze termen bevatten. Er zijn echter tal van andere gebruiksvoorbeeld waarvoor HBase geschikt is. Enkele daarvan worden gespecificeerd in deze sectie.

|Scenario |Beschrijving |
|---|---|
|Sleutel-waardearchief|HBase kan worden gebruikt als een key-value winkel, en het is geschikt voor het beheer van berichtensystemen. Facebook gebruikt HBase voor hun berichtensysteem en is ideaal voor het opslaan en beheren van internetcommunicatie. WebTable gebruikt HBase om tabellen die worden geëxtraheerd uit webpagina's te zoeken en beheren.|
|Sensorgegevens|HBase is handig wanneer u gegevens wilt vastleggen die stapsgewijs uit diverse bronnen worden verzameld. Deze gegevens omvatten sociale analyses en tijdreeksen. En interactieve dashboards up-to-date houden met trends en tellers en het beheren van auditlogsystemen. Voorbeelden hiervan zijn Bloomberg trader terminal en de Open Time Series Database (OpenTSDB). OpenTSDB slaat op en biedt toegang tot statistieken die zijn verzameld over de status van serversystemen.|
|Realtime query|[Apache Phoenix](https://phoenix.apache.org/) is een SQL-queryengine voor Apache HBase. Het is toegankelijk als een JDBC-stuurprogramma en maakt het mogelijk om HBase-tabellen op te vragen en te beheren met SQL.|
|HBase als een platform|Toepassingen kunnen worden uitgevoerd in HBase door HBase te gebruiken als gegevensopslag. Voorbeelden hiervan zijn Phoenix, OpenTSDB `Kiji`en Titan. Toepassingen kunnen ook worden geïntegreerd met HBase. Voorbeelden hiervan zijn: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark, `Ganglia`en Apache Drill.|

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Apache HBase-replicatie configureren in HDInsight](apache-hbase-replication.md)
