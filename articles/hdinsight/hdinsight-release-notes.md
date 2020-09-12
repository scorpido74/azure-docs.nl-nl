---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: fd0412459e7d6e51b6abdccbc8782d157acee6b9
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89319794"
---
# <a name="azure-hdinsight-release-notes"></a>Opmerkingen bij de release van Azure HDInsight

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-08092020"></a>Release datum: 08/09/2020

Deze release is alleen van toepassing op HDInsight 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="support-for-sparkcruise"></a>Ondersteuning voor SparkCruise
SparkCruise is een systeem voor het hergebruik van automatische berekeningen voor Spark. Gemeen schappelijke subexpressies worden geselecteerd voor realiseren op basis van de query workload van het verleden. SparkCruise resultatenset deze subexpressies als onderdeel van het verwerken van query's en hergebruik van berekeningen worden automatisch op de achtergrond toegepast. U kunt profiteren van SparkCruise zonder enige wijziging van de Spark-code.
 
### <a name="support-hive-view-for-hdinsight-40"></a>Hive-weer gave voor HDInsight 4,0
De Apache Ambari-Hive-weer gave is ontworpen om u te helpen bij het ontwerpen, optimaliseren en uitvoeren van Hive-query's vanuit uw webbrowser. De Hive-weer gave wordt standaard ondersteund voor HDInsight 4,0-clusters vanaf deze release. Het is niet van toepassing op bestaande clusters. U moet het cluster verwijderen en opnieuw maken om de ingebouwde Hive-weer gave te krijgen.
 
### <a name="support-tez-view-for-hdinsight-40"></a>TEZ-weer gave voor HDInsight 4,0
De Apache TEZ-weer gave wordt gebruikt om de uitvoering van de Hive TEZ-taak bij te houden en op te sporen. De weer gave TEZ wordt standaard ondersteund voor HDInsight 4,0 vanaf deze release. Het is niet van toepassing op bestaande clusters. U moet het cluster verwijderen en opnieuw maken om de ingebouwde weer gave TEZ te krijgen.

## <a name="deprecation"></a>Afschaffing
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Afschaffing van Apache Spark 2.1 en 2.2 in HDInsight 3.6 Spark-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Spark-clusters maken met Spark 2,1 en 2,2 op HDInsight 3,6. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om over te stappen op Spark 2,3 op HDInsight 3,6 van juni 30 2020 om mogelijke onderbreking van systeem/ondersteuning te voor komen.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Afschaffing van Apache Spark 2.3 in HDInsight 4.0 Spark-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Spark-clusters maken met Spark 2,3 op HDInsight 4,0. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om de overstap naar Apache Spark 2.4 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Afschaffing van Kafka 1.1 in HDInsight 4.0 Kafka-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Kafka-clusters maken met Kafka 1,1 op HDInsight 4,0. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om de overstap naar Kafka 2.1 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.

## <a name="behavior-changes"></a>Gedrags wijzigingen
### <a name="ambari-stack-version-change"></a>Ambari stack-versie wijziging
In deze versie verandert de Ambari-versie van 2. x. x. x in 4,1. U kunt de stack versie (HDInsight 4,1) controleren in Ambari: Ambari >-versies van gebruikers >.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
Er zijn geen aanstaande Afbrekings wijzigingen die u moet aanbest Eden.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

Hieronder vindt u een back-uppoort voor de Hive-JIRAs:
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

Hieronder vindt u een back-up van de JIRAs voor HBase:
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. In [dit document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.

## <a name="known-issues"></a>Bekende problemen

Er is een probleem opgelost in Azure Portal, waarbij gebruikers een fout ondervonden tijdens het maken van een Azure HDInsight-cluster met een openbare sleutel als SSH-verificatietype. Wanneer gebruikers op **Beoordelen + Maken** klikken, verschijnt de fout 'Mag geen drie opeenvolgende tekens uit de SSH-gebruikersnaam bevatten'. Dit probleem is opgelost, maar mogelijk moet u de cache van de browser vernieuwen door op CTRL + F5 te drukken om de gecorrigeerde weergave te laden. De tijdelijke oplossing voor dit probleem was het maken van een cluster met een ARM-sjabloon. 
