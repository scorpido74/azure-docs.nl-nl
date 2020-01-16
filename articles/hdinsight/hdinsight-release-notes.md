---
title: Release opmerkingen voor Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 56be45b8d0f8086d9a64811fe715fad967fca33e
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027772"
---
# <a name="release-notes"></a>Releaseopmerkingen

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-01092019"></a>Release datum: 01/09/2019

Deze release geldt voor HDInsight 3,6 en 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

> [!IMPORTANT]  
> Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie het [artikel over HDInsight-versie beheer](hdinsight-component-versioning.md)voor meer informatie.

## <a name="new-features"></a>Nieuwe functies
### <a name="tls-12-enforcement"></a>TLS 1.2 afdwingen
Transport Layer Security (TLS) en Secure Sockets Layer (SSL) zijn cryptografische protocollen die communicatie beveiliging bieden via een computer netwerk. Meer informatie over [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight gebruikt TLS 1,2 voor open bare HTTPs-eind punten, maar TLS 1,1 wordt nog steeds ondersteund voor achterwaartse compatibiliteit. 

In deze release kunnen klanten alleen voor alle verbindingen met behulp van het eind punt van het open bare cluster kiezen voor TLS 1,2. Ter ondersteuning hiervan wordt de nieuwe eigenschap **minSupportedTlsVersion** geïntroduceerd en kan deze worden opgegeven tijdens het maken van het cluster. Als de eigenschap niet is ingesteld, ondersteunt het cluster nog steeds TLS 1,0, 1,1 en 1,2, wat gelijk is aan het gedrag van vandaag. Klanten kunnen de waarde voor deze eigenschap instellen op ' 1,2 ', wat betekent dat het cluster alleen TLS 1,2 en hoger ondersteunt. Zie [een virtueel netwerk plannen-Transport Layer Security](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)voor meer informatie.

### <a name="bring-your-own-key-for-disk-encryption"></a>Uw eigen sleutel voor schijf versleuteling nemen
Alle beheerde schijven in HDInsight worden beveiligd met Azure Storage-service versleuteling (SSE). Gegevens op deze schijven worden standaard versleuteld door door micro soft beheerde sleutels. Vanaf deze release kunt u Bring Your Own Key (BYOK) voor schijf versleuteling en deze beheren met Azure Key Vault. BYOK-versleuteling is een configuratie met één stap tijdens het maken van het cluster zonder extra kosten. Registreer HDInsight als een beheerde identiteit met Azure Key Vault en voeg de versleutelings sleutel toe wanneer u uw cluster maakt. Zie door de [klant beheerde sleutel schijf versleuteling](https://docs.microsoft.com/azure/hdinsight/disk-encryption)voor meer informatie.

## <a name="deprecation"></a>Afschaffing
Geen afschaffing van deze release. Zie [aanstaande wijzigingen](#upcoming-changes)voor meer informatie over het voorbereiden van toekomstige afschaffing.

## <a name="behavior-changes"></a>Gedragswijzigingen
Er zijn geen gedrags wijzigingen voor deze versie. Zie [aanstaande wijzigingen](#upcoming-changes)voor meer informatie over het voorbereiden van aanstaande wijzigingen.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases. 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Er is een mini maal 4-core VM vereist voor het hoofd knooppunt 
Voor het hoofd knooppunt is een mini maal 4-core VM vereist om te zorgen voor hoge Beschik baarheid en betrouw baarheid van HDInsight-clusters. Vanaf 6 april 2020 kunnen klanten alleen een 4-core of een hogere VM kiezen als hoofd knooppunt voor de nieuwe HDInsight-clusters. Bestaande clusters blijven op de verwachte manier werken. 

### <a name="esp-spark-cluster-node-size-change"></a>Wijziging van grootte van ESP Spark-cluster knooppunt 
In de aanstaande release wordt de mini maal toegestane knooppunt grootte voor het ESP Spark-cluster gewijzigd in Standard_D13_V2. Vm's uit de A-serie kunnen problemen met ESP-clusters veroorzaken vanwege een relatief laag CPU-en geheugen capaciteit. Vm's uit de A-serie worden afgeschaft voor het maken van nieuwe ESP-clusters.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. In de aanstaande release maakt HDInsight gebruik van virtuele-machine schaal sets van Azure. Meer informatie over virtuele-machine schaal sets van Azure.

### <a name="hbase-20-to-21"></a>HBase 2,0 tot 2,1
In de aanstaande release van HDInsight 4,0 wordt de versie van HBase bijgewerkt van versie 2,0 naar 2,1.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. U vindt hier de huidige onderdeel versies voor HDInsight 4,0 AD HDInsight 3,6.
