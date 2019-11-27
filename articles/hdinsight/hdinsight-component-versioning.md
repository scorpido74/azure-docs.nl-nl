---
title: Apache Hadoop onderdelen en versies-Azure HDInsight
description: Meer informatie over de Apache Hadoop onderdelen en versies in azure HDInsight.
keywords: Hadoop-versies, Hadoop ecosysteem-onderdelen, Hadoop-onderdelen, Hadoop-versie controleren
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/21/2019
ms.openlocfilehash: b5687d659103eed0fcba70319aa37030bd85165a
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539112"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Wat zijn de Apache Hadoop onderdelen en versies die beschikbaar zijn in HDInsight?

Meer informatie over de [Apache Hadoop](https://hadoop.apache.org/) ecosysteem onderdelen en versies in Microsoft Azure HDInsight, evenals de Enterprise Security Package. Leer ook hoe u de versies van Hadoop-onderdelen in HDInsight kunt controleren.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop onderdelen die beschikbaar zijn in verschillende versies van HDInsight

Azure HDInsight ondersteunt meerdere Hadoop-cluster versies die op elk gewenst moment kunnen worden geïmplementeerd. Vanaf 4 april 2017 is de standaard cluster versie die wordt gebruikt door Azure HDInsight 3,6.

De versies van de onderdelen die zijn gekoppeld aan HDInsight-cluster versies worden weer gegeven in de volgende tabel:

> [!NOTE]  
> De standaard versie voor de HDInsight-service kan zonder kennisgeving worden gewijzigd. Als u een versie afhankelijkheid hebt, geeft u de HDInsight-versie op wanneer u uw clusters maakt met behulp van de .NET SDK met Azure PowerShell en klassieke CLI van Azure.

| Component              | HDInsight 4.0 | HDInsight 3,6 (standaard)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop en garen | punt         | 2.7.3                       |
| Apache TEZ             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0, 1.2.1                |
| Apache TEZ Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | wel         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (zie opmerking hieronder) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.0                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Vanwege systeem prestatie overwegingen is de ondersteuning voor Kafka-versie 0,10 in maart 2019 verlopen.

## <a name="check-for-current-hadoop-component-version-information"></a>Controleren op huidige versie gegevens van Hadoop-onderdeel

De versie van het Hadoop-ecosysteem onderdeel dat is gekoppeld aan HDInsight-cluster versies kan worden gewijzigd met updates voor HDInsight. Als u de Hadoop-onderdelen wilt controleren en wilt controleren welke versies worden gebruikt voor een cluster, gebruikt u de Ambari-REST API. De **GetComponentInformation** -opdracht haalt informatie op over service onderdelen. Zie de [Apache Ambari-documentatie](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)voor meer informatie.

### <a name="release-notes"></a>Releaseopmerkingen

Zie [opmerkingen](hdinsight-release-notes.md) bij de release van hdinsight voor aanvullende release opmerkingen over de nieuwste versies van HDInsight.

## <a name="supported-hdinsight-versions"></a>Ondersteunde HDInsight-versies

De volgende tabellen geven een lijst van de versies van HDInsight. De ondersteunings vervaldatums en pensioen datums worden ook gegeven, wanneer ze bekend zijn.

### <a name="available-versions"></a>Beschik bare versies

De volgende tabel geeft een lijst van de versies van HDInsight die beschikbaar zijn in de Azure Portal, evenals andere implementatie methoden, zoals Power shell en .NET SDK.

| HDInsight-versie | VM-BESTURINGS SYSTEEM | Releasedatum | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |  Beschik baarheid in de Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24 september 2018 | | |Ja |Ja |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4 april 2017 | 31 december 2020 |31 december 2020 |Ja |Ja |

Spark 2,1, 2,2 & Kafka 1,0-ondersteuning verloopt op 30 juni 2020.

> [!NOTE]  
> Nadat de ondersteuning voor een versie is verlopen, is deze mogelijk niet meer beschikbaar via de Microsoft Azure-portal. Cluster versies blijven echter beschikbaar met behulp van de para meter `Version` in de Windows Power shell [-opdracht New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) en de .NET SDK tot de datum van beëindiging van de versie.

### <a name="retired-versions"></a>Buiten gebruik gestelde versies

De volgende tabel geeft een lijst van de versies van HDInsight die **niet** beschikbaar zijn in de Azure Portal.

| HDInsight-versie | HDP-versie | VM-BESTURINGS SYSTEEM | Releasedatum | Verval datum ondersteuning | Buitengebruikstellings datum | Hoge beschikbaarheid |  Beschik baarheid op het Azure Portal |
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

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package voor HDInsight

Enter prise Security is een optioneel pakket dat u kunt toevoegen aan uw HDInsight-cluster als onderdeel van de werk stroom voor het maken van een cluster. Het Enterprise Security Package ondersteunt:

- Integratie met Active Directory voor authenticatie.

    In het verleden kunt u alleen HDInsight-clusters maken met een lokale beheerders gebruiker en een lokale SSH-gebruiker. De lokale beheerder gebruiker heeft toegang tot alle bestanden, mappen, tabellen en kolommen.  Met de Enterprise Security Package kunt u toegangs beheer op basis van rollen inschakelen door HDInsight-clusters te integreren met uw eigen Active Directory, waaronder on-premises Active Directory, Azure Active Directory Domain Services of Active Directory op IaaS virtuele machine. Domein beheerder op het cluster kan gebruikers toestaan hun eigen bedrijfs naam en wacht woord (domein) te gebruiken voor toegang tot het cluster.

    Zie voor meer informatie:

    - [Een inleiding tot Apache Hadoop beveiliging met HDInsight-clusters die zijn toegevoegd aan een domein](./domain-joined/hdinsight-security-overview.md)
    - [Apache Hadoop clusters die zijn toegevoegd aan het Azure-domein plannen in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Een aan een domein gekoppelde sandbox-omgeving configureren](./domain-joined/apache-domain-joined-configure.md)
    - [HDInsight-clusters die zijn gekoppeld aan een domein configureren met behulp van Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorisatie voor gegevens

  - Integratie met Apache zwerver voor autorisatie voor Hive-, Spark SQL-en garen-wacht rijen.
  - U kunt toegangs beheer instellen voor bestanden en mappen.

    Zie voor meer informatie:

  - [Apache Hive-beleid configureren in HDInsight die lid is van een domein](./domain-joined/apache-domain-joined-run-hive.md)

- Bekijk de audit Logboeken om de toegang tot en het geconfigureerde beleid te bewaken.

### <a name="supported-cluster-types"></a>Ondersteunde cluster typen

Momenteel ondersteunen alleen de volgende cluster typen de Enterprise Security Package:

- Hadoop (alleen HDInsight 3,6)
- Spark
- Interactieve query

### <a name="support-for-azure-data-lake-storage"></a>Ondersteuning voor Azure Data Lake Storage

De Enterprise Security Package ondersteunt het gebruik van Azure Data Lake Storage als de primaire opslag en de opslag van de invoeg toepassing.

### <a name="pricing-and-service-level-agreement"></a>Prijzen en service level agreement

Zie [prijzen voor HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie over prijzen en sla voor de Enterprise Security Package.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Service overeenkomst voor HDInsight-cluster versies

De service level agreement (SLA) wordt gedefinieerd in termen van een _ondersteunings venster_. Het ondersteunings venster is de periode dat een HDInsight-cluster versie wordt ondersteund door de klanten service en ondersteuning van micro soft. Als de versie een _ondersteunings vervaldatum_ heeft die is door gegeven, bevindt het HDInsight-cluster zich buiten het ondersteunings venster. De verval datum van de ondersteuning voor een opgegeven HDInsight-versie X (na een nieuwere X + 1-versie is beschikbaar) wordt berekend als de volgende:  

- Formule 1: Voeg 180 dagen toe aan de datum waarop het HDInsight-cluster versie X is uitgebracht.
- Formule 2: Voeg 90 dagen toe aan de datum waarop de versie van het HDInsight-cluster, X + 1, beschikbaar wordt gemaakt in Azure Portal.

De _datum van beëindiging_ is de datum waarna de Cluster versie niet kan worden gemaakt op HDInsight. Vanaf 31 juli 2017 kunt u de grootte van een HDInsight-cluster niet aanpassen na de aftredings datum.

> [!NOTE]  
> HDInsight Windows-clusters (inclusief versies 2,1, 3,0, 3,1, 3,2 en 3,3) worden uitgevoerd op de Azure-gast besturingssysteem versie 4, die gebruikmaakt van de 64-bits versie van Windows Server 2012 R2. Azure Guest OS-familie versie 4 ondersteunt de .NET Framework versies 4,0, 4,5, 4.5.1 en 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Release opmerkingen voor Hortonworks die zijn gekoppeld aan HDInsight-versies

De sectie bevat koppelingen naar release opmerkingen voor de Hortonworks-gegevens platform distributies en Apache-onderdelen die worden gebruikt met HDInsight.
* HDInsight-cluster versie 4,0 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 3,0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* HDInsight-cluster versie 3,6 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 2,6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* HDInsight-cluster versie 3,5 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 2,5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). HDInsight-cluster versie 3,5 is de _standaard_ Hadoop-cluster die in de Azure Portal is gemaakt.
* HDInsight-cluster versie 3,4 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 2,4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* HDInsight-cluster versie 3,3 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 2,3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Apache Storm release opmerkingen](https://storm.apache.org/2015/11/05/storm0100-released.html) zijn beschikbaar op de Apache-website.
  * [Apache Hive release opmerkingen](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) zijn beschikbaar op de Apache-website.
* HDInsight-cluster versie 3,2 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 2,2][hdp-2-2].

  * Release opmerkingen voor specifieke Apache-onderdelen zijn als volgt beschikbaar: [Hive 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [varkens 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [garen 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [TEZ 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)en [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-cluster versie 3,1 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data platform 2.1.7][hdp-2-1-7]. HDInsight 3,1-clusters die zijn gemaakt vóór november, 7, 2014, zijn gebaseerd op [Hortonworks data platform 2.1.1][hdp-2-1-1].
* HDInsight-cluster versie 3,0 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 2,0][hdp-2-0-8].
* HDInsight-cluster versie 2,1 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 1,3][hdp-1-3-0].
* HDInsight-cluster versie 1,6 maakt gebruik van een Hadoop-distributie die is gebaseerd op [Hortonworks data Platform 1,1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaard knooppunt configuratie en grootte van virtuele machines voor clusters

Zie [Configuratie Details van Azure HDInsight-cluster](hdinsight-supported-node-configuration.md)voor meer informatie over de virtuele-machine-sku's die u voor uw cluster wilt selecteren.

## <a name="next-steps"></a>Volgende stappen

- [Setup van het cluster voor Apache Hadoop, Spark en meer op HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Werken in Apache Hadoop op HDInsight vanaf een Windows-computer](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
