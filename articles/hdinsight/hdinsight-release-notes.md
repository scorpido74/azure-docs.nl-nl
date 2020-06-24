---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: c7bc818133a0bd708f9f850f0ad258dccc6c02cc
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737985"
---
# <a name="release-notes"></a>Releaseopmerkingen

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-06112020"></a>Release datum: 06/11/2020

Deze release geldt voor HDInsight 3,6 en 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt gebruik van virtuele machines van Azure om het cluster nu in te richten. Vanuit deze release beginnen nieuwe HDInsight-clusters met behulp van virtuele-machine schaal sets van Azure. De wijziging wordt geleidelijk uitgevouwen. U zou verwachten dat er geen breuk wordt gewijzigd. Meer informatie over [virtuele-machine schaal sets van Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Vm's in HDInsight-cluster opnieuw opstarten
In deze release ondersteunen we het opnieuw opstarten van Vm's in HDInsight-cluster om niet-reagerende knoop punten opnieuw op te starten. Momenteel kunt u dit alleen doen via API-, Power shell-en CLI-ondersteuning. Zie [dit document](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json)voor meer informatie over de API.
 
## <a name="deprecation"></a>Afschaffing
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Afschaffing van Apache Spark 2.1 en 2.2 in HDInsight 3.6 Spark-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Spark-clusters maken met Spark 2,1 en 2,2 op HDInsight 3,6. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om over te stappen op Spark 2,3 op HDInsight 3,6 van juni 30 2020 om mogelijke onderbreking van systeem/ondersteuning te voor komen.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Afschaffing van Apache Spark 2.3 in HDInsight 4.0 Spark-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Spark-clusters maken met Spark 2,3 op HDInsight 4,0. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om de overstap naar Apache Spark 2.4 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Afschaffing van Kafka 1.1 in HDInsight 4.0 Kafka-cluster
Vanaf juli 1 2020 kunnen klanten geen nieuwe Kafka-clusters maken met Kafka 1,1 op HDInsight 4,0. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Overweeg om de overstap naar Kafka 2.1 op HDInsight 4.0 voor 30 juni 2020 te maken om potentiële systeem- en ondersteuningsonderbrekingen te voorkomen.
 
## <a name="behavior-changes"></a>Gedrags wijzigingen
### <a name="esp-spark-cluster-head-node-size-change"></a>Grootte wijziging in het hoofd knooppunt van ESP Spark-cluster 
De mini maal toegestane grootte van het hoofd knooppunt voor het ESP Spark-cluster wordt gewijzigd in Standard_D13_V2. Vm's met lage kernen en geheugen als hoofd knooppunt kunnen problemen met ESP-clusters veroorzaken vanwege een relatief laag CPU-en geheugen capaciteit. Gebruik vanaf release Sku's hoger dan Standard_D13_V2 en Standard_E16_V3 als hoofd knooppunt voor ESP Spark-clusters.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Er is een mini maal 4-core VM vereist voor het hoofd knooppunt 
Voor het hoofd knooppunt is een mini maal 4-core VM vereist om te zorgen voor hoge Beschik baarheid en betrouw baarheid van HDInsight-clusters. Vanaf april 6 2020 kunnen klanten alleen kiezen voor 4-core of een hogere VM als hoofd knooppunt voor de nieuwe HDInsight-clusters. Bestaande clusters blijven op de verwachte manier worden uitgevoerd. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Wijziging van de inrichting van het cluster worker-knoop punt
Wanneer 80% van de worker-knoop punten gereed is, wordt het cluster geactiveerd in **operationele** fase. In deze fase kunnen klanten alle gegevenslaag bewerkingen uitvoeren, zoals het uitvoeren van scripts en taken. Klanten kunnen echter geen beheer bewerkingen uitvoeren zoals omhoog/omlaag schalen. Alleen verwijderen wordt ondersteund.
 
Na de **operationele** fase wacht het cluster nog een 60 minuten voor de resterende 20% worker-knoop punten. Aan het einde van deze 60 minuten wordt het cluster verplaatst naar het **actieve** stadium, zelfs als niet alle worker-knoop punten nog niet beschikbaar zijn. Zodra een cluster het **actieve** stadium binnenkomt, kunt u dit als normaal gebruiken. Beide bewerkings plan bewerkingen, zoals het omhoog/omlaag schalen en data-plan bewerkingen, zoals het uitvoeren van scripts en taken, worden geaccepteerd. Als sommige van de aangevraagde worker-knoop punten niet beschikbaar zijn, wordt het cluster gemarkeerd als gedeeltelijk geslaagd. Er worden kosten in rekening gebracht voor de knoop punten die zijn geïmplementeerd. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Nieuwe service-principal maken via HDInsight
Voorheen kunnen klanten bij het maken van een cluster een nieuwe service-principal maken voor toegang tot het verbonden ADLS gen 1-account in Azure Portal. Vanaf 15 2020 juni kunnen klanten geen nieuwe service-principal maken in de werk stroom voor het maken van HDInsight. alleen bestaande service-principal wordt ondersteund. Zie [Service-Principal en certificaten maken met behulp van Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).
 
## <a name="upcoming-changes"></a>Aanstaande wijzigingen
Er zijn geen aanstaande Afbrekings wijzigingen die u moet aanbest Eden.
 
## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 
 
## <a name="component-version-change"></a>Onderdeel versie wijzigen
### <a name="hbase-20-to-216"></a>HBase 2,0 tot 2.1.6
HBase-versie wordt bijgewerkt van versie 2,0 naar 2.1.6.
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 naar 2.4.4
Spark-versie wordt bijgewerkt van versie 2.4.0 naar 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 tot en met 2.1.1
Kafka-versie wordt bijgewerkt van versie 2.1.0 naar 2.1.1.
 
In [dit document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) vindt u de huidige versie van de onderdelen voor HDInsight 4,0 ad hdinsight 3,6

