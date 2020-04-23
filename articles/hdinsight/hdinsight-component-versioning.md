---
title: Apache Hadoop componenten en versies - Azure HDInsight
description: Leer de Apache Hadoop-componenten en -versies in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 337d36d8f5e7a7b69b98de2f68ff372d4914f905
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084767"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Wat zijn de Apache Hadoop componenten en versies beschikbaar met HDInsight?

Meer informatie over de onderdelen en versies van de [Apache Hadoop-omgeving](https://hadoop.apache.org/) in Microsoft Azure HDInsight en het Enterprise Security Package. Lees ook hoe je Hadoop-componentversies in HDInsight controleren.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop componenten beschikbaar met verschillende HDInsight versies

Azure HDInsight ondersteunt meerdere Hadoop-clusterversies die op elk gewenst moment kunnen worden geïmplementeerd. Op 4 april 2017 is de standaardclusterversie die wordt gebruikt door Azure HDInsight 3.6.

De componentversies die zijn gekoppeld aan HDInsight-clusterversies worden weergegeven in de volgende tabel:

> [!NOTE]  
> De standaardversie voor de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een afhankelijkheid van de versie hebt, geeft u de HDInsight-versie op wanneer u uw clusters maakt met de .NET SDK met Azure PowerShell en Azure Classic CLI.

| Onderdeel              | HDInsight 4.0 | HDInsight 3.6 (Standaard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop en GAREN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Varken             | 0.16.0        | 0.16.0                      |
| Apache Bijenkorf            | 3.1.0         | 2.1.0 (Niet-ESP-clusters), 1.2.1 (ESP-clusters)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0,9,0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (Zie Noot hieronder) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Vanwege overwegingen op het gebied van systeemprestaties is de ondersteuning voor Kafka-versie 0.10 in maart 2019 verlopen.

## <a name="check-for-current-hadoop-component-version-information"></a>Controleren op de huidige versie-informatie van de Hadoop-component

De Hadoop-omgevingscomponentversies die zijn gekoppeld aan HDInsight-clusterversies kunnen worden gewijzigd met updates voor HDInsight. Gebruik de Ambari REST API om de Hadoop-componenten te controleren en te controleren welke versies voor een cluster worden gebruikt. Met de opdracht **GetComponentInformation** wordt informatie over serviceonderdelen opgehaald. Zie voor meer informatie de [documentatie van Apache Ambari.](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

### <a name="release-notes"></a>Opmerkingen bij de release

Zie [HDInsight release notes](hdinsight-release-notes.md) voor extra release notes over de nieuwste versies van HDInsight.

## <a name="supported-hdinsight-versions"></a>Ondersteunde HDInsight-versies

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Ondersteuning vervaldatum en pensionering voor HDInsight-versies

**Het verlopen van** ondersteuning betekent dat Microsoft geen ondersteuning meer biedt voor de opgegeven HDInsight-versie. En het zal niet langer beschikbaar zijn via de Azure-portal voor het maken van cluster. Deze versies kunnen echter nog steeds worden gemaakt met behulp van de Azure CLI of de verschillende SDK's.

**Pensionering** van een HDInsight-versie betekent dat bestaande clusters blijven draaien zoals het is. Nieuwe clusters van deze versie kunnen echter niet op alle mogelijke manieren (inclusief CLI en SDK's) worden gemaakt. Andere functies van het besturingsvlak (zoals handmatig schalen en automatisch schalen) werken mogelijk ook niet na het pensioen van de versie. Ondersteuning is niet beschikbaar voor gepensioneerde versies.

In de volgende tabellen worden de versies van HDInsight weergegeven. De ondersteuning vervaldatumen en pensioendata worden ook verstrekt, wanneer ze bekend zijn.

### <a name="available-versions"></a>Beschikbare versies

In de volgende tabel worden de versies van HDInsight weergegeven die beschikbaar zijn in de Azure-portal en andere implementatiemethoden zoals PowerShell en .NET SDK.

| HDInsight-versie | VM-besturingssysteem | Releasedatum | Vervaldatum van ondersteuning | Pensioendatum | Maximale beschikbaarheid |  Beschikbaarheid in de Azure-portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |September 24, 2018 | | |Ja |Ja |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |4 april 2017 | 31 december 2020 |31 december 2020 |Ja |Ja |

Spark 2.1, 2.2 & Kafka 1.0-ondersteuning loopt af op 30 juni 2020.

> [!NOTE]  
> Nadat de ondersteuning voor een versie is verlopen, is deze mogelijk niet beschikbaar via de Microsoft Azure-portal. Clusterversies blijven echter beschikbaar met `Version` behulp van de parameter in de opdracht Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) en de .NET SDK tot de pensioendatum van de versie.

### <a name="retired-versions"></a>Gepensioneerde versies

In de volgende tabel worden de versies van HDInsight weergegeven die niet beschikbaar **zijn** in de Azure-portal.

| HDInsight-versie | HDP-versie | VM-besturingssysteem | Releasedatum | Vervaldatum van ondersteuning | Pensioendatum | Maximale beschikbaarheid |  Beschikbaarheid op de Azure-portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30 september 2016 |5 september 2017 |28 juni 2018 |Ja |Nee |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29 maart 2016 |29 december 2016 |9 januari 2018 |Ja |Nee |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2 december 2015 |27 juni 2016 |31 juli 2018 |Ja |Nee |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2 december 2015 |27 juni 2016 |31 juli 2017 |Ja |Nee |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS of Windows Server 2012 R2 |18 februari 2015 |1 maart 2016 |1 april 2017 |Ja |Nee |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24 juni 2014 |18 mei 2015 |30 juni 2016 |Ja |Nee |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11 februari 2014 |17 september 2014 |30 juni 2015 |Ja |Nee |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 oktober 2013 |12 mei 2014 |31 mei 2015 |Ja |Nee |
| HDInsight 1.6 |HDP 1.1 | |28 oktober 2013 |26 april 2014 |31 mei 2015 |Nee |Nee |

> [!NOTE]  
> Hoog beschikbare clusters met twee hoofdknooppunten worden standaard geïmplementeerd voor HDInsight-versie 2.1 en hoger. Ze zijn niet beschikbaar voor HDInsight versie 1.6 clusters.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package voor HDInsight

Enterprise Security is een optioneel pakket dat u toevoegen aan uw HDInsight-cluster als onderdeel van het maken van clusterwerkstroom. Het Enterprise Security Package ondersteunt:

- Integratie met Active Directory voor verificatie.

    In het verleden hebt u HDInsight-clusters gemaakt met lokale beheerdersgebruikers en lokale SSH-gebruikers. De lokale beheerder heeft toegang tot alle bestanden, mappen, tabellen en kolommen.  Met Enterprise Security Package maakt u op rollen gebaseerd toegangscontrolebeheer mogelijk door HDInsight te integreren met uw Active Directory. Dit geldt ook voor on-premises Active Directory, Azure Active Directory Domain Services. Of Active Directory op de virtuele machine iaas. Domeinbeheerder op het cluster kan gebruikers de toestemming geven om hun eigen bedrijfsnaam (domein) gebruikersnaam en wachtwoord te gebruiken.

    Zie voor meer informatie:

    - [Een inleiding tot Apache Hadoop-beveiliging met hdinsight-clusters met domeinverbonden](./domain-joined/hdinsight-security-overview.md)
    - [Azure-domeinsamengevoegde Apache Hadoop-clusters plannen in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Een aan een domein gekoppelde sandbox-omgeving configureren](./domain-joined/apache-domain-joined-configure.md)
    - [HdInsight-clusters met domein configureren met Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorisatie voor gegevens

  - Integratie met Apache Ranger voor autorisatie voor Hive-, Spark SQL- en garenwachtrijen.
  - U toegangsbeheer instellen voor bestanden en mappen.

    Zie voor meer informatie:

  - [Apache Hive-beleid configureren in hdinsight met domein](./domain-joined/apache-domain-joined-run-hive.md)

- Bekijk de controlelogboeken om toegangen en het geconfigureerde beleid te controleren.

### <a name="supported-cluster-types"></a>Ondersteunde clustertypen

Momenteel ondersteunen alleen de volgende clustertypen het Enterprise Security Package:

- Hadoop (alleen HDInsight 3.6)
- Spark
- Kafka
- HBase
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Ondersteuning voor Azure Data Lake Storage

Het Enterprise Security Package ondersteunt het gebruik van Azure Data Lake Storage als zowel de primaire opslag als de add-on storage.

### <a name="pricing-and-service-level-agreement-sla"></a>Sla (Pricing and service level agreement)

Zie [HDInsight-prijzen](https://azure.microsoft.com/pricing/details/hdinsight/)voor informatie over prijzen en SLA voor het Enterprise Security Package.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Servicelevelovereenkomst voor HDInsight-clusterversies

De servicelevelovereenkomst (SLA) wordt gedefinieerd als een _ondersteuningsvenster_. Ondersteuningsvenster is de periode waarin een HDInsight-versie wordt ondersteund door `Microsoft Customer Service and Support`. Als de versie een vervaldatum _voor ondersteuning_heeft, bevindt het HDInsight-cluster zich buiten het ondersteuningsvenster. Ondersteuning vervaldatum voor HDInsight versie X (na een nieuwere X + 1 versie beschikbaar is) is de latere:  

- Formule 1: Voeg 180 dagen toe aan de datum waarop de HDInsight-clusterversie X werd uitgebracht.
- Formule 2: Voeg 90 dagen toe aan de datum waarop de HDInsight-clusterversie X+1 beschikbaar wordt gesteld in azure-portal.

De _pensioendatum_ is de datum waarop de clusterversie niet kan worden gemaakt op HDInsight. Vanaf 31 juli 2017 u het formaat van een HDInsight-cluster na de pensioendatum niet wijzigen.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaardknooppuntconfiguratie en virtuele machineformaten voor clusters

Zie [Azure HDInsight-clusterconfiguratiegegevens](hdinsight-supported-node-configuration.md)voor meer informatie over welke SKU's voor virtuele machines u wilt selecteren voor uw cluster.

## <a name="next-steps"></a>Volgende stappen

- [Clustersetup voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Werken in Apache Hadoop op HDInsight vanaf een Windows-pc](hdinsight-hadoop-windows-tools.md)
- [Hortonworks release notes associated with Azure HDInsight versions Hortonworks release notes associated with Azure HDInsight versions Hortonworks release notes associated with Azure HDInsight versions Hortonworks](./hortonworks-release-notes.md)
