---
title: Wat is Apache HBase in azure HDInsight?
description: Een inleiding tot Apache HBase in HDInsight, een NoSQL-database gebaseerd op Hadoop. Meer informatie over de toepassingsmogelijkheden en HBase vergelijken met andere Hadoop-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: afbf9aff09999a34a84d55634a868250fbb6d1ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82188957"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Wat is Apache HBase in azure HDInsight

[Apache HBase](https://hbase.apache.org/) is een open-source NoSQL-data base die is gebouwd op Apache Hadoop en is gemodelleerd na [Google Bigtable](https://cloud.google.com/bigtable/). HBase biedt wille keurige toegang en sterke consistentie voor grote hoeveel heden gegevens in een schemaloze data base. De data base is ingedeeld op kolom families.

HBase is van gebruikers perspectief vergelijkbaar met een Data Base. Gegevens worden opgeslagen in de rijen en kolommen van een tabel, en gegevens in een rij worden gegroepeerd op kolom familie. HBase is een schemaloze data base. De kolommen en gegevens typen kunnen ongedefinieerd zijn voordat u ze kunt gebruiken. De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. Dit kan afhankelijk zijn van gegevens redundantie, batch verwerking en andere functies die worden geleverd door gedistribueerde toepassingen in de Hadoop-omgeving.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Hoe is Apache HBase geïmplementeerd in azure HDInsight?

HDInsight HBase wordt aangeboden als een beheerd cluster dat is geïntegreerd in de Azure-omgeving. De clusters zijn geconfigureerd om gegevens rechtstreeks op te slaan in [Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), wat een lage latentie en een verhoogde elasticiteit in de prestaties en kosten keuzen biedt. Met deze eigenschap kunnen klanten interactieve websites bouwen die werken met grote gegevens sets. Voor het bouwen van services die Sens oren en telemetriegegevens van miljoenen eind punten opslaan. En om deze gegevens te analyseren met Hadoop-taken. HBase en Hadoop zijn goede start punten voor big data project in Azure. Met de services kunnen realtime-toepassingen werken met grote gegevens sets.

De HDInsight-implementatie maakt gebruik van de scale-out-architectuur van HBase om automatische sharding van tabellen te bieden. En sterke consistentie voor lees-en schrijf bewerkingen en automatische failover. De prestaties zijn verbeterd dankzij in-memory caching voor leesbewerkingen en streamen met een hoge gegevensdoorvoer voor schrijfbewerkingen. Een HBase-cluster kan worden gemaakt in het virtuele netwerk. Zie [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md) voor meer informatie.

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>Hoe worden gegevens in HDInsight HBase beheerd?

Gegevens kunnen worden beheerd in HBase met de opdrachten `create`, `get`, `put` en `scan` in de HBase-shell. Gegevens worden met `put` naar de database geschreven en gelezen met `get`. De opdracht `scan` wordt gebruikt om gegevens uit meerdere rijen in een tabel op te halen. Gegevens kunnen ook worden beheerd met de HBase C# API, die naast de HBase REST API een clientbibliotheek biedt. Er kan ook een query worden uitgevoerd op een HBase-data base met behulp van [Apache Hive](https://hive.apache.org/). Zie aan de [slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)voor een inleiding tot deze programmeer modellen. Er zijn ook coprocessoren beschikbaar die gegevens verwerking toestaan in de knoop punten die als host fungeren voor de data base.

> [!NOTE]  
> Thrift wordt niet ondersteund door HBase in HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Gebruiks voorbeelden voor Apache HBase

De canonieke use-case waarvoor BigTable (en op uitbrei ding, HBase) is gemaakt op basis van webzoekopdracht. Zoekmachines bouwen indexen die termen koppelen aan de webpagina's die deze termen bevatten. Er zijn echter tal van andere gebruiksvoorbeeld waarvoor HBase geschikt is. Enkele daarvan worden gespecificeerd in deze sectie.

|Scenario |Beschrijving |
|---|---|
|Sleutel-waardearchief|HBase kan worden gebruikt als sleutel-value Store en is geschikt voor het beheren van bericht systemen. Facebook maakt gebruik van HBase voor hun berichten systeem en is ideaal voor het opslaan en beheren van Internet communicatie. WebTable gebruikt HBase om tabellen die worden geëxtraheerd uit webpagina's te zoeken en beheren.|
|Sensorgegevens|HBase is handig wanneer u gegevens wilt vastleggen die stapsgewijs uit diverse bronnen worden verzameld. Deze gegevens zijn onder andere sociale analyses en tijd reeksen. En interactieve Dash boards up-to-date te houden met trends en prestatie meter items en controle logboek systemen te beheren. Voor beelden zijn Bloomberg handelaar Terminal en de open time series-data base (OpenTSDB). OpenTSDB slaat en biedt toegang tot metrische gegevens die zijn verzameld over de status van server systemen.|
|Realtime query|[Apache Phoenix](https://phoenix.apache.org/) is een SQL-query-engine voor Apache HBase. Het is toegankelijk als een JDBC-stuur programma en Hiermee kunt u HBase-tabellen opvragen en beheren met behulp van SQL.|
|HBase als een platform|Toepassingen kunnen worden uitgevoerd in HBase door HBase te gebruiken als gegevensopslag. Voor beelden zijn Phoenix, OpenTSDB `Kiji`, en Titan. Toepassingen kunnen ook worden geïntegreerd met HBase. Voor beelden zijn: [Apache Hive](https://hive.apache.org/), Apache Pig, [solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark `Ganglia`, en Apache Drill.|

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Apache HBase met Apache Hadoop in HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [HDInsight-clusters maken in Azure Virtual Network](./apache-hbase-provision-vnet.md)
* [Apache HBase-replicatie in HDInsight configureren](apache-hbase-replication.md)
