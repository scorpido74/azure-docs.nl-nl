---
title: Overzicht van HDInsight 4,0-Azure
description: HDInsight 3.6 vergelijken met functies, beperkingen en aanbevelingen voor upgrades van HDInsight 4.0.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 0463e3297bbb2fda50adfeefaa89f0a7a3ef8b0a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901531"
---
# <a name="azure-hdinsight-40-overview"></a>Overzicht van Azure HDInsight 4,0

Azure HDInsight is onder zakelijke klanten een van de meest populaire services voor open source Apache Hadoop- en Apache Spark-analyses in Azure. HDInsight 4,0 is een Cloud distributie van Apache Hadoop-onderdelen. Dit artikel bevat informatie over de meest recente Azure HDInsight-release en over hoe u moet upgraden.

## <a name="whats-new-in-hdinsight-40"></a>Wat is er nieuw in HDInsight 4,0?

### <a name="apache-hive-30-and-llap"></a>Apache Hive 3.0 en LLAP

Voor analytische verwerking met lage latentie (LLAP) in Apache Hive wordt gebruikgemaakt van permanente query-servers en caching in geheugen om snel resultaten van een SQL-query op gegevens in een externe cloudopslag te leveren. LLAP in Hive maakt gebruikt van een set permanente daemons die fragmenten van Hive-query's uitvoeren. Query's uitvoeren in LLAP is vergelijkbaar met Hive zonder LLAP, maar werkroltaken worden in LLAP-daemons uitgevoerd in plaats van in containers.

Voordelen van LLAP in Hive zijn:

* De mogelijkheid om uitgebreide SQL-analyses uit te voeren, bijvoorbeeld complexe samenvoegingen, subquery's, vensterfuncties, sorteren, door de gebruiker gedefinieerde functies en complexe aggregaties, zonder verlies van de prestaties en schaalbaarheid.

* Interactieve query's op gegevens in dezelfde opslag waar de gegevens worden voorbereid, zodat u voor analytische verwerking geen gegevens hoeft te verplaatsen van een opslag naar een andere engine.

* De queryresultaten kunnen in de cache worden opgeslagen, zodat eerder berekende queryresultaten opnieuw kunnen worden gebruikt, wat tijd en middelen bespaart die anders zouden worden gebruikt om voor de query vereiste clustertaken uit te voeren.

### <a name="hive-dynamic-materialized-views"></a>Dynamisch gerealiseerde weergaven in Hive

Hive ondersteunt nu dynamisch gerealiseerde weergaven of een berekening vooraf van relevante samenvattingen die worden gebruikt om de queryverwerking in datawarehouses te versnellen. Gerealiseerde weergaven worden in Hive op het systeem bewaard en gebruiken naadloos LLAP-versnelling.

### <a name="hive-transactional-tables"></a>Transactionele Hive-tabellen

HDI 4.0 bevat Apache Hive 3, dat ACID-naleving (atoomvorm, consistentie, isolatie en duurzaamheid) vereist voor transactionele tabellen die in het Hive-warehouse worden bewaard. ACID-compatibele tabellen en tabelgegevens worden via Hive geopend en beheerd. Gegevens in CRUD-tabellen (maken, ophalen, bijwerken en verwijderen) moeten de ORC-bestandsindeling (geoptimaliseerde rijkolom) hebben, maar tabellen waarin alleen gegevens worden ingevoerd, ondersteunen alle bestandsindelingen.

* ACID v2 heeft prestatieverbeteringen in zowel het opslagformaat als de engine voor uitvoerbewerkingen.

* ACID is standaard ingeschakeld voor ondersteuning voor alle gegevensupdates.

* Met verbeterde ACID-mogelijkheden kunt u op rijniveau bijwerken en verwijderen.

* Er is geen prestatieoverhead.

* Er is geen bucketing vereist.

* Spark kan naar ACID-tabellen in Hive lezen en schrijven via Hive Warehouse Connector.

