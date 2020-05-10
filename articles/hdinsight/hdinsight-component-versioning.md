---
title: Apache Hadoop onderdelen en versies-Azure HDInsight
description: Meer informatie over de Apache Hadoop onderdelen en versies in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 05/08/2020
ms.openlocfilehash: 98bb643e1a780754fcb3b9f4eb91d8f04dc4e90b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996392"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Apache-onderdelen en-versies die beschikbaar zijn voor Azure HDInsight

Meer informatie over de onderdelen en versies van de [Apache Hadoop](https://hadoop.apache.org/) -omgeving in Microsoft Azure HDInsight en de Enterprise Security Package. Leer ook hoe u de versies van Hadoop-onderdelen in HDInsight kunt controleren.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Apache-onderdelen die beschikbaar zijn met verschillende HDInsight-versies

Azure HDInsight ondersteunt meerdere Hadoop-cluster versies die op elk gewenst moment kunnen worden geïmplementeerd. Op 4 april 2017 is de standaard cluster versie die door Azure HDInsight wordt gebruikt 3,6.

De versies van de onderdelen die zijn gekoppeld aan HDInsight-cluster versies worden weer gegeven in de volgende tabel:

> [!NOTE]  
> De standaard versie voor de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een versie afhankelijkheid hebt, geeft u de HDInsight-versie op wanneer u uw clusters maakt met behulp van de .NET SDK met Azure PowerShell en klassieke CLI van Azure.

| Onderdeel              | HDInsight 4.0 | HDInsight 3,6 (standaard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop en garen | punt         | 2.7.3                       |
| Apache TEZ             | 0.9.1         | 0.7.0                       |
| Apache-Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 op ESP-interactieve query) |
| Apache TEZ Hive2       | -             | 0.8.4                       |
| Apache zwerver          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0.5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (zie opmerking hieronder) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Vanwege systeem prestatie overwegingen is de ondersteuning voor Kafka-versie 0,10 in maart 2019 verlopen.

## <a name="check-for-current-apache-component-version-information"></a>Controleren op huidige versie gegevens van Apache-onderdeel

De versie van de Hadoop-omgevings onderdelen die zijn gekoppeld aan HDInsight-cluster versies kunnen veranderen met updates voor HDInsight. Als u de Hadoop-onderdelen wilt controleren en wilt controleren welke versies worden gebruikt voor een cluster, gebruikt u de Ambari-REST API. De **GetComponentInformation** -opdracht haalt informatie op over service onderdelen. Zie de [Apache Ambari-documentatie](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)voor meer informatie.

### <a name="release-notes"></a>Releaseopmerkingen

Zie [opmerkingen](hdinsight-release-notes.md) bij de release van hdinsight voor aanvullende release opmerkingen over de nieuwste versies van HDInsight.

## <a name="supported-hdinsight-versions"></a>Ondersteunde HDInsight-versies

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Verval en buiten gebruiks telling voor HDInsight-versies ondersteunen

**Verval van ondersteuning** betekent dat micro soft niet langer ondersteuning biedt voor de opgegeven HDInsight-versie. En is niet meer beschikbaar via de Azure Portal voor het maken van een cluster. Deze versies kunnen echter nog steeds worden gemaakt met behulp van de Azure CLI of de verschillende Sdk's.

**Buiten** gebruik stellen van een HDInsight-versie houdt in dat bestaande clusters nog steeds worden uitgevoerd. Nieuwe clusters van deze versie kunnen echter niet op een wille keurige manier worden gemaakt (met inbegrip van CLI en Sdk's). Andere functies van het besturings systeem (zoals hand matig schalen en automatisch schalen) werken mogelijk ook niet na het buiten gebruik stellen van de versie. Ondersteuning is niet beschikbaar voor niet-verouderde versies.

De volgende tabellen geven een lijst van de versies van HDInsight. De ondersteunings vervaldatums en pensioen datums worden ook gegeven, wanneer ze bekend zijn.

### <a name="available-versions"></a>Beschik bare versies

De volgende tabel bevat de versies van HDInsight die beschikbaar zijn in de Azure Portal en andere implementatie methoden, zoals Power shell en .NET SDK.

| HDInsight-versie | VM-besturingssysteem | Releasedatum | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |  Beschik baarheid in de Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |September 24, 2018 | | |Ja |Ja |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4 april 2017 | 31 december 2020 |31 december 2020 |Ja |Ja |

Spark 2,1, 2,2 & Kafka 1,0-ondersteuning verloopt op 30 juni 2020.

> [!NOTE]  
> Nadat de ondersteuning voor een versie is verlopen, is deze mogelijk niet meer beschikbaar via de Microsoft Azure-portal. Cluster versies blijven echter beschikbaar met behulp van de `Version` para meter in de Windows Power shell [-opdracht New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) en de .NET SDK tot de datum van beëindiging van de versie.

### <a name="retired-versions"></a>Buiten gebruik gestelde versies

De volgende tabel geeft een lijst van de versies van HDInsight die **niet** beschikbaar zijn in de Azure Portal.

| HDInsight-versie | HDP-versie | VM-besturingssysteem | Releasedatum | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |  Beschik baarheid op het Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30 september 2016 |5 september 2017 |28 juni 2018 |Ja |Nee |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |29 maart 2016 |29 december 2016 |9 januari 2018 |Ja |Nee |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2 december 2015 |27 juni 2016 |31 juli 2018 |Ja |Nee |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2 december 2015 |27 juni 2016 |31 juli 2017 |Ja |Nee |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS of Windows Server 2012 R2 |18 februari 2015 |1 maart 2016 |1 april 2017 |Ja |Nee |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |24 juni 2014 |18 mei 2015 |30 juni 2016 |Ja |Nee |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |11 februari 2014 |17 september 2014 |30 juni 2015 |Ja |Nee |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |28 oktober 2013 |12 mei 2014 |31 mei 2015 |Ja |Nee |
| HDInsight 1,6 |HDP 1,1 | |28 oktober 2013 |26 april 2014 |31 mei 2015 |Nee |Nee |

> [!NOTE]  
> Maxi maal beschik bare clusters met twee hoofd knooppunten worden standaard geïmplementeerd voor HDInsight-versie 2,1 en hoger. Ze zijn niet beschikbaar voor HDInsight-versie 1,6-clusters.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Service overeenkomst voor HDInsight-cluster versies

De service level agreement (SLA) wordt gedefinieerd als een _ondersteunings venster_. Ondersteunings venster is de tijds periode waarin een HDInsight- `Microsoft Customer Service and Support`versie wordt ondersteund door. Als de versie de _verval datum van de ondersteunings ondersteuning_heeft, bevindt het HDInsight-cluster zich buiten het ondersteunings venster. Ondersteuning voor verloop tijd van HDInsight-versie X (nadat een nieuwere X + 1-versie beschikbaar is), is de volgende:  

- Formule 1: Voeg 180 dagen toe aan de datum waarop het HDInsight-cluster versie X is uitgebracht.
- Formule 2: Voeg 90 dagen toe aan de datum waarop de versie van het HDInsight-cluster, X + 1, beschikbaar wordt gemaakt in Azure Portal.

De _datum van pensionering_ is de datum waarna de Cluster versie niet kan worden gemaakt op HDInsight. Vanaf 31 juli 2017 kunt u de grootte van een HDInsight-cluster niet aanpassen na de aftredings datum.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaard knooppunt configuratie en grootte van virtuele machines voor clusters

Zie [Configuratie Details van Azure HDInsight-cluster](hdinsight-supported-node-configuration.md)voor meer informatie over de virtuele-machine-sku's die u voor uw cluster wilt selecteren.

## <a name="next-steps"></a>Volgende stappen

- [Setup van het cluster voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)
- [Release opmerkingen voor Hortonworks die zijn gekoppeld aan Azure HDInsight-versies](./hortonworks-release-notes.md)
- [Enterprise Security Package](./enterprise-security-package.md)