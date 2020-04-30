---
title: ETL uitpakken, transformeren en laden op schaal-Azure HDInsight
description: Ontdek hoe extra heren, transformeren en laden worden gebruikt in HDInsight met Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: ee989ccbb2e441256bec71781c538c7761fc7b88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232237"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>(ETL) uitpakken, transformeren en laden op schaal

Extra heren, transformeren en laden (ETL) is het proces waarmee gegevens uit verschillende bronnen worden verkregen. Verzameld en verwerkt op een standaard locatie. Uiteindelijk geladen in een gegevens opslag waarvan de query kan worden uitgevoerd. Met verouderde ETL worden gegevens geïmporteerd en op de juiste plaats gereinigd en opgeslagen in een relationele gegevens engine. Met HDInsight ondersteunen een breed scala aan Apache Hadoop omgevings onderdelen ETL op schaal.

Het gebruik van HDInsight in het ETL-proces kan worden samenvatten door deze pijp lijn:

![Overzicht van HDInsight ETL op schaal](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

In de volgende secties worden alle ETL-fasen en de bijbehorende onderdelen besproken.

## <a name="orchestration"></a>Orchestration

Indeling omvat alle fasen van de ETL-pijp lijn. ETL-taken in HDInsight omvatten vaak verschillende producten die met elkaar samen werken.  U kunt Hive gebruiken om een deel van de gegevens te schonen, terwijl varken een ander gedeelte opschoont.  U kunt Azure Data Factory voor het laden van gegevens in Azure SQL Database van Azure Data Lake Store.

Indeling is nodig om de juiste taak op het juiste tijdstip uit te voeren.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie is een coördinatiesysteem voor werkstromen waarmee Hadoop-taken worden beheerd. Oozie wordt uitgevoerd binnen een HDInsight-cluster en is geïntegreerd met de Hadoop-stack. Oozie ondersteunt Hadoop-taken voor Apache Hadoop MapReduce, Apache varken, Apache Hive en Apache Sqoop. Oozie kan ook worden gebruikt voor het plannen van taken die specifiek zijn voor een systeem, zoals Java-Program ma's of shell scripts.

Zie [Apache Oozie gebruiken met Apache Hadoop om een werk stroom te definiëren en uit te voeren op HDInsight](../hdinsight-use-oozie-linux-mac.md)voor meer informatie. Zie ook [operationeel maken de gegevens pijplijn](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory biedt Orchestration-mogelijkheden in de vorm van platform-as-a-service. Het is een cloud-gebaseerde gegevens integratie service waarmee u gegevensgestuurde werk stromen kunt maken in de Cloud. Werk stromen voor het organiseren en automatiseren van gegevens verplaatsing en gegevens transformatie.

Met Azure Data Factory kunt u het volgende doen:

1. Gegevens gegevensgestuurde werk stromen (pijp lijnen genoemd) maken en plannen die gegevens uit verschillende gegevens archieven opnemen.
2. De gegevens verwerken en transformeren met behulp van Compute-services zoals Azure HDInsight Hadoop. Of Spark, Azure Data Lake Analytics, Azure Batch en Azure Machine Learning.
3. Uitvoergegevens publiceren naar gegevensopslaglocaties als Azure SQL Data Warehouse, waar BI-toepassingen (business intelligence) er gebruik van kunnen maken.

Raadpleeg de [documentatie](../../data-factory/introduction.md)voor meer informatie over Azure Data Factory.

## <a name="ingest-file-storage-and-result-storage"></a>Bestands opslag en resultaat opslag opnemen

Brongegevens bestanden worden meestal geladen op een locatie in Azure Storage of Azure Data Lake Storage. Bestanden kunnen een wille keurige indeling hebben, maar zijn meestal platte bestanden, zoals Csv's.

### <a name="azure-storage"></a>Azure Storage

Azure Storage heeft specifieke adaptieve doelen. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../../storage/blobs/scalability-targets.md). Voor de meeste analytische knoop punten wordt Azure Storage het beste geschaald wanneer er veel kleinere bestanden worden verwerkt.  Azure Storage garandeert dezelfde prestaties, ongeacht hoe groot de bestanden (zo lang u zich binnen uw limieten bevindt).  Deze garantie houdt in dat u terabytes kunt opslaan en toch consistente prestaties krijgt, of u nu een subset van de gegevens of alle gegevens gebruikt.

Azure Storage heeft verschillende typen blobs.  Een *toevoeg-BLOB* is een uitstekende optie om Weblogboeken of sensor gegevens op te slaan.  

Er kunnen meerdere blobs worden gedistribueerd over verschillende servers om de toegang tot ze te schalen. Maar één Blob kan slechts door één server worden geleverd. Hoewel blobs logisch kunnen worden gegroepeerd in BLOB-containers, zijn er geen gevolgen voor het partitioneren van deze groepering.

Azure Storage heeft ook een WebHDFS API-laag voor de Blob-opslag.  Alle services in HDInsight hebben toegang tot bestanden in Azure Blob Storage voor het opschonen van gegevens en het verwerken van gegevens. Vergelijkbaar met de manier waarop deze services Hadoop Distributed File System (HDFS) gebruiken.

Gegevens worden doorgaans opgenomen in Azure Storage met behulp van Power shell, de Azure Storage SDK of AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) is een beheerde grootschalige-opslag plaats. Een opslag plaats voor analyse gegevens die compatibel zijn met HDFS.  ADLS maakt gebruik van een ontwerp paradigma dat vergelijkbaar is met HDFS. ADLS biedt onbeperkte aanpas baarheid voor totale capaciteit en de grootte van afzonderlijke bestanden. ADLS is goed bij het werken met grote bestanden omdat een groot bestand kan worden opgeslagen op meerdere knoop punten.  Partitioneren van gegevens in ADLS geschiedt achter de schermen.  U profiteert van een enorme door Voer voor het uitvoeren van analytische taken met duizenden gelijktijdige uitvoerders die op efficiënte wijze honderden terabytes aan gegevens lezen en schrijven.

