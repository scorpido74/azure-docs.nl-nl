---
title: Ondersteuning voor opensourcesoftware in Azure HDInsight
description: Microsoft Azure biedt een algemeen ondersteuningsniveau voor open-sourcetechnologieën.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: f93be73af4bbbd159ffc01804617892251d96347
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772169"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Ondersteuning voor opensourcesoftware in Azure HDInsight

De Microsoft Azure HDInsight-service maakt gebruik van een omgeving van open-source technologieën die zijn gevormd rond Apache Hadoop. Microsoft Azure biedt een algemeen ondersteuningsniveau voor open-sourcetechnologieën. Zie het gedeelte **Ondersteuningsbereik** van [veelgestelde vragen](https://azure.microsoft.com/support/faq/)over het bereik van Azure Support voor meer informatie. De HDInsight-service biedt een extra ondersteuningsniveau voor ingebouwde componenten.

## <a name="components"></a>Onderdelen

Er zijn twee soorten opensourcecomponenten beschikbaar in de HDInsight-service:

### <a name="built-in-components"></a>Ingebouwde componenten

Deze componenten zijn vooraf geïnstalleerd op HDInsight-clusters en bieden kernfunctionaliteit van het cluster. De volgende onderdelen behoren tot deze categorie:

* [Apache Hadoop GAREN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager.
* De Hive-querytaal [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).
* [Apache Mahout.](https://mahout.apache.org/)

Een volledige lijst van clustercomponenten is beschikbaar in [Wat zijn de Apache Hadoop componenten en versies beschikbaar met HDInsight?](hdinsight-component-versioning.md)

### <a name="custom-components"></a>Aangepaste componenten

Als gebruiker van het cluster u elk onderdeel dat beschikbaar is in de community of door u is gemaakt, in uw werkbelasting installeren of gebruiken.

> [!WARNING]  
> Onderdelen die bij het HDInsight-cluster worden geleverd, worden volledig ondersteund. Microsoft Support helpt bij het isoleren en oplossen van problemen met betrekking tot deze onderdelen.
>
> Aangepaste componenten ontvangen commercieel redelijke ondersteuning om u te helpen het probleem verder op te lossen. Microsoft Support kan het probleem mogelijk oplossen. Of ze kunnen u vragen om beschikbare kanalen in te schakelen voor de open-source technologieën waar diepgaande expertise voor die technologie wordt gevonden. Veel community sites kunnen worden gebruikt. Voorbeelden zijn [MSDN forum voor HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight) en [Stack Overflow](https://stackoverflow.com).
>
> Apache projecten hebben ook project sites op de [Apache website](https://apache.org). Een voorbeeld is [Hadoop.](https://hadoop.apache.org/)

## <a name="component-usage"></a>Componentgebruik

De HDInsight-service biedt verschillende manieren om aangepaste componenten te gebruiken. Hetzelfde ondersteuningsniveau is van toepassing, ongeacht hoe een component wordt gebruikt of geïnstalleerd op het cluster. In de volgende tabel worden de meest voorkomende manieren beschreven waarop aangepaste componenten worden gebruikt op HDInsight-clusters:

|Gebruik |Beschrijving |
|---|---|
|Vacature|Hadoop of andere typen taken die aangepaste componenten uitvoeren of gebruiken, kunnen in het cluster worden ingediend.|
|Clusteraanpassing|Tijdens het maken van het cluster u aanvullende instellingen en aangepaste componenten opgeven die op de clusterknooppunten zijn geïnstalleerd.|
|Voorbeelden|Voor populaire aangepaste componenten kunnen Microsoft en anderen voorbeelden geven van hoe deze componenten kunnen worden gebruikt op HDInsight-clusters. Deze monsters worden geleverd zonder ondersteuning.|

## <a name="next-steps"></a>Volgende stappen

* [Azure HDInsight-clusters aanpassen met behulp van scriptacties](./hdinsight-hadoop-customize-cluster-linux.md)
* [Scriptactiescripts ontwikkelen voor HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Een Python-omgeving veilig beheren in Azure HDInsight met scriptactie](./spark/apache-spark-python-package-installation.md)
