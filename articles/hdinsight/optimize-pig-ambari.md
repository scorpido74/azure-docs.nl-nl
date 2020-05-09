---
title: Apache Pig optimaliseren met Apache Ambari in azure HDInsight
description: Gebruik de Apache Ambari-webgebruikersinterface om Apache varken te configureren en te optimaliseren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 469019eb1e90654d1953156337593d5de99b46c0
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796679"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Apache Pig optimaliseren met Apache Ambari in azure HDInsight

Apache Ambari is een webinterface voor het beheren en controleren van HDInsight-clusters. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md)voor een inleiding tot de Ambari-webgebruikersinterface.

Apache varken-eigenschappen kunnen worden gewijzigd in de Ambari-webgebruikersinterface om Pig-query's af te stemmen. Als u de Pig-eigenschappen van Ambari wijzigt, worden de Pig `/etc/pig/2.4.2.0-258.0/pig.properties` -eigenschappen in het bestand direct gewijzigd.

1. Als u de eigenschappen van varken wilt wijzigen, gaat u naar het tabblad Pig- **configuratie** en vouwt u het deel venster **Geavanceerde Pig-eigenschappen** uit.

1. Zoek, verwijder de opmerking en wijzig de waarde van de eigenschap die u wilt wijzigen.

1. Selecteer **Opslaan** in de rechter bovenhoek van het venster om de nieuwe waarde op te slaan. Voor sommige eigenschappen is het opnieuw opstarten van de service vereist.

    ![Geavanceerde eigenschappen van Apache-Pig](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Alle instellingen op sessie niveau overschrijven eigenschaps waarden in `pig.properties` het bestand.

## <a name="tune-execution-engine"></a>Uitvoerings engine afstemmen

Er zijn twee uitvoerings engines beschikbaar voor het uitvoeren van Pig-scripts: MapReduce en TEZ. TEZ is een geoptimaliseerde engine en is veel sneller dan MapReduce.

1. Als u de uitvoerings Engine wilt wijzigen, gaat u naar het deel venster **Geavanceerde Pig-eigenschappen** en gaat u naar de eigenschap `exectype`.

1. De standaard waarde is **MapReduce**. Wijzig deze in **TEZ**.

## <a name="enable-local-mode"></a>Lokale modus inschakelen

Net als bij Hive wordt de lokale modus gebruikt om taken te versnellen met relatief kleinere hoeveel heden gegevens.

1. Als u de lokale modus wilt inschakelen `pig.auto.local.enabled` , stelt u in op **waar**. De standaardwaarde is false.

1. Taken met een grootte van invoer gegevens die kleiner `pig.auto.local.input.maxbytes` zijn dan de waarde van de eigenschap, worden beschouwd als kleine taken. De standaard waarde is 1 GB.

## <a name="copy-user-jar-cache"></a>Gebruiker jar-cache kopiëren

Varken kopieert de JAR-bestanden die zijn vereist door Udf's naar een gedistribueerde cache om ze beschikbaar te maken voor taak knooppunten. Deze potten veranderen niet regel matig. Als deze `pig.user.cache.enabled` optie is ingeschakeld, kunnen potten in een cache worden geplaatst om ze opnieuw te gebruiken voor taken die door dezelfde gebruiker worden uitgevoerd. Deze instelling resulteert in een kleine toename van de taak prestaties.

1. Stel `pig.user.cache.enabled` in op waar om in te scha kelen. De standaard waarde is False.

1. Als u het basispad van de in de cache geplaatste `pig.user.cache.location` potten wilt instellen, stelt u het pad naar het basispad in. De standaardwaarde is `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Prestaties optimaliseren met geheugen instellingen

De volgende geheugen instellingen kunnen helpen de prestaties van het Pig-script te optimaliseren.

* `pig.cachedbag.memusage`: De hoeveelheid geheugen die aan een Bag is toegewezen. Een Bag is verzameling Tuples. Een tuple is een geordende set velden en een veld is een stukje gegevens. Als de gegevens in een Bag zich buiten het opgegeven geheugen bevinden, wordt deze overgelopen naar de schijf. De standaard waarde is 0,2. Dit is 20 procent van het beschik bare geheugen. Dit geheugen wordt gedeeld in alle strafpot van een toepassing.

* `pig.spill.size.threshold`: Zakken die groter zijn dan deze drempel waarde voor de grootte van overloop (in bytes) worden overgelopen naar de schijf. De standaard waarde is 5 MB.

## <a name="compress-temporary-files"></a>Tijdelijke bestanden comprimeren

Pig genereert tijdelijke bestanden tijdens het uitvoeren van taken. Het comprimeren van de tijdelijke bestanden resulteert in een prestatie verhoging bij het lezen of schrijven van bestanden naar schijf. De volgende instellingen kunnen worden gebruikt voor het comprimeren van tijdelijke bestanden.

* `pig.tmpfilecompression`: Als deze optie is ingesteld op True, wordt tijdelijke bestands compressie ingeschakeld. De standaardwaarde is false.

* `pig.tmpfilecompression.codec`: De compressie-codec die moet worden gebruikt voor het comprimeren van de tijdelijke bestanden. De aanbevolen compressie-codecs zijn LZO en Snappy voor een lager CPU-gebruik.

## <a name="enable-split-combining"></a>Splitsen combi neren inschakelen

Wanneer deze functie is ingeschakeld, worden kleine bestanden gecombineerd voor minder toewijzings taken. Deze instelling verbetert de efficiëntie van taken met veel kleine bestanden. Stel `pig.noSplitCombination` in op waar om in te scha kelen. De standaardwaarde is false.

## <a name="tune-mappers"></a>Toewijzings toewijzingen afstemmen

Het aantal mappers wordt bepaald door het wijzigen van de `pig.maxCombinedSplitSize`eigenschap. Met deze eigenschap geeft u de grootte van de gegevens die moeten worden verwerkt door één toewijzings taak. De standaard waarde is de standaard blok grootte van het bestands systeem. Het verhogen van deze waarde resulteert in een lager aantal toewijzings taken.

## <a name="tune-reducers"></a>Verkleiners afstemmen

Het aantal verminderers wordt berekend op basis van de `pig.exec.reducers.bytes.per.reducer`para meter. De para meter bepaalt het aantal bytes dat per vermindering is verwerkt, standaard 1 GB. Als u het maximum aantal verminderers wilt beperken, `pig.exec.reducers.max` stelt u de eigenschap standaard in op 999.

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Clusters optimaliseren](./hdinsight-changing-configs-via-ambari.md)
* [Apache HBase optimaliseren](./optimize-hbase-ambari.md)
* [Apache Hive optimaliseren](./optimize-hive-ambari.md)
