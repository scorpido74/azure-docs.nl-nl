---
title: On-premises Apache Hadoop migreren naar Azure HDInsight-motivatie en voor delen
description: Meer informatie over de motivatie en voor delen voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 3c122a68e9f28772be4e3a17a21e5457aa187ce6
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736102"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>On-premises Apache Hadoop clusters migreren naar Azure HDInsight-motivatie en voor delen

Dit artikel is de eerste in een reeks over de best practices voor het migreren van on-premises Apache Hadoop implementaties van het eco-systeem naar Azure HDInsight. Deze reeks artikelen is bedoeld voor mensen die verantwoordelijk zijn voor het ontwerp, de implementatie en de migratie van Apache Hadoop oplossingen in azure HDInsight. De rollen die kunnen profiteren van deze artikelen zijn onder andere Cloud Architects, Hadoop-beheerders en DevOps-technici. Software ontwikkelaars, gegevens technici en gegevens wetenschappers moeten ook profiteren van de uitleg over hoe verschillende typen clusters in de Cloud werken.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Waarom migreren naar Azure HDInsight

Azure HDInsight is een Cloud distributie van Hadoop-onderdelen. Azure HDInsight maakt het eenvoudig, snel en kosteneffectief om enorme hoeveelheden gegevens te verwerken. HDInsight bevat de populairste open source-frameworks, zoals:

- Apache Hadoop
- Apache Spark
- Apache Hive met LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Voor delen van Azure HDInsight over on-premises Hadoop

- **Lage kosten** -kosten kunnen worden verminderd door [clusters op aanvraag te maken](../hdinsight-hadoop-create-linux-clusters-adf.md) en alleen te betalen voor wat u gebruikt. Ontkoppelde reken kracht en opslag bieden flexibiliteit door het gegevens volume onafhankelijk van de cluster grootte te bewaren.

- **Automatisch maken van clusters** : voor het automatisch maken van een cluster zijn minimale installatie en configuratie vereist. Automation kan worden gebruikt voor clusters op aanvraag.

- **Beheerde hardware en configuratie** : u hoeft zich geen zorgen te maken over de fysieke hardware of infra structuur met een HDInsight-cluster. U hoeft alleen de configuratie van het cluster op te geven. Azure stelt deze in.

- **Eenvoudig schaal bare** HDInsight biedt u de mogelijkheid om werk belastingen omhoog of omlaag te [schalen](../hdinsight-administer-use-portal-linux.md) . Azure zorgt ervoor dat de herverdeling van gegevens en de herverdeling van de werk belasting zonder onderbreking van de gegevensverwerkings taken worden uitgevoerd.

