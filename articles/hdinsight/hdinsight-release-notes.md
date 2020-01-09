---
title: Release opmerkingen voor Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 2abdae95e14ecc9dab673216a2c6aef652915988
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435430"
---
# <a name="release-notes"></a>Releaseopmerkingen

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is onder zakelijke klanten een van de meest populaire services voor open source Apache Hadoop- en Apache Spark-analyses in Azure.

## <a name="release-date-12172019"></a>Release datum: 12/17/2019

Deze release geldt voor HDInsight 3,6 en 4,0.

> [!IMPORTANT]  
> Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie het [artikel over HDInsight-versie beheer](hdinsight-component-versioning.md)voor meer informatie.


## <a name="new-features"></a>Nieuwe functies

### <a name="service-tags"></a>Servicetags
Service Tags vereenvoudigen de beveiliging van virtuele Azure-machines en virtuele netwerken van Azure, waardoor u eenvoudig netwerk toegang tot de Azure-Services kunt beperken. U kunt service tags in uw NSG-regels (netwerk beveiligings groep) gebruiken om verkeer toe te staan of te weigeren voor een specifieke Azure-service, globaal of per Azure-regio. Azure biedt het onderhoud van IP-adressen die onder elke tag liggen. HDInsight-service tags voor netwerk beveiligings groepen (Nsg's) zijn groepen met IP-adressen voor status-en beheer Services. Deze groepen helpen de complexiteit te minimaliseren voor het maken van de beveiligings regel. HDInsight-klanten kunnen service tags inschakelen via Azure Portal, Power shell en REST API. Zie [NSG-service tags (netwerk beveiligings groep) voor Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-service-tags)voor meer informatie.

### <a name="custom-ambari-db"></a>Aangepaste Ambari-database
Met HDInsight kunt u nu uw eigen SQL-Data Base gebruiken voor Apache Ambari. U kunt deze aangepaste Ambari-data base configureren vanuit het Azure Portal of via de Resource Manager-sjabloon.  Met deze functie kunt u de juiste SQL-data base kiezen voor uw verwerkings-en capaciteits behoeften. U kunt ook eenvoudig een upgrade uitvoeren om te voldoen aan de vereisten voor bedrijfs groei. Zie [HDInsight-clusters instellen met een aangepaste AMBARI DB](hdinsight-custom-ambari-db.md)voor meer informatie.

![Aangepaste Ambari-database](./media/hdinsight-release-notes/custom-ambari-db.png)

## <a name="deprecation"></a>Afschaffing
Geen afschaffing van deze release. Zie [aanstaande wijzigingen](#upcoming-changes)voor meer informatie over het voorbereiden van toekomstige afschaffing.

## <a name="behavior-changes"></a>Gedragswijzigingen
Er zijn geen gedrags wijzigingen voor deze versie. Zie [aanstaande wijzigingen](#upcoming-changes)voor meer informatie over het voorbereiden van toekomstige gedrags wijzigingen.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases. 

### <a name="transport-layer-security-tls-12-enforcement"></a>Afdwinging van Transport Layer Security (TLS) 1,2
Transport Layer Security (TLS) en Secure Sockets Layer (SSL) zijn cryptografische protocollen die communicatie beveiliging bieden via een computer netwerk. Zie [Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0)voor meer informatie. Hoewel Azure HDInsight-clusters TLS 1,2-verbindingen accepteren op open bare HTTPS-eind punten, wordt TLS 1,1 nog steeds ondersteund voor achterwaartse compatibiliteit met oudere clients.

Vanaf de volgende release kunt u uw nieuwe HDInsight-clusters inschakelen en configureren, zodat alleen TLS 1,2-verbindingen worden geaccepteerd. 

Later in het jaar, te beginnen op 6/30/2020, dwingt Azure HDInsight TLS 1,2 of hogere versies af voor alle HTTPS-verbindingen. We raden u aan om ervoor te zorgen dat alle clients klaar zijn voor het verwerken van TLS 1,2 of hoger.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. Vanaf Feburary 2020 (exacte datum wordt later gecommuniceerd), maakt HDInsight gebruik van virtuele-machine schaal sets van Azure. Meer informatie over [virtuele-machine schaal sets van Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="esp-spark-cluster-node-size-change"></a>Wijziging van grootte van ESP Spark-cluster knooppunt 
In de aanstaande release:
- De mini maal toegestane knooppunt grootte voor het ESP Spark-cluster wordt gewijzigd in Standard_D13_V2. 
- Vm's uit de a-serie worden afgeschaft voor het maken van nieuwe ESP-clusters, omdat Vm's uit de A-serie kunnen leiden tot ESP-cluster problemen vanwege een relatief laag CPU-en geheugen capaciteit.

### <a name="hbase-20-to-21"></a>HBase 2,0 tot 2,1
In de aanstaande release van HDInsight 4,0 wordt de versie van HBase bijgewerkt van versie 2,0 naar 2,1.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
We hebben ondersteuning voor HDInsight 3,6 uitgebreid tot 31 december 2020. Meer informatie vindt u in [ondersteunde HDInsight-versies](hdinsight-component-versioning.md#supported-hdinsight-versions).

Geen wijziging van onderdeel versie voor HDInsight 4,0.

Apache Zeppelin op HDInsight 3,6:0.7.0--> 0.7.3. 

In [dit document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)vindt u de meest recente onderdeel versies.

## <a name="new-regions"></a>Nieuwe regio's

### <a name="uae-north"></a>VAE - noord
De beheer-IP-adressen van UAE-noord zijn: `65.52.252.96` en `65.52.252.97`.