Gegevens worden doorgaans opgenomen in ADLS met behulp van Azure Data Factory. Of ADLS Sdk's, AdlCopy service, Apache DistCp of Apache Sqoop.  Welke van deze services het meest gebruikt, is afhankelijk van waar de gegevens zich bevindt.  Als de gegevens zich momenteel in een bestaand Hadoop-cluster bevindt, kunt u Apache DistCp, AdlCopy service of Azure Data Factory gebruiken.  Voor gegevens in Azure Blob Storage kunt u Azure Data Lake Storage .NET SDK, Azure PowerShell of Azure Data Factory gebruiken.

ADLS is ook geoptimaliseerd voor gebeurtenis opname met behulp van Azure Event hub of Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Overwegingen voor beide opslag opties

Voor het uploaden van gegevens sets in het terabyte-bereik kan de netwerk latentie een groot probleem zijn, met name als de gegevens afkomstig zijn van een on-premises locatie.  In dergelijke gevallen kunt u de onderstaande opties gebruiken:

* Azure ExpressRoute: met Azure ExpressRoute kunt u particuliere verbindingen maken tussen Azure-data centers en uw on-premises infra structuur. Deze verbindingen bieden een betrouw bare optie voor het overbrengen van grote hoeveel heden gegevens. Zie de [documentatie van Azure ExpressRoute](../../expressroute/expressroute-introduction.md)voor meer informatie.

* ' Offline ' uploaden van gegevens. U kunt de [Azure import/export-service](../../storage/common/storage-import-export-service.md) gebruiken om harde schijven met uw gegevens naar een Azure-Data Center te verzenden. Uw gegevens worden eerst geüpload naar Azure Storage blobs. U kunt vervolgens Azure Data Factory of het hulp programma AdlCopy gebruiken om gegevens van Azure Storage blobs naar Data Lake Storage te kopiëren.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW is een uitstekende keuze voor het opslaan van voor bereide resultaten.  Azure HDInsight kan worden gebruikt om deze services uit te voeren voor Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) is een relationele database opslag dat is geoptimaliseerd voor analytische werk belastingen.  Azure SQL DW kan worden geschaald op basis van gepartitioneerde tabellen.  Tabellen kunnen worden gepartitioneerd op meerdere knoop punten.  Knoop punten van Azure SQL DW worden geselecteerd op het moment van maken.  Ze kunnen worden geschaald na het feit, maar dat is een actief proces waarvoor gegevens verplaatsing nodig is. Zie [SQL Data Warehouse-Compute beheren](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)voor meer informatie.

### <a name="apache-hbase"></a>Apache HBase

