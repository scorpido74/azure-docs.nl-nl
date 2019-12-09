---
title: Release opmerkingen voor Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 5d9ca8d0df3eb0186add5c40765c87a4409a5660
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926427"
---
# <a name="release-notes"></a>Releaseopmerkingen

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is onder zakelijke klanten een van de meest populaire services voor open source Apache Hadoop- en Apache Spark-analyses in Azure.

## <a name="release-date-11072019"></a>Release datum: 11/07/2019

Deze release geldt voor HDInsight 3,6 en 4,0.

> [!IMPORTANT]  
> Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie het [artikel over HDInsight-versie beheer](hdinsight-component-versioning.md)voor meer informatie.


## <a name="new-features"></a>Nieuwe functies

### <a name="hdinsight-identity-broker-hib-preview"></a>HDInsight Identity Broker (HIB) (preview-versie)

Met HDInsight Identity Broker (HIB) kunnen gebruikers zich aanmelden bij Apache Ambari met behulp van multi-factor Authentication (MFA) en de vereiste Kerberos-tickets ophalen zonder dat wacht woord-hashes nodig zijn in Azure Active Directory Domain Services (AAD-DS). Momenteel is HIB alleen beschikbaar voor clusters die zijn geïmplementeerd via ARM-sjabloon.

### <a name="kafka-rest-api-proxy-preview"></a>Kafka rest API-proxy (preview-versie)

Kafka rest API proxy biedt één klik voor de implementatie van de Maxi maal beschik bare REST proxy met Kafka-cluster via een beveiligde AAD-autorisatie en een OAuth-protocol. 

### <a name="auto-scale"></a>Automatisch schalen

Automatisch schalen voor Azure HDInsight is nu algemeen beschikbaar in alle regio's voor de typen Apache Spark en Hadoop-cluster. Deze functie maakt het mogelijk om big data Analytics-workloads op een kostenbesparende en productieve manier te beheren. Nu kunt u het gebruik van uw HDInsight-clusters optimaliseren en betaalt u alleen voor wat u nodig hebt.

Afhankelijk van uw vereisten kunt u kiezen tussen automatische schaalaanpassing op basis van belasting of op basis van planning. Met automatisch schalen op basis van een werk belasting kunt u de cluster grootte omhoog en omlaag schalen op basis van de huidige behoeften van de resource, terwijl automatisch schalen op basis van een planning de cluster grootte kan wijzigen op basis van een vooraf gedefinieerd schema. 

Ondersteuning voor automatisch schalen voor HBase en LLAP-werk belasting is ook een open bare preview. Zie [automatisch schalen van Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-autoscale-clusters)voor meer informatie.

### <a name="hdinsight-accelerated-writes-for-apache-hbase"></a>Versnelde schrijf bewerkingen van HDInsight voor Apache HBase 

Voor verbeterde schrijfbewerkingen worden beheerde Azure Premium SSD-schijven gebruikt om de prestaties van het Apache HBase Write Ahead Log (WAL) te verbeteren. Zie [Verbeterde schrijfbewerkingen van Azure HDInsight voor Apache HBase](https://docs.microsoft.com/azure/hdinsight/hbase/apache-hbase-accelerated-writes) voor meer informatie.

### <a name="custom-ambari-db"></a>Aangepaste Ambari-database

HDInsight biedt nu een nieuwe capaciteit om klanten in staat te stellen hun eigen SQL-Data Base voor Ambari te gebruiken. Klanten kunnen nu de juiste SQL-Data Base voor Ambari kiezen en deze eenvoudig upgraden op basis van hun eigen bedrijfs groei vereiste. De implementatie wordt uitgevoerd met een Azure Resource Manager sjabloon. Zie [HDInsight-clusters instellen met een aangepaste AMBARI DB](https://docs.microsoft.com/azure/hdinsight/hdinsight-custom-ambari-db)voor meer informatie.

### <a name="f-series-virtual-machines-are-now-available-with-hdinsight"></a>Virtuele machines uit de F-serie zijn nu beschikbaar met HDInsight

Virtuele machines uit de F-serie zijn een goede keuze om aan de slag te gaan met HDInsight met vereisten voor lichte verwerking. Tegen een lagere prijs per uur levert de F-serie de beste prijs/kwaliteit-verhouding van de Azure-portfolio, gebaseerd op de ACU (Azure Compute Unit) per vCPU. Zie [de juiste VM-grootte voor uw Azure HDInsight-cluster selecteren](https://docs.microsoft.com/azure/hdinsight/hdinsight-selecting-vm-size)voor meer informatie.

## <a name="deprecation"></a>Afschaffing

### <a name="g-series-virtual-machine-deprecation"></a>Afschaffing van de virtuele machines uit de G-serie
Vanuit deze release worden virtuele machines uit de G-serie niet meer aangeboden in HDInsight.

### <a name="dv1-virtual-machine-deprecation"></a>Afschaffing van Dv1-virtuele machines
In deze release is het gebruik van Dv1 Vm's met HDInsight afgeschaft. Elke klant aanvraag voor Dv1 wordt automatisch geleverd met dv2. Er is geen prijs verschil tussen Dv1 en dv2 Vm's.

## <a name="behavior-changes"></a>Gedragswijzigingen

### <a name="cluster-managed-disk-size-change"></a>Wijziging in cluster-beheerde schijf grootte
HDInsight biedt beheerde schijf ruimte voor het cluster. In deze release wordt de beheerde schijf grootte van elk knoop punt in het nieuwe gemaakte cluster gewijzigd in 128 GB.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden doorgevoerd in de komende releases. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. Vanaf december maakt HDInsight gebruik van virtuele-machine schaal sets van Azure. Meer informatie over [virtuele-machine schaal sets van Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

### <a name="hbase-20-to-21"></a>HBase 2,0 tot 2,1
In de aanstaande release van HDInsight 4,0 wordt de versie van HBase bijgewerkt van versie 2,0 naar 2,1.

### <a name="a-series-virtual-machine-deprecation-for-esp-cluster"></a>Uitafschaffing van een virtuele machine in de A-serie voor het ESP-cluster
Vm's uit de A-serie kunnen problemen met ESP-clusters veroorzaken vanwege een relatief laag CPU-en geheugen capaciteit. In de aanstaande release worden Vm's uit de A-serie afgeschaft voor het maken van nieuwe ESP-clusters.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. U vindt [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)de huidige onderdeel versies voor HDInsight 4,0 en hdinsight 3,6.
