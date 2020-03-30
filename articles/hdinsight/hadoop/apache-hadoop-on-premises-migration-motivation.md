---
title: 'Voordelen: Migreren on-premises Apache Hadoop naar Azure HDInsight'
description: Leer de motivatie en voordelen voor het migreren van on-premises Hadoop-clusters naar Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 2440b93629416ea73fcf211cbe7bf5a3b72ab2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267331"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>On-premises Apache Hadoop-clusters migreren naar Azure HDInsight - motivatie en voordelen

Dit artikel is het eerste in een serie over best practices voor het migreren van on-premises Apache Hadoop eco-systeemimplementaties naar Azure HDInsight. Deze serie artikelen is bedoeld voor mensen die verantwoordelijk zijn voor het ontwerp, de implementatie en de migratie van Apache Hadoop-oplossingen in Azure HDInsight. De rollen die kunnen profiteren van deze artikelen zijn cloudarchitecten, Hadoop-beheerders en DevOps-technici. Softwareontwikkelaars, dataengineers en data scientists moeten ook profiteren van de uitleg hoe verschillende soorten clusters werken in de cloud.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Waarom migreren naar Azure HDInsight

Azure HDInsight is een clouddistributie van Hadoop-componenten. Azure HDInsight maakt het eenvoudig, snel en kosteneffectief om enorme hoeveelheden gegevens te verwerken. HDInsight bevat de meest populaire open-source frameworks zoals:

- Apache Hadoop
- Apache Spark
- Apache Hive met LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight profiteert van on-premises Hadoop

- **Lage kosten** - Kosten kunnen worden verlaagd door [clusters op aanvraag te maken](../hdinsight-hadoop-create-linux-clusters-adf.md) en alleen te betalen voor wat u gebruikt. Ontkoppelde rekenkracht en opslag biedt flexibiliteit door het gegevensvolume onafhankelijk te houden van de clustergrootte.

- **Geautomatiseerde clustercreatie** - Voor het maken van geautomatiseerde clusterinstellingen is minimale installatie en configuratie vereist. Automatisering kan worden gebruikt voor on-demand clusters.

- **Beheerde hardware en configuratie** - U hoeft zich geen zorgen te maken over de fysieke hardware of infrastructuur met een HDInsight-cluster. Geef gewoon de configuratie van het cluster op en Azure stelt het in.

- **Eenvoudig schaalbaar** - MET HDInsight u workloads omhoog of omlaag [schalen.](../hdinsight-administer-use-portal-linux.md) Azure zorgt voor gegevensherverdeling en het opnieuw in evenwicht brengen van workloads zonder gegevensverwerkingstaken te onderbreken.