Apache HBase is een sleutel waarde-archief dat beschikbaar is in azure HDInsight.  Apache HBase is een open-source NoSQL-database die is gebaseerd op Hadoop en die is gemodelleerd naar Google BigTable. HBase biedt een uitvoerende, wille keurige toegang en sterke consistentie voor grote hoeveel heden ongestructureerde en semigestructureerde gegevens. Gegevens in een schemaloze data base, geordend op kolom families.

De gegevens worden opgeslagen in de rijen van een tabel en de gegevens in een rij worden gegroepeerd op basis van de kolomfamilie. HBase is een schemaloze data base. De kolommen en gegevens typen die in hen zijn opgeslagen, hoeven niet te worden gedefinieerd voordat ze kunnen worden gebruikt. De open-source code wordt lineair geschaald om petabytes aan gegevens op duizenden knooppunten te verwerken. HBase kan afhankelijk zijn van gegevens redundantie, batch verwerking en andere functies die worden geleverd door gedistribueerde toepassingen in de Hadoop-omgeving.

HBase is een uitstekende bestemming voor sensor-en logboek gegevens voor toekomstige analyse.

HBase Adaptive is afhankelijk van het aantal knoop punten in het HDInsight-cluster.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database en Azure-data base

Azure biedt drie verschillende relationele data bases als platform-as-a-Service (PAAS).

* [Azure SQL database](../../sql-database/sql-database-technical-overview.md) is een implementatie van Microsoft SQL Server. Zie [prestaties afstemmen in Azure SQL database](../../sql-database/sql-database-performance-guidance.md)voor meer informatie over prestaties.
* [Azure database for MySQL](../../mysql/overview.md) is een implementatie van Oracle MySQL.
* [Azure database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) is een implementatie van postgresql.

Deze producten worden omhoog geschaald, wat betekent dat ze worden geschaald door meer CPU en geheugen toe te voegen.  U kunt er ook voor kiezen om Premium-schijven met de producten te gebruiken voor betere I/O-prestaties.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) is een engine voor analytische gegevens die wordt gebruikt in de besluit ondersteuning en Business Analytics. AAS levert de analytische gegevens voor zakelijke rapporten en client toepassingen zoals Power BI. Ook Excel, Reporting Services-rapporten en andere hulpprogram ma's voor gegevens visualisatie.

Analyse kubussen kunnen worden geschaald door de lagen voor elke afzonderlijke kubus te wijzigen.  Zie [Azure Analysis Services prijzen](https://azure.microsoft.com/pricing/details/analysis-services/)voor meer informatie.

## <a name="extract-and-load"></a>Uitpakken en laden

Zodra de gegevens aanwezig zijn in azure, kunt u veel services gebruiken om deze te extra heren en te laden in andere producten.  HDInsight ondersteunt Sqoop en Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop is een hulp programma dat is ontworpen voor het efficiënt overbrengen van gegevens tussen gestructureerde, semi-gestructureerde en ongestructureerde gegevens bronnen.

Sqoop maakt gebruik van MapReduce voor het importeren en exporteren van de gegevens, om parallelle bewerkingen en fout tolerantie mogelijk te maken.

### <a name="apache-flume"></a>Apache Flume

`Apache Flume`is een gedistribueerde, betrouw bare service waarmee u op efficiënte wijze grote hoeveel heden logboek gegevens kunt verzamelen, samen voegen en verplaatsen. Flume heeft een flexibele architectuur op basis van streaming-gegevens stromen. Flume is robuust en fout tolerant met instel bare-betrouwbaarheids mechanismen en veel failover-en herstel mechanismen. Flume maakt gebruik van een eenvoudig uitbreidbaar gegevens model waarmee u online analytische toepassing kunt gebruiken.

Apache Flume kan niet worden gebruikt met Azure HDInsight.  Een on-premises Hadoop-installatie kan Flume gebruiken om gegevens te verzenden naar Azure Storage-blobs of Azure Data Lake Storage.  Zie [using Apache Flume with HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/)(Engelstalig) voor meer informatie.

## <a name="transform"></a>Transformeren

Zodra de gegevens op de gekozen locatie aanwezig zijn, moet u deze opschonen, combi neren of voorbereiden voor een specifiek gebruiks patroon.  Hive-, Pig-en Spark SQL zijn allemaal een goede keuze voor dat soort werk.  Ze worden allemaal ondersteund op HDInsight.

## <a name="next-steps"></a>Volgende stappen

* [Apache Hive als ETL-hulp programma gebruiken](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Gegevens verplaatsen van Azure SQL Database naar Apache Hive tabel](./apache-hadoop-use-sqoop-mac-linux.md)