- **Wereld wijde Beschik baarheid** -HDInsight is beschikbaar in meer [regio's](https://azure.microsoft.com/regions/services/) dan enige andere Big data analyse-aanbieding. Azure HDInsight is ook beschikbaar in Azure Government, China en Duitsland, waarmee u kunt voldoen aan de behoeften van uw bedrijf in belangrijke soevereine gebieden.

- Met **beveiligen en compatibel** -HDInsight kunt u uw zakelijke gegevensassets beveiligen met [Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md), [versleuteling](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)en integratie met [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight voldoet ook aan de meest populaire normen voor de industrie-en regerings [naleving](https://azure.microsoft.com/overview/trusted-cloud).

- **Vereenvoudigd versie beheer** : Azure HDInsight beheert de versie van de Hadoop-eco-systeem onderdelen en houdt deze up-to-date. Software-updates zijn doorgaans een complex proces voor on-premises implementaties.

- **Kleinere clusters die zijn geoptimaliseerd voor specifieke werk belastingen met minder afhankelijkheden tussen onderdelen** : een typische on-premises Hadoop-installatie maakt gebruik van één cluster dat veel doelen speelt. Met Azure HDInsight kunnen specifieke werkbelasting clusters worden gemaakt. Het maken van clusters voor specifieke werk belastingen verwijdert de complexiteit van het onderhoud van één cluster met toenemende complexiteit.

- **Productiviteit** : u kunt verschillende hulpprogram Ma's voor Hadoop en Spark gebruiken in uw favoriete ontwikkel omgeving.

- **Uitbreid baarheid met aangepaste hulpprogram ma's of toepassingen** van derden-HDInsight-clusters kunnen worden uitgebreid met geïnstalleerde onderdelen en kunnen ook worden geïntegreerd met de andere oplossingen van Big data met [één klik op](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) implementaties in de Azure-markt plaats.

- **Eenvoudig beheer, beheer en controle** : Azure HDInsight kan worden geïntegreerd met [Azure monitor logboeken](../hdinsight-hadoop-oms-log-analytics-tutorial.md) om een enkele interface te bieden waarmee u al uw clusters kunt bewaken.

- **Integratie met andere Azure-Services** -HDInsight kan eenvoudig worden geïntegreerd met andere populaire Azure-Services, zoals de volgende:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- Zelf herstellende **processen en onderdelen** : met HDInsight worden de infra structuur en open source-onderdelen voortdurend gecontroleerd met behulp van een eigen bewakings infrastructuur. Daarnaast worden kritieke fouten, zoals niet-beschik baarheid van open-source onderdelen en knoop punten, automatisch hersteld. Waarschuwingen worden in Ambari geactiveerd als een OSS-onderdeel is mislukt.

Zie het artikel [Wat is Azure HDInsight en de technologie stack van Apache Hadoop](../hadoop/apache-hadoop-introduction.md)voor meer informatie.

## <a name="migration-planning-process"></a>Migratie plannings proces

De volgende stappen worden aanbevolen voor het plannen van een migratie van on-premises Hadoop-clusters naar Azure HDInsight:

1. Inzicht in de huidige on-premises implementatie en topologieën.
2. Inzicht in de huidige project omvang, tijd lijnen en team expertise.
3. Meer informatie over de vereisten van Azure.
4. Bouw een gedetailleerd plan op basis van best practices.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Details verzamelen om een migratie voor te bereiden

Deze sectie bevat sjabloon vragenlijsten waarmee u belang rijke informatie kunt verzamelen over:

- De on-premises implementatie
- Projectgegevens
- Azure-vereisten

### <a name="on-premises-deployment-questionnaire"></a>Vragen lijst on-premises implementatie

| **Produkt** | **Voorbeeld** | **Antwoord** |
|---|---|---|
|**Onderwerp**: **Variabelen**|||
|Distributie versie van cluster|HDP 2.6.5, CDH 5.7|
|Systeem onderdelen van Big Data|HDFS, garens, Hive, LLAP, Impala, kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, solr, Sqoop, Oozie, zwerver, Atlas, Falcon, Zeppelin, R|
|Cluster typen|Hadoop, Spark, Confluent Kafka, Storm, solr|
|Aantal clusters|4|
|Aantal hoofd knooppunten|2|
|Aantal worker-knoop punten|100|
|Aantal Edge-knoop punten| 5|
|Totale schijf ruimte|100 TB|
|Configuratie van hoofd knooppunt|m/y, CPU, schijf, etc.|
|Configuratie van gegevens knooppunten|m/y, CPU, schijf, etc.|
|Configuratie van Edge-knoop punten|m/y, CPU, schijf, etc.|
|HDFS-versleuteling?|Ja|
|Hoge beschikbaarheid|HDFS HA, meta Store HA|
|Nood herstel/back-up|Back-upcluster?|  
|Systemen die afhankelijk zijn van het cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integraties van derden|Tableau, GridGain, Qubole, informatica, Splunk|
|**Onderwerp**: **Beveiligingsprincipal**|||
|Perimeter beveiliging|Firewalls|
|Autorisatie voor cluster verificatie &|Active Directory, Ambari, Cloudera Manager, geen verificatie|
|HDFS Access Control|  Hand matig, SSH-gebruikers|
|Autorisatie van Hive-verificatie &|Sentry, LDAP, AD met Kerberos, zwerver|
|Controleren|Ambari, Cloudera Navigator, Ranger|
|Bewaking|Grafiet, verzamelde, statistieken, telegrafie, InfluxDB|
|Waarschuwingen|Kapacitor, Prometheus, Datadog|
|Duur van gegevens retentie| 3 jaar, 5 jaar|
|Cluster beheerders|Eén beheerder, meerdere beheerders|

### <a name="project-details-questionnaire"></a>Project details vragen lijst

|**Produkt**|**Voorbeeld**|**Antwoord**|
|---|---|---|
|**Onderwerp**: **Werk belastingen en frequentie**|||
|MapReduce-taken|10 taken-twee keer per dag||
|Hive-taken|100-taken--elk uur||
|Spark-batch taken|50 taken: elke 15 minuten||
|Spark-streaming-taken|5 taken--elke 3 minuten||
|Structured streaming-taken|5 taken--elke minuut||
|Trainings taken voor ML model|2 taken: één keer per week||
|Programmeertalen|Python, Scala, Java||
|Uitvoeren van scripts|Shell, Python||
|**Onderwerp**: **Gegevens**|||
|Gegevensbronnen|Platte bestanden, JSON, Kafka, RDBMS||
|Data orchestratie|Oozie werk stromen, lucht stroom||
|Zoek opdrachten in het geheugen|Apache Ignite, redis||
|Gegevens bestemmingen|HDFS, RDBMS, Kafka, MPP ||
|**Onderwerp**: **Meta gegevens**|||
|DATABASE type van Hive|MySQL, post gres||
|Nee. van Hive-meta Stores|2||
|Nee. van Hive-tabellen|100||
|Nee. van Zwerver-beleids regels|20||
|Nee. van Oozie-werk stromen|100||
|**Onderwerp**: **Schalen**|||
|Gegevens volume inclusief replicatie|100 TB||
|Dagelijks opname volume|50 GB||
|Groei tempo van gegevens|10% per jaar||
|Groei tempo van cluster knooppunten|5% per jaar
|**Onderwerp**: **Cluster gebruik**|||
|Gemiddeld CPU-percentage gebruikt|60%||
|Gemiddeld geheugen gebruikt%|75%||
|Gebruikte schijfruimte|75%||
|Gemiddeld netwerk gebruikt%|25%
|**Onderwerp**: **Mede**|||
|Nee. van beheerders|2||
|Nee. van ontwikkel aars|10||
|Nee. van eind gebruikers|100||
|Vaardigheden|Hadoop, Spark||
|Nee. beschik bare bronnen voor migratie taken|2||
|**Onderwerp**: **Beperkingen**|||
|Huidige beperkingen|Latentie is hoog||
|Huidige uitdagingen|Probleem met gelijktijdigheid||

### <a name="azure-requirements-questionnaire"></a>Vragen lijst Azure-vereisten

|**Onderwerp**: **Analyseren** |||
|---|---|---|
|**Produkt**|**Voorbeeld**|**Antwoord**|
| Voorkeursregio|US - oost||
|Voor keur voor VNet?|Ja||
|HA/DR nodig?|Ja||
|Integratie met andere Cloud Services?|ADF, CosmosDB||
|**Onderwerp**:   **Gegevensverplaatsing**  |||
|Initiële laad voorkeur|DistCp, Data box, ADF, WANDisco||
|Delta voor gegevens overdracht|DistCp, AzCopy||
|Voortdurende incrementele gegevens overdracht|DistCp, Sqoop||
|**Onderwerp**:   **Bewaking & waarschuwingen** |||
|Azure monitoring &-waarschuwingen gebruiken versus bewaking van derden integreren|Azure monitoring &-waarschuwingen gebruiken||
|**Onderwerp**:   **Beveiligings voorkeuren** |||
|Persoonlijke en beveiligde gegevens pijplijn?|Ja||
|Aan het domein toegevoegd cluster (ESP)?|     Ja||
|On-premises AD Sync naar de Cloud?|     Ja||
|Nee. van AD-gebruikers om te synchroniseren?|          100||
|Wilt u wacht woorden synchroniseren met de Cloud?|    Ja||
|Alleen gebruikers in de Cloud?|                 Ja||
|MFA vereist?|                       Nee|| 
|Vereisten voor gegevens autorisatie?|  Ja||
|Access Control op basis van rollen?|        Ja||
|Controle vereist?|                  Ja||
|Gegevens versleuteling in rust?|          Ja||
|Gegevens versleuteling in transit?|       Ja||
|**Onderwerp**:   **Voor keuren voor nieuwe architectuur** |||
|Eén cluster versus specifieke cluster typen|Specifieke cluster typen||
|Opgeslagen opslag versus externe opslag?|Externe opslag||
|Kleinere cluster grootte naarmate gegevens extern worden opgeslagen?|Kleinere cluster grootte||
|Gebruikt u meerdere kleinere clusters in plaats van één groot cluster?|Meerdere kleinere clusters gebruiken||
|Een externe meta Store gebruiken?|Ja||
|Wilt u de meta Stores delen tussen verschillende clusters?|Ja||
|Werk belastingen ontconstrueren?|Hive-taken vervangen door Spark-taken||
|ADF gebruiken voor gegevens indeling?|Nee||

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Aanbevolen procedures voor architectuur voor on-premises migratie van Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