Meer informatie over [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Apache Spark ontvang bij te werken tabellen en ACID-transacties via de Hive Warehouse Connector. Met Hive Warehouse Connector registreert u transactionele Hive-tabellen als externe tabellen in Spark voor volledige transactionele functionaliteit. Eerdere versies ondersteunden alleen manipulatie van partitietabellen. Hive Warehouse Connector ondersteunt ook Streaming DataFrames om vanuit Spark lees- en schrijfbewerkingen te streamen naar transactionele en streaming Hive-tabellen.

Spark-executors maken rechtstreeks verbinding met LLAP-daemons van Hive om gegevens op een transactionele manier op te halen en bij te werken, waardoor Hive de controle houdt over de gegevens.

Apache Spark in HDInsight 4.0 ondersteunt de volgende scenario's:

* Training voor machine learning-modellen uitvoeren voor hetzelfde transactionele tabel dat wordt gebruikt voor rapportage.
* ACID-transacties gebruiken om kolommen van Spark ML veilig toe te voegen aan een Hive-tabel.
* Een Spark-streamingtaak uitvoeren in de wijzigingsfeed van een streaming Hive-tabel.
* ORC-bestanden rechtstreeks vanuit een Spark Structured Streaming-taak maken.

U hoeft u geen zorgen te maken dat u per ongeluk rechtstreeks vanuit Spark transactionele Hive-tabellen opent, wat zou leiden tot inconsistente resultaten, dubbele gegevens of de beschadiging van gegevens. In HDInsight 4,0 worden Spark-tabellen en Hive-tabellen opgeslagen in afzonderlijke meta Stores. Gebruik de Hive Data Warehouse Connector om transactionele Hive-tabellen expliciet te registreren als externe Spark-tabellen.

Meer informatie over [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie 4.3.1 is opgenomen in HDI 4.0 met de volgende wijzigingen:

* Oozie voert Hive-acties niet langer uit. De Hive CLI is verwijderd en vervangen door BeeLine.

* U sluit ongewenste afhankelijkheden uit van ShareLib door een uitsluitpatroon op te nemen in het bestand **job.properties**.

Meer informatie over [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Upgraden naar HDInsight 4,0

Zoals bij elke grote release is het belangrijk om uw onderdelen uitgebreid te testen voordat u de nieuwste versie in een productieomgeving implementeert. HDInsight 4,0 is beschikbaar voor u om het upgrade proces te starten, maar HDInsight 3,6 is de standaard optie om onbedoelde Mishaps te voor komen.

Er wordt geen ondersteund upgradepad van eerdere versies van HDInsight naar HDInsight 4,0. Omdat de indelingen van meta Store en BLOB-gegevens zijn gewijzigd, is HDInsight 4,0 niet compatibel met eerdere versies. Het is belang rijk dat u uw nieuwe HDInsight 4,0-omgeving gescheiden blijft van uw huidige productie omgeving. Als u HDInsight 4,0 op uw huidige omgeving implementeert, wordt uw meta Store bijgewerkt en kan deze niet worden omgekeerd.  

## <a name="limitations"></a>Beperkingen

* HDInsight 4,0 biedt geen ondersteuning voor MapReduce voor Apache Hive. Gebruik in plaats daarvan Apache Tez. Meer informatie over [Apache Tez](https://tez.apache.org/).
* HDInsight 4,0 biedt geen ondersteuning voor Apache Storm.
* De Hive-weer gave is niet meer beschikbaar in HDInsight 4,0.
* Shell-interpreter in Apache Zeppelin wordt niet ondersteund in Spark-en interactieve query clusters.
* U kunt LLAP in een Apache Spark-LLAP-cluster niet *uitschakelen*. U kunt alleen LLAP uitschakelen.
* Azure Data Lake Storage Gen2 kan Juypter-notitieblokken niet opslaan in een Apache Spark-cluster.

## <a name="next-steps"></a>Volgende stappen

* [Documentatie voor Azure HDInsight](index.yml)
* [Releaseopmerkingen](hdinsight-release-notes.md)
