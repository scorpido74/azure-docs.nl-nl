---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006531"
---
# <a name="azure-hdinsight-release-notes"></a>Opmerkingen bij de release van Azure HDInsight

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-07132020"></a>Release datum: 07/13/2020

Deze release geldt voor HDInsight 3,6 en 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>Ondersteuning voor Klanten-lockbox voor Microsoft Azure
Azure HDInsight ondersteunt nu Azure Klanten-lockbox. Het biedt klanten een interface om aanvragen voor toegang tot klant gegevens te controleren en goed te keuren of af te wijzen. Het wordt gebruikt wanneer een micro soft-Engineer toegang heeft tot klant gegevens tijdens een ondersteunings aanvraag. Zie [klanten-lockbox voor Microsoft Azure](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview)voor meer informatie.

### <a name="service-endpoint-policies-for-storage"></a>Service-eindpunt beleid voor opslag
Klanten kunnen nu gebruikmaken van het service-eindpunt beleid (SEP) op het subnet van het HDInsight-cluster. Meer informatie over [Azure service Endpoint-beleid](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="deprecation"></a>Afschaffing
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Afschaffing van Apache Spark 2.1 en 2.2 in HDInsight 3.6 Spark-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Spark-clusters maken met Spark 2,1 en 2,2 op HDInsight 3,6. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om over te stappen op Spark 2,3 op HDInsight 3,6 van juni 30 2020 om mogelijke onderbreking van systeem/ondersteuning te voor komen.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Afschaffing van Apache Spark 2.3 in HDInsight 4.0 Spark-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Spark-clusters maken met Spark 2,3 op HDInsight 4,0. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om de overstap naar Apache Spark 2.4 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Afschaffing van Kafka 1.1 in HDInsight 4.0 Kafka-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Kafka-clusters maken met Kafka 1,1 op HDInsight 4,0. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om de overstap naar Kafka 2.1 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.

## <a name="behavior-changes"></a>Gedrags wijzigingen
U hoeft geen aandacht te best Eden aan de wijzigingen.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases. 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Mogelijkheid om verschillende Zookeeper-SKU'S te selecteren voor Spark-, Hadoop-en ML-Services
HDInsight biedt geen ondersteuning voor het wijzigen van Zookeeper SKU voor cluster typen van Spark, Hadoop en ML Services. Er wordt gebruikgemaakt van A2_v2/a2 SKU voor Zookeeper knooppunten en klanten worden niet in rekening gebracht. In de aanstaande release kunnen klanten de Zookeeper-SKU voor Spark-, Hadoop-en ML-Services zo nodig wijzigen. Zookeeper-knoop punten met een andere SKU dan A2_v2/a2 worden in rekening gebracht. De standaard-SKU wordt nog steeds A2_V2/a2 en gratis.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 
### <a name="fixed-hive-warehouse-connector-issue"></a>Probleem met vast Hive Warehouse-connector
Er is een probleem opgetreden bij de bruikbaarheid van Hive Warehouse-connectors in de vorige versie. Het probleem is opgelost. 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>Probleem met vaste Zeppelin-notebook afgebroken voorloop nullen oplossen
Zeppelin is onjuist afgekapt voor voorloop nullen in de tabel uitvoer voor teken reeks indeling. Dit probleem is opgelost in deze release.

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. In [dit document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.
