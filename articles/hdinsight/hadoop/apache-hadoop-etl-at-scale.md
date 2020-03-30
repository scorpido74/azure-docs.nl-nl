---
title: ETL extraheren, transformeren en laden op schaal - Azure HDInsight
description: Ontdek hoe extract, transformatie en belasting wordt gebruikt in HDInsight met Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: f4be3343f090c4d31ccb85eba8e99f22a3b1fcae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529472"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>ETL op schaal extraheren, transformeren en laden (ETL)

Extraheren, transformeren en laden (ETL) is het proces waarbij gegevens worden verkregen uit verschillende bronnen, verzameld op een standaardlocatie, worden gereinigd en verwerkt en uiteindelijk worden geladen in een datastore waaruit deze kan worden opgevraagd. Legacy ETL verwerkt importgegevens, reinigt deze op zijn plaats en slaat deze op in een relationele data-engine. Met HDInsight ondersteunt een breed scala aan Apache Hadoop-ecosysteemcomponenten het uitvoeren van ETL op schaal.

Het gebruik van HDInsight in het ETL-proces kan worden samengevat door deze pijplijn:

![HDInsight ETL op schaal overzicht](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

De volgende secties verkennen elk van de ETL-fasen en de bijbehorende componenten.

## <a name="orchestration"></a>Orkestratie

Orchestration bestrijkt alle fasen van de ETL-pijplijn. ETL jobs in HDInsight hebben vaak te maken met verschillende producten die samenwerken.  U Hive gebruiken om een deel van de gegevens schoon te maken, terwijl Pig een ander gedeelte reinigt.  U Azure Data Factory gebruiken om gegevens te laden in Azure SQL Database vanuit Azure Data Lake Store.

Orkestratie is nodig om de juiste taak op het juiste moment uit te voeren.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie is een coördinatiesysteem voor werkstromen waarmee Hadoop-taken worden beheerd. Oozie draait binnen een HDInsight cluster en is geïntegreerd met de Hadoop stack. Oozie ondersteunt Hadoop banen voor Apache Hadoop MapReduce, Apache Pig, Apache Hive, en Apache Sqoop. Oozie kan ook worden gebruikt om taken te plannen die specifiek zijn voor een systeem, zoals Java-programma's of shell scripts.

Zie [Apache Oozie gebruiken met Apache Hadoop voor](../hdinsight-use-oozie-linux-mac.md) meer informatie om een werkstroom op HDInsight te definiëren en uit te voeren Voor een diepe duik die laat zien hoe u Oozie gebruiken om een end-to-end pijplijn te rijden, raadpleegt u [De gegevenspijplijn operationeel maken](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory biedt orchestration-mogelijkheden in de vorm van platform-as-a-service. Het is een cloudgebaseerde data-integratieservice waarmee u datagestuurde workflows in de cloud maken voor het orkestreren en automatiseren van gegevensverplaatsing en gegevenstransformatie.

Met Azure Data Factory u het als nog niet doen:

1. Gegevensgestuurde werkstromen (pijplijnen genoemd) maken en plannen die gegevens opnemen uit verschillende gegevensarchieven.
2. De gegevens verwerken en transformeren met behulp van compute services zoals Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch en Azure Machine Learning.
3. Uitvoergegevens publiceren naar gegevensopslaglocaties als Azure SQL Data Warehouse, waar BI-toepassingen (business intelligence) er gebruik van kunnen maken.

Zie de [documentatie](../../data-factory/introduction.md)voor meer informatie over Azure Data Factory.

## <a name="ingest-file-storage-and-result-storage"></a>Inname van bestandsopslag en resultaatopslag

Brongegevensbestanden worden meestal geladen naar een locatie in Azure Storage of Azure Data Lake Storage. Bestanden kunnen in elk formaat zijn, maar meestal zijn het platte bestanden zoals CSV's.

### <a name="azure-storage"></a>Azure Storage

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) heeft specifieke schaalbaarheidsdoelen. Zie [Schaalbaarheids- en prestatiedoelen voor Blob-opslag voor](../../storage/blobs/scalability-targets.md)meer informatie. Voor de meeste analytische knooppunten schaalt Azure Storage het beste bij het omgaan met veel kleinere bestanden.  Azure Storage garandeert dezelfde prestaties, ongeacht hoeveel bestanden of hoe groot de bestanden zijn (zolang u zich binnen uw grenzen bevindt).  Dit betekent dat u terabytes aan gegevens opslaan en toch consistente prestaties krijgen, of u nu een subset van de gegevens of alle gegevens gebruikt.

Azure Storage heeft verschillende typen blobs.  Een *toevoegenblob* is een geweldige optie voor het opslaan van weblogs of sensorgegevens.  

Meerdere blobs kunnen over veel servers worden verdeeld om de toegang tot deze servers uit te schalen, maar één blob kan slechts door één server worden bediend. Hoewel blobs logisch kunnen worden gegroepeerd in blobcontainers, zijn er geen implicaties voor partitionering van deze groepering.

Azure Storage heeft ook een WebHDFS API-laag voor de blob-opslag.  Alle services in HDInsight hebben toegang tot bestanden in Azure Blob Storage voor gegevensreiniging en gegevensverwerking, vergelijkbaar met de manier waarop deze services Hadoop Distributed Files System (HDFS) zouden gebruiken.

Gegevens worden meestal opgenomen in Azure Storage met PowerShell, de Azure Storage SDK of AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) is een beheerde, hyperscale repository voor analysegegevens die compatibel is met HDFS.  ADLS maakt gebruik van een ontwerpparadigma dat vergelijkbaar is met HDFS en biedt onbeperkte schaalbaarheid in termen van totale capaciteit en de grootte van afzonderlijke bestanden. ADLS is zeer goed bij het werken met grote bestanden, omdat een groot bestand kan worden opgeslagen op meerdere knooppunten.  Het partitioneren van gegevens in ADLS gebeurt achter de schermen.  U krijgt enorme doorvoer om analytische taken uit te voeren met duizenden gelijktijdige uitvoerders die op efficiënte wijze honderden terabytes aan gegevens lezen en schrijven.

