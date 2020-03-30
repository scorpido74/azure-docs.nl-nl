---
title: Apache Spark-prestaties - Azure HDInsight IO-cache (voorbeeld)
description: Meer informatie over Azure HDInsight IO-cache en hoe u deze gebruiken om de prestaties van Apache Spark te verbeteren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: 43875b87d26f144b85454077fd3c044c820132bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494982"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Prestaties van Apache Spark-workloads verbeteren met Azure HDInsight IO-cache

IO Cache is een service voor het cacheen van gegevens voor Azure HDInsight die de prestaties van Apache Spark-taken verbetert. IO Cache werkt ook met [Apache TEZ-](https://tez.apache.org/) en Apache Hive-workloads, die kunnen worden uitgevoerd op [Apache Spark-clusters.](https://spark.apache.org/) [Apache Hive](https://hive.apache.org/) IO Cache maakt gebruik van een open-source caching component genaamd RubiX. RubiX is een lokale schijfcache voor gebruik met big data analytics-engines die toegang krijgen tot gegevens van cloudopslagsystemen. RubiX is uniek onder caching-systemen, omdat het Solid-State Drives (SSD's) gebruikt in plaats van het werkgeheugen te reserveren voor caching-doeleinden. De IO-cacheservice start en beheert RubiX-metagegevensservers op elk werknemersknooppunt van het cluster. Het configureert ook alle services van het cluster voor transparant gebruik van RubiX-cache.

De meeste SSD's bieden meer dan 1 GByte per seconde bandbreedte. Deze bandbreedte, aangevuld met het besturingssysteem in-memory file cache, biedt voldoende bandbreedte om big data compute processing engines, zoals Apache Spark laden. Het werkgeheugen blijft beschikbaar voor Apache Spark om zwaar geheugenafhankelijke taken te verwerken, zoals shuffles. Met exclusief gebruik van het werkgeheugen kan Apache Spark een optimaal resourcegebruik bereiken.  

> [!Note]  
> IO Cache gebruikt Momenteel RubiX als een caching-component, maar dit kan veranderen in toekomstige versies van de service. Gebruik io-cache-interfaces en neem geen afhankelijkheden rechtstreeks van de RubiX-implementatie.
>IO-cache wordt op dit moment alleen ondersteund met Azure BLOB Storage.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Voordelen van Azure HDInsight IO-cache

Het gebruik van IO-cache biedt een prestatieverhoging voor taken die gegevens uit Azure Blob Storage lezen.

U hoeft geen wijzigingen aan te brengen in uw Spark-taken om de prestaties te zien toenemen bij het gebruik van IO-cache. Wanneer IO-cache is uitgeschakeld, leest deze Spark-code gegevens `spark.read.load('wasbs:///myfolder/data.parquet').count()`op afstand uit Azure Blob Storage: . Wanneer IO-cache is geactiveerd, zorgt dezelfde coderegel ervoor dat de cache in de cache wordt gelezen. Bij het volgende leest, worden de gegevens lokaal gelezen van SSD. Worker nodes op HDInsight cluster zijn uitgerust met lokaal aangesloten, dedicated SSD-schijven. HDInsight IO Cache gebruikt deze lokale SSD's voor caching, wat het laagste niveau van latentie biedt en de bandbreedte maximaliseert.

## <a name="getting-started"></a>Aan de slag

Azure HDInsight IO-cache is standaard gedeactiveerd in preview. IO-cache is beschikbaar op Azure HDInsight 3.6+ Spark-clusters, waarop Apache Spark 2.3 wordt uitgevoerd.  Ga als volgt te werk om IO-cache op HDInsight 4.0 te activeren:

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Selecteer de **IO-cacheservice** aan de linkerkant.

1. Selecteer **Acties** **(serviceacties** in HDI 3.6) en **Activeer**.

    ![De IO-cacheservice inschakelen in Ambari](./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png "De IO-cacheservice inschakelen in Ambari")

1. Bevestig het opnieuw starten van alle betrokken services op het cluster.

> [!NOTE]  
> Hoewel de voortgangsbalk geactiveerd wordt weergegeven, is IO-cache pas ingeschakeld als u de andere getroffen services opnieuw start.

## <a name="troubleshooting"></a>Problemen oplossen
  
Er kunnen schijfruimtefouten worden weergegeven met Spark-taken nadat u IO-cache hebt ingemaakt. Deze fouten treden op omdat Spark ook lokale schijfopslag gebruikt voor het opslaan van gegevens tijdens schuifbewerkingen. Spark kan opraken van SSD ruimte zodra IO Cache is ingeschakeld en de ruimte voor Spark-opslag wordt verminderd. De hoeveelheid ruimte die door IO Cache wordt gebruikt, bedraagt standaard de helft van de totale SSD-ruimte. Het schijfruimtegebruik voor IO-cache is configureerbaar in Ambari. Als er schijfruimtefouten worden weergegeven, vermindert u de hoeveelheid SSD-ruimte die wordt gebruikt voor IO-cache en start u de service opnieuw. Ga als volgt te werk om de ruimteset voor IO-cache te wijzigen:

1. Selecteer in Apache Ambari de **HDFS-service** aan de linkerkant.

1. Selecteer de tabbladen **Configs** en **Advanced.**

    ![HDFS-geavanceerde configuratie bewerken](./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png "HDFS-geavanceerde configuratie bewerken")

1. Scroll naar beneden en vouw het **gebied Aangepaste kernsite** uit.

1. Zoek de eigenschap **hadoop.cache.data.fullness.percentage**.

1. Wijzig de waarde in het vak.

    ![Percentage volheid van IO-cache bewerken](./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png "Percentage volheid van IO-cache bewerken")

1. Selecteer **Opslaan** rechtsboven.

1. Selecteer**Alle betrokkenen opnieuw** **starten** > .

    ![Apache Ambari herstart alle getroffen](./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png "Alle betrokkenen opnieuw starten")

1. Selecteer **Alles opnieuw starten bevestigen**.

Als dat niet werkt, schakelt u IO-cache uit.

## <a name="next-steps"></a>Volgende stappen

Lees meer over IO Cache, inclusief prestatiebenchmarks in deze blogpost: [Apache Spark-taken worden tot 9x sneller met HDInsight IO-cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
