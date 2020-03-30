---
title: 'Gegevensmigratie: On-premises Apache Hadoop naar Azure HDInsight'
description: Lees aanbevolen procedures voor gegevensmigratie voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 41112359408497d84243ed9bb06f396acf008dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74665998"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>On-premises Apache Hadoop-clusters migreren naar Azure HDInsight - best practices voor gegevensmigratie

In dit artikel worden aanbevelingen gegeven voor gegevensmigratie naar Azure HDInsight. Het maakt deel uit van een serie die best practices biedt om te helpen bij het migreren van on-premises Apache Hadoop-systemen naar Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>On-premises gegevens migreren naar Azure

Er zijn twee belangrijke opties om gegevens van on-premises naar azure-omgeving te migreren:

* Gegevens overzetten via netwerk met TLS
    * Via internet - U gegevens overbrengen naar Azure-opslag via een gewone internetverbinding met behulp van een van de verschillende hulpprogramma's, zoals: Azure Storage Explorer, AzCopy, Azure Powershell en Azure CLI. Zie [Gegevens verplaatsen van en naar Azure Storage](../../storage/common/storage-moving-data.md)voor meer informatie.

    * Express Route - ExpressRoute is een Azure-service waarmee u privéverbindingen maken tussen Microsoft-datacenters en infrastructuur die zich op uw locatie of in een colocatiefaciliteit bevinden. ExpressRoute-verbindingen gaan niet via het openbare internet en bieden hogere beveiliging, betrouwbaarheid en snelheden met lagere latencies dan typische verbindingen via internet. Zie [Een ExpressRoute-circuit maken en wijzigen](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)voor meer informatie.

    * Online gegevensoverdracht van gegevensbox - Data Box Edge en Data Box Gateway zijn online gegevensoverdrachtsproducten die fungeren als netwerkopslaggateways om gegevens tussen uw site en Azure te beheren. Data Box Edge, een on-premises netwerkapparaat, brengt gegevens van en naar Azure over en maakt gebruik van kunstmatige intelligentie (AI)-enabled edge compute om gegevens te verwerken. Data Box Gateway is een virtueel toestel met mogelijkheden voor opslaggateways. Zie [Azure Data Box Documentation - Online Transfer](https://docs.microsoft.com/azure/databox-online/)voor meer informatie.

* Gegevens offline verzenden

    Data Box offline gegevensoverdracht - Data Box, Data Box Disk en Data Box Heavy-apparaten helpen u grote hoeveelheden gegevens over te zetten naar Azure wanneer het netwerk geen optie is. Deze apparaten voor offline gegevensoverdracht worden verzonden tussen uw organisatie en het Azure-datacenter. Ze gebruiken AES-versleuteling om uw gegevens tijdens het transport te beschermen en ondergaan een grondig ontsmettingsproces na het uploaden om uw gegevens van het apparaat te verwijderen. Zie Azure Data Box Documentation - [Offline Transfer](https://docs.microsoft.com/azure/databox/)voor meer informatie over de offline transferapparaten van de databox. Zie Azure Data Box gebruiken om [te migreren van een on-premises HDFS-winkel naar Azure Storage voor](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)meer informatie over migratie van Hadoop-clusters.

De volgende tabel heeft een geschatte duur van de gegevensoverdracht op basis van het gegevensvolume en de netwerkbandbreedte. Gebruik een vak Gegevens als de gegevensmigratie naar verwachting meer dan drie weken in de duren zal duren.

|Gegevens Qty|Netwerkbandbreedte||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**|
|1 TB|2 dagen|1 dag| 2 uur|14 minuten|
|10 TB|22 dagen|10 dagen|1 dag|2 uur|
|35 TB|76 dagen|34 dagen|3 dagen|8 uur|
|80 TB|173 dagen|78 dagen|8 dagen|19 uur|
|100 TB|216 dagen|97 dagen|10 dagen|1 dag|
|200 TB|1 jaar|194 dagen|19 dagen|2 dagen|
|500 TB|3 jaar|1 jaar|49 dagen|5 dagen|
|1 PB|6 jaar|3 jaar|97 dagen|10 dagen|
|2 PB|12 jaar|Vijf jaar
|194 dagen|19 dagen|

Hulpprogramma's die zijn voor Azure, zoals Apache Hadoop DistCp, Azure Data Factory en AzureCp, kunnen worden gebruikt om gegevens over het netwerk over te dragen. De tool VAN derden WANDisco kan ook voor hetzelfde doel worden gebruikt. Apache Kafka Mirrormaker en Apache Sqoop kunnen worden gebruikt voor voortdurende gegevensoverdracht van on-premises naar Azure-opslagsystemen.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Prestatieoverwegingen bij het gebruik van Apache Hadoop DistCp

DistCp is een Apache-project dat een mapreduce map-taak gebruikt om gegevens over te dragen, fouten te verwerken en te herstellen van die fouten. Er wordt een lijst met bronbestanden toegewezen aan elke kaarttaak. De taak Kaart kopieert vervolgens alle toegewezen bestanden naar de bestemming. Er zijn verschillende technieken kunnen de prestaties van DistCp verbeteren.

### <a name="increase-the-number-of-mappers"></a>Het aantal Mappers verhogen

DistCp probeert kaarttaken te maken, zodat elk van deze taken ongeveer hetzelfde aantal bytes kopieert. Standaard gebruiken DistCp-taken 20 mappers. Het gebruik van meer Mappers voor Distcp (met de parameter 'm' op de opdrachtregel) verhoogt het parallellisme tijdens het gegevensoverdrachtproces en vermindert de lengte van de gegevensoverdracht. Er zijn echter twee dingen om te overwegen, terwijl het verhogen van het aantal Mappers:

* De laagste granulariteit van DistCp is één bestand. Het opgeven van een aantal Mappers meer dan het aantal bronbestanden helpt niet en zal de beschikbare clusterbronnen verspillen.

* Overweeg het beschikbare garengeheugen op het cluster om het aantal Mappers te bepalen. Elke kaarttaak wordt gestart als garencontainer. Ervan uitgaande dat er geen andere zware workloads worden uitgevoerd op het cluster, kan het aantal \* Mappers worden bepaald door de volgende formule: m = (aantal werknemersknooppunten GARENgeheugen voor elke werknemerknooppunt) / YARN-containergrootte. Als andere toepassingen echter geheugen gebruiken, kiest u ervoor om slechts een gedeelte van het YARN-geheugen te gebruiken voor DistCp-taken.

### <a name="use-more-than-one-distcp-job"></a>Meer dan één DistCp-taak gebruiken

Wanneer de grootte van de te verplaatsen gegevensset groter is dan 1 TB, gebruikt u meer dan één DistCp-taak. Het gebruik van meer dan één taak beperkt de impact van fouten. Als een taak mislukt, hoeft u alleen die specifieke taak opnieuw op te starten in plaats van alle taken.

### <a name="consider-splitting-files"></a>Overweeg bestanden te splitsen

Als er een klein aantal grote bestanden, dan overwegen splitsen ze in 256-MB bestandsbrokken om meer potentiële gelijktijdigheid te krijgen met meer Mappers.

### <a name="use-the-strategy-command-line-parameter"></a>De opdrachtregelparameter 'strategie' gebruiken

Overweeg `strategy = dynamic` de parameter in de opdrachtregel te gebruiken. De standaardwaarde `strategy` van `uniform size`de parameter is , in welk geval elke kaart ongeveer hetzelfde aantal bytes kopieert. Wanneer deze parameter `dynamic`wordt gewijzigd in , wordt het vermeldingsbestand opgesplitst in verschillende "chunk-files". Het aantal chunk-bestanden is een veelvoud van het aantal kaarten. Aan elke kaarttaak wordt een van de chunk-bestanden toegewezen. Nadat alle paden in een stuk zijn verwerkt, wordt de huidige brok verwijderd en wordt een nieuwe brok verkregen. Het proces gaat door totdat er geen brokken meer beschikbaar zijn. Deze "dynamische" benadering maakt het mogelijk snellere kaarttaken om meer paden te verbruiken dan langzamere, waardoor de DistCp-taak in het algemeen wordt versneld.

### <a name="increase-the-number-of-threads"></a>Het aantal threads verhogen

Kijk of `-numListstatusThreads` het verhogen van de parameter de prestaties verbetert. Deze parameter bepaalt het aantal threads dat moet worden gebruikt voor het maken van bestandsvermelding en 40 is de maximale waarde.

### <a name="use-the-output-committer-algorithm"></a>Het algoritme voor uitvoercommitter gebruiken

Kijk of het `-Dmapreduce.fileoutputcommitter.algorithm.version=2` passeren van de parameter de DistCp-prestaties verbetert. Deze uitvoer committer algoritme heeft optimalisaties rond het schrijven van output bestanden naar de bestemming. De volgende opdracht is een voorbeeld dat het gebruik van verschillende parameters weergeeft:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migratie van metagegevens

### <a name="apache-hive"></a>Apache Bijenkorf

De hive metastore kan worden gemigreerd met behulp van de scripts of met behulp van de DB-replicatie.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive metastoremigratie met behulp van scripts

1. Genereer de Hive DDLs van on-premises Hive metastore. Deze stap kan worden gedaan met behulp van een [wrapper bash script](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Bewerk de gegenereerde DDL om de HDFS-url te vervangen door WASB/ADLS/ABFS URL's.
1. Voer de bijgewerkte DDL uit op de metastore vanuit het HDInsight-cluster.
1. Zorg ervoor dat de Hive-versie compatibel is tussen on-premises en cloud.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive metastoremigratie met DB-replicatie

- Databasereplicatie instellen tussen on-premises Hive metastore DB en HDInsight metastore DB.
- Gebruik de "Hive MetaTool" om hdfs-url te vervangen door WASB/ADLS/ABFS urls, bijvoorbeeld:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- On-premises Ranger-beleid exporteren naar xml-bestanden.
- Transformeer op specifieke HDFS-gebaseerde paden naar WASB/ADLS met behulp van een tool als XSLT.
- Importeer het beleid op Ranger dat op HDInsight wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Aanbevolen procedures voor beveiliging en DevOps voor on-premises naar Azure HDInsight Hadoop-migratie](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
