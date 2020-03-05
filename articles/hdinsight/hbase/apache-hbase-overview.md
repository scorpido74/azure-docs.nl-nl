---
title: Wat is Apache HBase in azure HDInsight?
description: Een inleiding tot Apache HBase in HDInsight, een NoSQL-database gebaseerd op Hadoop. Meer informatie over de toepassingsmogelijkheden en HBase vergelijken met andere Hadoop-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 03/03/2020
ms.openlocfilehash: 97814f4d22629fd74f395887a7361a3aabe55012
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78271838"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Wat is Apache HBase in azure HDInsight

[Apache HBase](https://hbase.apache.org/) is een open-source NoSQL-data base die is gebouwd op [Apache Hadoop](https://hadoop.apache.org/) en is gemodelleerd na [Google Bigtable](https://cloud.google.com/bigtable/). HBase biedt willekeurige toegang en een sterke consistentie voor grote hoeveelheden ongestructureerde en semigestructureerde gegevens in een database zonder schema die is georganiseerd op basis van kolomfamilies.

HBase is van gebruikers perspectief vergelijkbaar met een Data Base. Gegevens worden opgeslagen in de rijen en kolommen van een tabel, en gegevens in een rij worden gegroepeerd op kolom familie. HBase is een database zonder schema in de zin dat zowel de kolommen als het type gegevens dat hierin wordt opgeslagen niet hoeven te worden gedefinieerd voordat u ze kunt gebruiken. De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. Hiervoor kan gebruik worden gemaakt van gegevensredundantie, batchverwerking en andere functies die worden geboden door gedistribueerde toepassingen in het Hadoop-ecosysteem.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hoe is Apache HBase geïmplementeerd in azure HDInsight?

HDInsight HBase wordt aangeboden als een beheerd cluster dat is geïntegreerd in de Azure-omgeving. De clusters zijn geconfigureerd om gegevens rechtstreeks op te slaan in [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), wat een lage latentie en een verhoogde elasticiteit in de prestaties en kosten keuzen biedt. Hierdoor kunnen klanten interactieve websites bouwen die geschikt zijn voor grote gegevenssets. Daarnaast kunnen ze services bouwen voor het opslaan van sensor- en telemetriegegevens van miljoenen eindpunten en deze gegevens analyseren met Hadoop-taken. HBase en Hadoop vormen een goed startpunt voor big data-projecten in Azure. Met name omdat ze ervoor zorgen dat realtime toepassingen met grote gegevenssets kunnen werken.

De HDInsight-implementatie maakt gebruik van de opschaalbare architectuur van HBase om automatische sharding van tabellen, een sterke consistentie voor lees- en schrijfbewerkingen en automatische failover te bieden. De prestaties zijn verbeterd dankzij in-memory caching voor leesbewerkingen en streamen met een hoge gegevensdoorvoer voor schrijfbewerkingen. Een HBase-cluster kan worden gemaakt in het virtuele netwerk. Zie [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md) voor meer informatie.

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hoe worden gegevens in HDInsight HBase beheerd?

Gegevens kunnen worden beheerd in HBase met de opdrachten `create`, `get`, `put` en `scan` in de HBase-shell. Gegevens worden met `put` naar de database geschreven en gelezen met `get`. De opdracht `scan` wordt gebruikt om gegevens uit meerdere rijen in een tabel op te halen. Gegevens kunnen ook worden beheerd met de HBase C# API, die naast de HBase REST API een clientbibliotheek biedt. Er kan ook een query worden uitgevoerd op een HBase-data base met behulp van [Apache Hive](https://hive.apache.org/). Zie aan de [slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)voor een inleiding tot deze programmeer modellen. Er zijn ook coprocessoren beschikbaar die gegevens verwerking toestaan in de knoop punten die als host fungeren voor de data base.

> [!NOTE]  
> Thrift wordt niet ondersteund door HBase in HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Gebruiks voorbeelden voor Apache HBase

De canonieke use-case waarvoor BigTable (en op uitbrei ding, HBase) is gemaakt op basis van webzoekopdracht. Zoekmachines bouwen indexen die termen koppelen aan de webpagina's die deze termen bevatten. Er zijn echter tal van andere gebruiksvoorbeeld waarvoor HBase geschikt is. Enkele daarvan worden gespecificeerd in deze sectie.

|Scenario |Beschrijving |
|---|---|
|Sleutel-waardearchief|HBase kan worden gebruikt als sleutel-value Store en is geschikt voor het beheren van bericht systemen. Facebook maakt gebruik van HBase voor hun berichten systeem en is ideaal voor het opslaan en beheren van Internet communicatie. WebTable gebruikt HBase om tabellen die worden geëxtraheerd uit webpagina's te zoeken en beheren.|
|Sensorgegevens|HBase is handig wanneer u gegevens wilt vastleggen die stapsgewijs uit diverse bronnen worden verzameld. Dit omvat sociale analyses, time series, het up-to-date houden van interactieve Dash boards met trends en prestatie meter items en het beheren van audit logboek systemen. Voorbeelden zijn Bloomberg-traderterminal en de Open Time Series Database (OpenTSDB) voor het opslaan van en bieden van toegang tot metrische gegevens die worden verzameld over de status van serversystemen.|
|Realtime query|[Apache Phoenix](https://phoenix.apache.org/) is een SQL-query-engine voor Apache HBase. Het is toegankelijk als een JDBC-stuur programma en Hiermee kunt u HBase-tabellen opvragen en beheren met behulp van SQL.|
|HBase als een platform|Toepassingen kunnen worden uitgevoerd in HBase door HBase te gebruiken als gegevensopslag. Voor beelden zijn Phoenix, [OpenTSDB](http://opentsdb.net/), Kiji en Titan. Toepassingen kunnen ook worden geïntegreerd met HBase. Voor beelden zijn [Apache Hive](https://hive.apache.org/), [Apache Pig](https://pig.apache.org/), [solr](https://lucene.apache.org/solr/), [Apache Storm](https://storm.apache.org/), [Apache Flume](https://flume.apache.org/), [Apache Impala](https://impala.apache.org/), [Apache Spark](https://spark.apache.org/) , [ganglia](http://ganglia.info/)en [Apache boren](https://drill.apache.org/).|

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Apache HBase-replicatie in HDInsight configureren](apache-hbase-replication.md)