- **Wereldwijde beschikbaarheid** - HDInsight is beschikbaar in meer [regio's](https://azure.microsoft.com/regions/services/) dan enig ander big data analytics-aanbod. Azure HDInsight is ook beschikbaar in Azure Government, China en Duitsland, waarmee u kunt voldoen aan de behoeften van uw bedrijf in belangrijke soevereine gebieden.

- **Veilig en compatibel** - MET HDInsight u uw bedrijfsgegevensassets beveiligen met [Azure Virtual Network,](../hdinsight-plan-virtual-network-deployment.md) [versleuteling](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)en integratie met Azure [Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight voldoet ook aan de meest populaire normen voor [naleving](https://azure.microsoft.com/overview/trusted-cloud)van de industrie en de overheid.

- **Vereenvoudigd versiebeheer** - Azure HDInsight beheert de versie van Hadoop-ecosysteemcomponenten en houdt deze up-to-date. Software-updates zijn meestal een complex proces voor on-premises implementaties.

- **Kleinere clusters die zijn geoptimaliseerd voor specifieke workloads met minder afhankelijkheden tussen componenten** - Een typische on-premises Hadoop-installatie maakt gebruik van één cluster dat vele doeleinden dient. Met Azure HDInsight kunnen workload-specifieke clusters worden gemaakt. Als u clusters maakt voor specifieke workloads, wordt de complexiteit van het onderhouden van één cluster met toenemende complexiteit weggevonden.

- **Productiviteit** - U verschillende tools gebruiken voor Hadoop en Spark in uw gewenste ontwikkelomgeving.

- **Uitbreidbaarheid met aangepaste hulpprogramma's of toepassingen van derden** - HDInsight-clusters kunnen worden uitgebreid met geïnstalleerde componenten en kunnen ook worden geïntegreerd met de andere big [data-oplossingen](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) met behulp van implementaties met één klik van de Azure Market-plaats.

- **Eenvoudig beheer, beheer en bewaking** - Azure HDInsight integreert met [Azure Monitor-logboeken](../hdinsight-hadoop-oms-log-analytics-tutorial.md) om één interface te bieden waarmee u al uw clusters bewaken.

- **Integratie met andere Azure-services** - HDInsight kan eenvoudig worden geïntegreerd met andere populaire Azure-services, zoals:

    - Azure-gegevensfabriek (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Zelfherstellende processen en componenten** - HDInsight controleert voortdurend de infrastructuur en open-source componenten met behulp van een eigen monitoringinfrastructuur. Het herstelt ook automatisch kritieke fouten, zoals het niet beschikbaar zijn van open-source componenten en knooppunten. Waarschuwingen worden geactiveerd in Ambari als een OSS-component is mislukt.

Zie voor meer informatie het artikel [Wat is Azure HDInsight en de Apache Hadoop-technologiestack.](../hadoop/apache-hadoop-introduction.md)

## <a name="migration-planning-process"></a>Migratieplanningsproces

De volgende stappen worden aanbevolen voor het plannen van een migratie van on-premises Hadoop-clusters naar Azure HDInsight:

1. Inzicht in de huidige on-premises implementatie en topologieën.
2. Inzicht in de huidige projectscope, tijdlijnen en teamexpertise.
3. Begrijp de Azure-vereisten.
4. Bouw een gedetailleerd plan op basis van best practices.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Het verzamelen van details om zich voor te bereiden op een migratie

In deze sectie vindt u sjabloonvragenlijsten om belangrijke informatie over:

- De on-premises implementatie
- Projectdetails
- Azure-vereisten

### <a name="on-premises-deployment-questionnaire"></a>On-premises implementatievragenlijst

| **Vraag** | **Voorbeeld** | **Antwoord** |
|---|---|---|
|**Onderwerp**: **Milieu**|||
|Clusterdistributieversie|HDP 2.6.5, CDH 5.7|
|Big Data eco-systeem componenten|HDFS, Garen, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Clustertypen|Hadoop, Spark, Confluent Kafka, Storm, Solr|
|Aantal clusters|4|
|Aantal hoofdknooppunten|2|
|Aantal werknemersknooppunten|100|
|Aantal randknooppunten| 5|
|Totale schijfruimte|100 TB|
|Configuratie van hoofdknooppunt|m/y, cpu, schijf, etc.|
|Configuratie van gegevensknooppunten|m/y, cpu, schijf, etc.|
|Configuratie randknooppunten|m/y, cpu, schijf, etc.|
|HDFS Encryptie?|Ja|
|Hoge beschikbaarheid|HDFS HA, Metastore HA|
|Disaster Recovery / Back-up|Back-up cluster?|  
|Systemen die afhankelijk zijn van cluster|SQL Server, Teradata, Power BI, MongoDB|
|Integraties van derden|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Onderwerp**: **Beveiliging**|||
|Perimeterbeveiliging|Firewalls|
|Beheer van clusterverificatie & autorisatie|Active Directory, Ambari, Cloudera Manager, Geen verificatie|
|HDFS-toegangsbeheer|  Handmatige, ssh-gebruikers|
|Hive-verificatie & autorisatie|Sentry, LDAP, AD met Kerberos, Ranger|
|Controleren|Ambari, Cloudera Navigator, Ranger|
|Bewaking|Grafiet, verzameld, statsd, Telegraf, InfluxDB|
|Waarschuwingen|Kapacitor, Prometheus, Datadog|
|Duur van gegevensbewaring| 3 jaar, 5 jaar|
|Clusterbeheerders|Eén beheerder, meerdere beheerders|

### <a name="project-details-questionnaire"></a>Vragenlijst projectdetails

|**Vraag**|**Voorbeeld**|**Antwoord**|
|---|---|---|
|**Onderwerp**: **Workloads en Frequentie**|||
|MapTaken verminderen|10 banen -- twee keer per dag||
|Hive vacatures|100 banen -- elk uur||
|Batchtaken spark|50 banen -- elke 15 minuten||
|Spark-streamingtaken|5 banen -- elke 3 minuten||
|Gestructureerde streamingtaken|5 banen -- elke minuut||
|ML Model trainingsvacatures|2 banen -- één keer per week||
|Programmeertalen|Python, Scala, Java||
|Scripts uitvoeren|Shell, Python||
|**Onderwerp**: **Gegevens**|||
|Gegevensbronnen|Platte bestanden, Json, Kafka, RDBMS||
|Gegevensorkestratie|Oozie-werkstromen, Luchtstroom||
|In geheugenopzoekingen|Apache Ignite, Redis||
|Gegevensbestemmingen|HDFS, RDBMS, Kafka, MPP ||
|**Onderwerp**: **Meta-gegevens**|||
|Hive DB-type|Mysql, Postgres||
|Aantal Hive metastores|2||
|Aantal Hive-tabellen|100||
|Aantal Ranger-beleid|20||
|Aantal Oozie-werkstromen|100||
|**Onderwerp**: **Schaal**|||
|Gegevensvolume inclusief replicatie|100 TB||
|Dagelijks innamevolume|50 GB||
|Datagroeisnelheid|10% per jaar||
|Groeisnelheid clusterknooppunten|5% per jaar
|**Onderwerp**: **Clustergebruik**|||
|Gemiddeld CPU%-percentage gebruikt|60%||
|Gemiddeld geheugen % gebruikt|75%||
|Gebruikte schijfruimte|75%||
|Gemiddeld netwerk % gebruikt|25%
|**Onderwerp**: **Personeel**|||
|Aantal beheerders|2||
|Aantal ontwikkelaars|10||
|Aantal eindgebruikers|100||
|Vaardigheden|Hadoop, Vonk||
|Aantal beschikbare middelen voor migratie-inspanningen|2||
|**Onderwerp**: **Beperkingen**|||
|Huidige beperkingen|Latentie is hoog||
|Huidige uitdagingen|Gelijktijdigheidsprobleem||

### <a name="azure-requirements-questionnaire"></a>Vragenlijst voor Azure-vereisten

|**Onderwerp**: **Infrastructuur** |||
|---|---|---|
|**Vraag**|**Voorbeeld**|**Antwoord**|
| Voorkeursregio|US - oost||
|VNet voorkeur?|Ja||
|HA / DR nodig?|Ja||
|Integratie met andere cloudservices?|ADF, CosmosDB||
|**Onderwerp**: **Data Movement**  |||
|Voorkeur voor initiële belasting|DistCp, gegevensvak, ADF, WANDisco||
|Delta voor gegevensoverdracht|DistCp, AzCopy||
|Lopende incrementele gegevensoverdracht|DistCp, Sqoop||
|**Onderwerp**: **Monitoring & Alerting** |||
|Azure Monitoring & Waarschuwingen gebruiken Voor het integreren van bewaking door derden|Azure Monitoring & waarschuwen gebruiken||
|**Onderwerp**: **Beveiligingsvoorkeuren** |||
|Particuliere en beveiligde gegevenspijplijn?|Ja||
|Domein samengevoegd cluster (ESP)?|     Ja||
|On-Premises AD-synchronisatie naar cloud?|     Ja||
|Aantal AD-gebruikers dat moet worden gesynchroniseerd?|          100||
|Ok om wachtwoorden te synchroniseren met de cloud?|    Ja||
|Alleen gebruikers in de cloud?|                 Ja||
|MFA nodig?|                       Nee|| 
|Vereisten voor gegevensautorisatie?|  Ja||
|Op rollen gebaseerd toegangscontrole?|        Ja||
|Auditing nodig?|                  Ja||
|Dataencryptie in rust?|          Ja||
|Data encryptie onderweg?|       Ja||
|**Onderwerp**: **Voorkeuren voor re-architectuur** |||
|Eén cluster vs Specifieke clustertypen|Specifieke clustertypen||
|Colocated Storage Vs Remote Storage?|Externe opslag||
|Kleinere clustergrootte omdat gegevens op afstand worden opgeslagen?|Kleinere clustergrootte||
|Meerdere kleinere clusters gebruiken in plaats van één groot cluster?|Meerdere kleinere clusters gebruiken||
|Gebruik je een remote metastore?|Ja||
|Metastores delen tussen verschillende clusters?|Ja||
|Workloads deconstrueren?|Hive-taken vervangen door Spark-taken||
|ADF gebruiken voor gegevensorkestratie?|Nee||

## <a name="next-steps"></a>Volgende stappen

Lees het volgende artikel in deze serie:

- [Aanbevolen procedures voor architectuur voor on-premises naar Azure HDInsight Hadoop-migratie](apache-hadoop-on-premises-migration-best-practices-architecture.md)
