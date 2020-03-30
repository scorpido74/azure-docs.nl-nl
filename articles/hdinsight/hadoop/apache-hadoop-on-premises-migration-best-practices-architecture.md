---
title: 'Architectuur: On-premises Apache Hadoop naar Azure HDInsight'
description: Leer best practices voor architectuur voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 2d0d5bb871612bc5e16a26eb49808c39661ffb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934690"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>On-premises Apache Hadoop-clusters migreren naar Azure HDInsight - aanbevolen procedures voor architectuur

In dit artikel worden aanbevelingen gegeven voor de architectuur van Azure HDInsight-systemen. Het maakt deel uit van een serie die best practices biedt om te helpen bij het migreren van on-premises Apache Hadoop-systemen naar Azure HDInsight.

## <a name="use-multiple-workload-optimized-clusters"></a>Meerdere voor workloads geoptimaliseerde clusters gebruiken

Veel on-premises Apache Hadoop-implementaties bestaan uit één groot cluster dat veel workloads ondersteunt. Dit ene cluster kan complex zijn en kan compromissen vereisen voor de afzonderlijke services om alles samen te laten werken. Het migreren van on-premises Hadoop-clusters naar Azure HDInsight vereist een wijziging in de aanpak.

Azure HDInsight-clusters zijn ontworpen voor een specifiek type rekengebruik. Omdat opslag kan worden gedeeld in meerdere clusters, is het mogelijk om meerdere voor workloads geoptimaliseerde compute clusters te maken om te voldoen aan de behoeften van verschillende taken. Elk clustertype heeft de optimale configuratie voor die specifieke werkbelasting. In de volgende tabel worden de ondersteunde clustertypen in HDInsight en de bijbehorende workloads weergegeven.

|Workload|HDInsight-clustertype|
|---|---|
|Batchverwerking (ETL / ELT)|Hadoop, Vonk|
|Datawarehousing|Hadoop, Spark, Interactieve Query|
|IoT / Streaming|Kafka, Storm, Vonk|
|NoSQL Transactionele verwerking|HBase|
|Interactieve en snellere query's met in-memory caching|Interactive Query|
|Data Science|ML-services, Spark|

In de volgende tabel worden de verschillende methoden weergegeven die kunnen worden gebruikt om een HDInsight-cluster te maken.

