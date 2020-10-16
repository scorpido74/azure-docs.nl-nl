---
title: Opmerkingen bij de release van Azure HDInsight
description: Nieuwste opmerkingen bij de release voor Azure HDInsight. Bekijk ontwikkel tips en Details voor Hadoop, Spark, R Server, Hive en meer.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974563"
---
# <a name="azure-hdinsight-release-notes"></a>Opmerkingen bij de release van Azure HDInsight

Dit artikel bevat informatie over de **meest recente** updates voor Azure HDInsight-release. Zie voor meer informatie over eerdere versies het [HDInsight Release Notes-archief](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Samenvatting

Azure HDInsight is een van de populairste services van zakelijke klanten voor open-source analyses op Azure.

## <a name="release-date-10082020"></a>Release datum: 10/08/2020

Deze versie is van toepassing op zowel HDInsight 3,6 als HDInsight 4,0. HDInsight-release wordt beschikbaar gesteld voor alle regio's over enkele dagen. De release datum geeft hier de release datum van de eerste regio aan. Als de onderstaande wijzigingen niet worden weer gegeven, wacht u tot de release over enkele dagen in uw regio actief is.

## <a name="new-features"></a>Nieuwe functies
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Persoonlijke HDInsight-clusters zonder open bare IP-en privé-koppeling (preview-versie)
HDInsight ondersteunt nu het maken van clusters zonder open bare IP-en privé koppelings toegang tot de clusters als preview. Klanten kunnen de nieuwe geavanceerde netwerk instellingen gebruiken voor het maken van een volledig geïsoleerd cluster zonder openbaar IP-adres en hun eigen privé-eind punten gebruiken om toegang te krijgen tot het cluster. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Verplaatsen naar schaal sets voor virtuele Azure-machines
HDInsight maakt nu gebruik van virtuele machines van Azure om het cluster in te richten. Vanaf deze release wordt de service geleidelijk naar [virtuele-machine schaal sets van Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)gemigreerd. Het hele proces kan maanden duren. Nadat uw regio's en abonnementen zijn gemigreerd, worden nieuw gemaakte HDInsight-clusters uitgevoerd op virtuele-machine schaal sets zonder klant acties. Er wordt geen breuk wijziging verwacht.

## <a name="deprecation"></a>Afschaffing
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Afschaffing van HDInsight 3,6 ML Services-cluster
Het cluster type van HDInsight 3,6 ML-Services is einde van ondersteuning door DEC 31 2020. Klanten maken hierna geen nieuwe 3,6 ML-Services-clusters. Bestaande clusters worden uitgevoerd zonder de ondersteuning van micro soft. Controleer [hier](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)het ondersteunings verloop voor HDInsight-versies en cluster typen.

## <a name="behavior-changes"></a>Gedrags wijzigingen
Er is geen wijziging van het gedrag voor deze versie.

## <a name="upcoming-changes"></a>Aanstaande wijzigingen
De volgende wijzigingen worden uitgevoerd in toekomstige releases.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Mogelijkheid om verschillende Zookeeper van virtuele machines te selecteren voor Spark-, Hadoop-en ML-Services
HDInsight biedt geen ondersteuning voor het aanpassen van de grootte van Zookeeper-knoop punten voor de cluster typen Spark, Hadoop en ML Services. De standaard instelling is dat de grootte van de virtuele machines van/a2 wordt A2_v2, die gratis worden meegeleverd. In de aanstaande release kunt u de grootte van een virtuele Zookeeper-machine selecteren die het meest geschikt is voor uw scenario. Zookeeper-knoop punten met een andere grootte dan A2_v2/a2 worden in rekening gebracht. A2_v2 en a2 virtuele machines worden nog gratis beschikbaar gesteld.

## <a name="bug-fixes"></a>Opgeloste fouten
HDInsight blijft de betrouw baarheid en prestaties van het cluster verbeteren. 

## <a name="component-version-change"></a>Onderdeel versie wijzigen
Er is geen wijziging van de onderdeel versie voor deze versie. In [dit document](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)vindt u de huidige versie van de onderdelen voor hdinsight 4,0 en hdinsight 3,6.