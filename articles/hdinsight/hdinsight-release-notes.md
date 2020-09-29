---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: f6527a0c5712d68756310b699d214013e89f38e1
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449579"
---
# <a name="azure-hdinsight-release-notes"></a>Opmerkingen bij de release van Azure HDInsight

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-09282020"></a>Release datum: 09/28/2020

Deze versie is van toepassing op zowel HDInsight 3,6 als HDInsight 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="llap-cluster-auto-scale-general-available"></a>Algemene Beschik baarheid voor automatisch schalen van LLAP-cluster
Automatisch schalen voor het LLAP-cluster type is nu algemeen beschikbaar (GA). Alle LLAP-clusters die na 27 augustus 2020 zijn gemaakt, hebben GA ondersteuning voor automatisch schalen.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>HBase-cluster ondersteunt Premium-ADLS Gen2
HDInsight ondersteunt nu Premium ADLS Gen2 als primair opslag account voor HDInsight HBase 3,6-en 4,0-clusters. In combi natie met [versnelde schrijf bewerkingen](./hbase/apache-hbase-accelerated-writes.md)kunt u betere prestaties voor uw HBase-clusters krijgen.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Distributie van Kafka-partities in azure-fout domeinen
Een foutdomein is een logische groepering van de onderliggende hardware in een Azure-datacenter. Elk foutdomein deelt een algemene voedingsbron en netwerkswitch. Voordat HDInsight Kafka alle partitie replica's in hetzelfde fout domein kan opslaan. Vanaf deze release ondersteunt HDInsight nu automatisch de distributie van Kafka-partities op basis van Azure-fout domeinen. 

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Klanten kunnen versleuteling in transit tussen cluster knooppunten inschakelen met behulp van IPSec-versleuteling met door het platform beheerde sleutels. Deze optie kan worden ingeschakeld op het moment dat het cluster wordt gemaakt. Zie meer informatie over [het inschakelen van versleuteling tijdens de overdracht](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Versleuteling op de host
Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. Vanuit deze release kunt u versleuteling op de **tijdelijke gegevens schijf op de host inschakelen** bij het maken van het cluster. Versleuteling op host wordt alleen ondersteund op [bepaalde VM-sku's in beperkte regio's](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). HDInsight ondersteunt de [volgende knooppunt configuratie en sku's](./hdinsight-supported-node-configuration.md). Zie meer informatie over [het inschakelen van versleuteling op de host](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. Vanaf deze release wordt de service geleidelijk naar [virtuele-machine schaal sets van Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)gemigreerd. Het hele proces kan maanden duren. Nadat uw regio's en abonnementen zijn gemigreerd, worden nieuw gemaakte HDInsight-clusters uitgevoerd op virtuele-machine schaal sets zonder klant acties. Er wordt geen breuk wijziging verwacht.

## <a name="deprecation"></a>Afschaffing
Geen afschaffing voor deze release.

## <a name="behavior-changes"></a>Gedrags wijzigingen
Er is geen wijziging van het gedrag voor deze versie.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Mogelijkheid om verschillende Zookeeper-SKU'S te selecteren voor Spark-, Hadoop-en ML-Services
HDInsight biedt geen ondersteuning voor het wijzigen van Zookeeper SKU voor cluster typen van Spark, Hadoop en ML Services. Er wordt gebruikgemaakt van A2_v2/a2 SKU voor Zookeeper knooppunten en klanten worden niet in rekening gebracht. In de aanstaande release kunnen klanten de Zookeeper-SKU voor Spark-, Hadoop-en ML-Services indien nodig wijzigen. Zookeeper-knoop punten met een andere SKU dan A2_v2/a2 worden in rekening gebracht. De standaard-SKU wordt nog steeds A2_V2/a2 en gratis.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. In [dit document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.