|Hulpprogramma|Browser gebaseerd|Opdrachtregel|REST API|SDK|
|---|---|---|---|---|
|[Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure-gegevensfabriek](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (ver 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[Curl](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet)||||X|
|[Python SDK](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python)||||X|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable)||||X|
|[Azure Resource Manager-sjablonen](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Zie voor meer informatie het artikel [Clustertypen in HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Tijdelijke on-demandclusters gebruiken

HDInsight-clusters kunnen voor langere tijd ongebruikt blijven. Om te helpen besparen op resourcekosten, ondersteunt HDInsight on-demand tijdelijke clusters, die kunnen worden verwijderd nadat de werkbelasting is voltooid.

Wanneer u een cluster verwijdert, worden het bijbehorende opslagaccount en externe metagegevens niet verwijderd. Het cluster kan later opnieuw worden gemaakt met dezelfde opslagaccounts en meta-opslag.

Azure Data Factory kan worden gebruikt om het maken van on-demand HDInsight-clusters te plannen. Zie voor meer informatie het artikel [On-demand Apache Hadoop-clusters maken in HDInsight met Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Opslag loskoppelen van compute

Typische on-premises Hadoop-implementaties gebruiken dezelfde set machines voor gegevensopslag en gegevensverwerking. Omdat ze samen zijn geplaatst, moeten rekenkracht en opslag worden samengevoegd.

Op HDInsight-clusters hoeft opslag niet te worden gekoppeld aan compute en kan deze zich bevinden in Azure-opslag, Azure Data Lake Storage of beide. Het loskoppelen van opslag van rekenkracht heeft de volgende voordelen:

- Het delen van gegevens tussen clusters.
- Gebruik van tijdelijke clusters omdat de gegevens niet afhankelijk zijn van het cluster.
- Lagere opslagkosten.
- Opslag schalen en afzonderlijk berekenen.
- Gegevensreplicatie in verschillende regio's.

Compute clusters worden gemaakt in de buurt van opslagaccountbronnen in een Azure-regio om de prestatiekosten van het scheiden van rekenkracht en opslag te beperken. Snelle netwerken maken het efficiënt voor de compute nodes om toegang te krijgen tot de gegevens in Azure-opslag.

## <a name="use-external-metadata-stores"></a>Externe metagegevensopslag gebruiken

Er zijn twee belangrijke metastores die werken met HDInsight clusters: [Apache Hive](https://hive.apache.org/) en [Apache Oozie](https://oozie.apache.org/). De Hive metastore is de centrale schemarepository die kan worden gebruikt door gegevensverwerkingsengines, waaronder Hadoop, Spark, LLAP, Presto en Apache Pig. De Oozie metastore slaat details op over de planning en de status van in uitvoering en voltooide Hadoop-taken.

HDInsight maakt gebruik van Azure SQL Database voor Hive- en Oozie-metastores. Er zijn twee manieren om een metastore in HDInsight-clusters op te zetten:

1. Standaardmetastore

    - Geen extra kosten.
    - Metastore wordt verwijderd wanneer het cluster wordt verwijderd.
    - Metastore kan niet worden gedeeld tussen verschillende clusters.
    - Maakt gebruik van basic Azure SQL DB, die een limiet van vijf DTU heeft.

1. Aangepaste externe metastore

    - Geef een externe Azure SQL Database op als metastore.
    - Clusters kunnen worden gemaakt en verwijderd zonder metadata te verliezen, waaronder Hive-schema Oozie-taakgegevens.
    - Single metastore db kan worden gedeeld met verschillende soorten clusters.
    - Metastore kan naar behoefte worden opgeschaald.
    - Zie [Externe metagegevensopslag gebruiken in Azure HDInsight](../hdinsight-use-external-metadata-stores.md)voor meer informatie.

## <a name="best-practices-for-hive-metastore"></a>Aanbevolen procedures voor Hive Metastore

Sommige HDInsight Hive metastore best practices zijn als volgt:

- Gebruik een aangepaste externe metastore om compute resources en metadata te scheiden.
- Begin met een S2-laags Azure SQL-exemplaar, dat 50 DTU en 250 GB opslag biedt. Als u een knelpunt ziet, u de database opschalen.
- Deel de metastore die is gemaakt voor één HDInsight-clusterversie niet met clusters van een andere versie. Verschillende Hive-versies maken gebruik van verschillende schema's. Een metastore kan bijvoorbeeld niet worden gedeeld met zowel Hive 1.2- als Hive 2.1-clusters.
- Een back-up van de aangepaste metastore periodiek.
- Houd het cluster metastore en HDInsight in dezelfde regio.
- Controleer de metastore op prestaties en beschikbaarheid met Azure SQL Database Monitoring-hulpprogramma's, zoals Azure-portal- of Azure Monitor-logboeken.
- Voer `ANALYZE TABLE` de opdracht uit die nodig is om statistieken voor tabellen en kolommen te genereren. Bijvoorbeeld `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Aanbevolen procedures voor verschillende workloads

- Overweeg het LLAP-cluster te gebruiken voor interactieve Hive-query's met een verbeterde responstijd [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) is een nieuwe functie in Hive 2.0 waarmee query's in het geheugen kunnen worden incaches. LLAP maakt Hive-query's veel sneller, tot [26x sneller dan Hive 1.x in sommige gevallen.](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/)
- Overweeg spark-taken te gebruiken in plaats van Hive-taken.
- Overweeg om impala-gebaseerde query's te vervangen door LLAP-query's.
- Overweeg map te vervangenWerk verminderen met Spark-taken.
- Overweeg om sparkbatchtaken met lage latentie te vervangen met Spark Structured Streaming-taken.
- Overweeg Azure Data Factory (ADF) 2.0 te gebruiken voor gegevensorkestratie.
- Overweeg Ambari voor clusterbeheer.
- Gegevensopslag wijzigen van on-premises HDFS naar WASB of ADLS of ADFS voor het verwerken van scripts.
- Overweeg ranger RBAC te gebruiken op Hive-tabellen en auditing.
- Overweeg cosmosdb te gebruiken in plaats van MongoDB of Cassandra.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Aanbevolen procedures voor infrastructuur voor on-premises naar Azure HDInsight Hadoop-migratie](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)