Gegevens worden meestal opgenomen in ADLS met Azure Data Factory, ADLS SDKs, AdlCopy Service, Apache DistCp of Apache Sqoop.  Welke van deze diensten te gebruiken hangt grotendeels af van waar de gegevens is.  Als de gegevens zich momenteel in een bestaand Hadoop-cluster bevinden, u Apache DistCp, AdlCopy-service of Azure Data Factory gebruiken.  Als het zich in Azure Blob Storage bevindt, u Azure Data Lake Storage .NET SDK, Azure PowerShell of Azure Data Factory gebruiken.

ADLS is ook geoptimaliseerd voor het innemen van gebeurtenissen met Azure Event Hub of Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Overwegingen voor beide opslagopties

Voor het uploaden van gegevenssets in het terabyte-bereik kan de netwerklatentie een groot probleem zijn, vooral als de gegevens afkomstig zijn van een on-premises locatie.  In dergelijke gevallen u de onderstaande opties gebruiken:

* Azure ExpressRoute: met Azure ExpressRoute u privéverbindingen maken tussen Azure-datacenters en uw on-premises infrastructuur. Deze verbindingen bieden een betrouwbare optie voor het overbrengen van grote hoeveelheden gegevens. Zie [Azure ExpressRoute-documentatie](../../expressroute/expressroute-introduction.md)voor meer informatie.

* "Offline" uploaden van gegevens. U [azure import/exportservice](../../storage/common/storage-import-export-service.md) gebruiken om harde schijven met uw gegevens naar een Azure-datacenter te verzenden. Uw gegevens worden eerst geüpload naar Azure Storage Blobs. U azure [data factory](../../data-factory/connector-azure-data-lake-store.md) of het [AdlCopy-hulpprogramma](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) vervolgens gebruiken om gegevens van Azure Storage-blobs naar Data Lake Storage te kopiëren.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW is een geweldige keuze om gereinigde en voorbereide resultaten op te slaan voor toekomstige analyses.  Azure HDInsight kan worden gebruikt om deze services voor Azure SQL DW uit te voeren.

Azure SQL Data Warehouse (SQL DW) is een relationele databaseopslag die is geoptimaliseerd voor analytische workloads.  Azure SQL DW schaalt op basis van partitietabellen.  Tabellen kunnen worden verdeeld over meerdere knooppunten.  Azure SQL DW-knooppunten zijn geselecteerd op het moment van maken.  Ze kunnen schalen na het feit, maar dat is een actief proces dat mogelijk gegevensverplaatsing vereist. Zie [SQL Data Warehouse - Compute beheren voor](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)meer informatie.

### <a name="apache-hbase"></a>Apache HBase

Apache HBase is een key-value store die beschikbaar is in Azure HDInsight.  Apache HBase is een open-source NoSQL-database die is gebaseerd op Hadoop en die is gemodelleerd naar Google BigTable. HBase biedt performante willekeurige toegang en sterke consistentie voor grote hoeveelheden ongestructureerde en semigestructureerde gegevens in een schemaloze database georganiseerd door kolomfamilies.

De gegevens worden opgeslagen in de rijen van een tabel en de gegevens in een rij worden gegroepeerd op basis van de kolomfamilie. HBase is een database zonder schema in de zin dat zowel de kolommen als het type gegevens dat hierin wordt opgeslagen niet hoeven te worden gedefinieerd voordat u ze kunt gebruiken. De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. HBase kan vertrouwen op redundantie van gegevens, batchverwerking en andere functies die worden geleverd door gedistribueerde toepassingen in het Hadoop-ecosysteem.

HBase is een uitstekende bestemming voor sensor- en loggegevens voor toekomstige analyse.

De schaalbaarheid van HBase is afhankelijk van het aantal knooppunten in het HDInsight-cluster.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL-database en Azure-database

Azure biedt drie verschillende relationele databases als platform-as-a-service (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) is een implementatie van Microsoft SQL Server. Zie [Prestaties afstemmen in Azure SQL Database](../../sql-database/sql-database-performance-guidance.md)voor meer informatie over prestaties.
* [Azure Database voor MySQL](../../mysql/overview.md) is een implementatie van Oracle MySQL.
* [Azure Database voor PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) is een implementatie van PostgreSQL.

Deze producten worden opgeschaald, wat betekent dat ze worden geschaald door meer CPU en geheugen toe te voegen.  U er ook voor kiezen om premium schijven te gebruiken met de producten voor een betere I /O-prestaties.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) is een analytische gegevensengine die wordt gebruikt bij beslissingsondersteuning en bedrijfsanalyses en de analytische gegevens voor bedrijfsrapporten en clienttoepassingen zoals Power BI, Excel, Reporting Services-rapporten en andere gegevens visualisatietools.

Analysekubussen kunnen worden geschaald door lagen voor elke afzonderlijke kubus te wijzigen.  Zie [Azure Analysis Services Pricing](https://azure.microsoft.com/pricing/details/analysis-services/)voor meer informatie.

## <a name="extract-and-load"></a>Extraheren en laden

Zodra de gegevens in Azure bestaan, u veel services gebruiken om deze te extraheren en te laden in andere producten.  HDInsight ondersteunt Sqoop en Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop is een tool die is ontworpen voor het efficiënt overbrengen van gegevens tussen gestructureerde, semi-gestructureerde en ongestructureerde gegevensbronnen.

Sqoop gebruikt MapReduce om de gegevens te importeren en exporteren, om parallelle bewerking en fouttolerantie te bieden.

### <a name="apache-flume"></a>Apache Flume

Apache Flume is een gedistribueerde, betrouwbare en beschikbare service voor het efficiënt verzamelen, aggregeren en verplaatsen van grote hoeveelheden loggegevens. Flume heeft een eenvoudige en flexibele architectuur op basis van streaming datastromen. Flume is robuust en fouttolerant met tunable betrouwbaarheidsmechanismen en veel failover- en herstelmechanismen. Flume maakt gebruik van een eenvoudig uitbreidbaar gegevensmodel dat het mogelijk maakt voor online analytische toepassing.

Apache Flume kan niet worden gebruikt met Azure HDInsight.  Een on-premises Hadoop-installatie kan Flume gebruiken om gegevens te verzenden naar Azure Storage Blobs of Azure Data Lake Storage.  Zie [Apache Flume gebruiken met HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)voor meer informatie.

## <a name="transform"></a>Transformeren

Zodra gegevens op de gekozen locatie aanwezig zijn, moet u deze opschonen, combineren of voorbereiden op een specifiek gebruikspatroon.  Hive, Pig en Spark SQL zijn allemaal goede keuzes voor dat soort werk.  Ze worden allemaal ondersteund op HDInsight.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hive gebruiken als ETL-tool](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Gegevens verplaatsen van Azure SQL Database naar apache hive-tabel](./apache-hadoop-use-sqoop-mac-linux.md)
