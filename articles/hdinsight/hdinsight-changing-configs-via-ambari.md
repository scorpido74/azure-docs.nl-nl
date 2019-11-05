---
title: Apache Ambari voor het optimaliseren van cluster configuraties-Azure HDInsight
description: Gebruik de Apache Ambari Web-UI om Azure HDInsight-clusters te configureren en te optimaliseren.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: e0d94a41febdba1bea6818309e05d287bef6d3a1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492514"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Apache Ambari gebruiken om configuraties van HDInsight-clusters te optimaliseren

HDInsight biedt [Apache Hadoop](https://hadoop.apache.org/) clusters voor grootschalige toepassingen voor gegevens verwerking. Het beheren, bewaken en optimaliseren van deze complexe clusters met meerdere knoop punten kan lastig zijn. [Apache Ambari](https://ambari.apache.org/) is een webinterface voor het beheren en controleren van HDInsight Linux-clusters.  Voor Windows-clusters gebruikt u de [Ambari-rest API](hdinsight-hadoop-manage-ambari-rest-api.md).

Zie [HDInsight-clusters beheren met behulp van de Apache Ambari-webgebruikersinterface](hdinsight-hadoop-manage-ambari.md) voor een inleiding tot het gebruik van de Ambari-webgebruikersinterface.

Meld u bij `https://CLUSTERNAME.azurehdidnsight.net` aan bij Ambari met uw cluster referenties. In het eerste scherm wordt een overzichts dashboard weer gegeven.

![Apache Ambari-gebruikers dashboard weer gegeven](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

De Ambari-webgebruikersinterface kan worden gebruikt voor het beheren van hosts, services, waarschuwingen, configuraties en weer gaven. Ambari kan niet worden gebruikt voor het maken van een HDInsight-cluster, het upgraden van services, het beheren van stacks en versies, het buiten gebruik stellen of de hergebruik van hosts of het toevoegen van services aan het cluster.

## <a name="manage-your-clusters-configuration"></a>De configuratie van uw cluster beheren

Met configuratie-instellingen kunt u een bepaalde service afstemmen. Als u de configuratie-instellingen van een service wilt wijzigen, selecteert u de service in de zijbalk van **Services** (aan de linkerkant) en navigeert u vervolgens naar het tabblad **configuratie** van de detail pagina van de service.

![Terzijde voor Apache Ambari Services](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Grootte van NameNode Java-heap wijzigen

De grootte van de NameNode Java-heap is afhankelijk van veel factoren, zoals de belasting van het cluster, het aantal bestanden en het aantal blokken. De standaard grootte van 1 GB werkt goed voor de meeste clusters, maar sommige werk belastingen kunnen meer of minder geheugen vereisen.

De grootte van de NameNode Java-heap wijzigen:

1. Selecteer **HDFS** in de services-zijbalk en navigeer naar het tabblad **configuratie** .

    ![Apache Ambari HDFS-configuratie](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Zoek de instelling **NameNode Java Heap-grootte**. U kunt ook het tekstvak **filteren** gebruiken om een bepaalde instelling te typen en te zoeken. Selecteer het pictogram met de **pen** naast de naam van de instelling.

    ![Ambari NameNode Java-Heap-grootte](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Typ de nieuwe waarde in het tekstvak en druk vervolgens op **Enter** om de wijziging op te slaan.

    ![Ambari bewerken NameNode Java-heap size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. De grootte van de NameNode Java-heap is gewijzigd van 1 GB van 2 GB.

    ![Bewerkte NameNode Java-heap size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Sla uw wijzigingen op door te klikken op de groene knop **Opslaan** boven aan het configuratie scherm.

    ![Ambari Ambari-configuraties opslaan](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive optimalisatie

In de volgende secties worden de configuratie opties beschreven voor het optimaliseren van algemene Apache Hive prestaties.

1. Als u Hive-configuratie parameters wilt wijzigen, selecteert u **Hive** van de services-zijbalk.
1. Navigeer naar het tabblad **configuratie** .

### <a name="set-the-hive-execution-engine"></a>De engine voor het uitvoeren van de Hive instellen

Hive bevat twee uitvoerings engines: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) en [Apache TEZ](https://tez.apache.org/). TEZ is sneller dan MapReduce. HDInsight Linux-clusters hebben TEZ als de standaard uitvoerings engine. De uitvoerings engine wijzigen:

1. Typ **Execution Engine** in het vak Filter op het tabblad **configuratie** van Hive.

    ![Engine voor het uitvoeren van Apache Ambari Search](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. De standaard waarde van de **optimalisatie** -eigenschap is **TEZ**.

    ![Optimalisatie-Apache TEZ-engine](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Toewijzings toewijzingen afstemmen

Hadoop probeert één bestand te splitsen (*toewijzen*) aan meerdere bestanden en de resulterende bestanden parallel te verwerken. Het aantal mappers is afhankelijk van het aantal splitsingen. Met de volgende twee configuratie parameters wordt het aantal splitsingen voor de TEZ-uitvoerings engine gebrand:

* `tez.grouping.min-size`: de onderste limiet voor de grootte van een gegroepeerde splitsing, met een standaard waarde van 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: de bovengrens voor de grootte van een gegroepeerde splitsing, met een standaard waarde van 1 GB (1.073.741.824 bytes).

Als prestatie regel voor de vuist verlaagt u beide para meters om de latentie te verbeteren, verg root u voor meer door voer.

Als u bijvoorbeeld vier toewijzings taken wilt instellen voor een gegevens grootte van 128 MB, stelt u beide para meters in op 32 MB elke (33.554.432 bytes).

1. Als u de limiet parameters wilt wijzigen, gaat u naar het tabblad **configuratie** van de TEZ-service. Vouw het deel venster **Algemeen** uit en zoek de para meters `tez.grouping.max-size` en `tez.grouping.min-size`.

1. Stel beide para meters in op **33.554.432** bytes (32 MB).

    ![Ambari TEZ-groeperings grootte van Apache](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Deze wijzigingen zijn van invloed op alle TEZ-taken op de server. Kies de juiste parameter waarden om optimaal resultaat te krijgen.

### <a name="tune-reducers"></a>Verkleiners afstemmen

[Apache Orc](https://orc.apache.org/) en [Snappy](https://google.github.io/snappy/) bieden hoge prestaties. Hive kan echter te weinig vertragers hebben, waardoor er knel punten ontstaan.

Stel, u hebt een grootte van 50 GB voor de invoer gegevens. De gegevens in de ORC-indeling met Snappy-compressie is 1 GB. Onderdeel schat het aantal verminderers dat nodig is als: (aantal bytes invoer voor mappers/`hive.exec.reducers.bytes.per.reducer`).

Met de standaard instellingen is dit voor beeld 4 verminderingen.

De para meter `hive.exec.reducers.bytes.per.reducer` bepaalt het aantal verwerkte bytes per verminderr. De standaard waarde is 64 MB. Het afstemmen van deze waarde is groter, en kan de prestaties verbeteren. Als u het apparaat te laag afstemt, kunnen er ook te veel vertraagden worden geproduceerd, waardoor de prestaties mogelijk nadelig worden beïnvloed. Deze para meter is gebaseerd op uw specifieke gegevens vereisten, compressie-instellingen en andere omgevings factoren.

1. Als u de para meter wilt wijzigen, gaat u naar het tabblad **configuratie** van Hive en zoekt u de para meter **gegevens per verkorter** op de pagina instellingen.

    ![Apache Ambari-gegevens per Reductier](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Selecteer **bewerken** om de waarde te wijzigen in 128 MB (134.217.728 bytes) en druk vervolgens op **Enter** om op te slaan.

    ![Ambari-gegevens per reductie bewerking](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Op basis van een invoer grootte van 1.024 MB, met 128 MB aan gegevens per reduceerere, zijn er 8 verminderers (1024/128).

1. Een onjuiste waarde voor de **gegevens per reduceerere** para meter kan leiden tot een groot aantal verminderers, wat een negatieve invloed heeft op de prestaties van query's. Als u het maximum aantal verminderers wilt beperken, stelt u `hive.exec.reducers.max` in op een geschikte waarde. De standaard waarde is 1009.

### <a name="enable-parallel-execution"></a>Parallelle uitvoering inschakelen

Een Hive-query wordt uitgevoerd in een of meer fasen. Als de onafhankelijke fasen parallel kunnen worden uitgevoerd, worden de query prestaties verbeterd.

1. Als u parallelle query uitvoering wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en zoekt u naar de eigenschap `hive.exec.parallel`. De standaard waarde is False. Wijzig de waarde in True en druk vervolgens op **Enter** om de waarde op te slaan.

1. Als u het aantal taken dat parallel moet worden uitgevoerd, wilt beperken, wijzigt u de eigenschap `hive.exec.parallel.thread.number`. De standaard waarde is 8.

    ![Parallelle weer gave Apache Hive exec](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Vectorization inschakelen

Hive verwerkt gegevens rijen per rij. Vectorization-doorstuur component voor het verwerken van gegevens in blokken van 1.024 rijen in plaats van één rij tegelijk. Vectorization is alleen van toepassing op de ORC-bestands indeling.

1. Als u een vector query-uitvoering wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en zoekt u naar de para meter `hive.vectorized.execution.enabled`. De standaard waarde is True voor Hive 0.13.0 of hoger.

1. Als u een vector uitvoering wilt inschakelen voor de reductie van de query, stelt u de para meter `hive.vectorized.execution.reduce.enabled` in op True. De standaard waarde is False.

    ![Apache Hive vector uitvoering](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Optimalisatie op basis van kosten inschakelen (CBO)

Hive volgt standaard een set regels om één optimaal query-uitvoerings plan te vinden. Met CBO (cost-based Optimization) worden meerdere plannen geëvalueerd voor het uitvoeren van een query en worden er kosten aan elk plan toegewezen. vervolgens wordt het goedkoopste-plan bepaald voor het uitvoeren van een query.

Om CBO in te scha kelen, gaat u naar het tabblad **configuratie** van Hive en zoekt u naar `parameter hive.cbo.enable`. vervolgens schakelt u de wissel knop in **op**aan.

![Optimalisatie op basis van HDInsight-kosten](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

De volgende aanvullende configuratie parameters verhogen Hive-query prestaties wanneer CBO is ingeschakeld:

* `hive.compute.query.using.stats`

    Als deze eigenschap is ingesteld op True, gebruikt Hive statistieken die zijn opgeslagen in de meta Store om eenvoudige query's te beantwoorden, zoals `count(*)`.

    ![Reken query Apache Hive met behulp van statistieken](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolom statistieken worden gemaakt wanneer CBO is ingeschakeld. Hive maakt gebruik van kolom statistieken, die zijn opgeslagen in de meta Store, voor het optimaliseren van query's. Het ophalen van kolom statistieken voor elke kolom duurt langer wanneer het aantal kolommen hoog is. Als deze instelling is ingesteld op False, wordt het ophalen van kolom statistieken uit de meta Store uitgeschakeld.

    ![Apache Hive stats kolom statistieken instellen](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Standaard partitie statistieken, zoals het aantal rijen, de grootte van de gegevens en de bestands grootte, worden opgeslagen in de meta Store. Als deze eigenschap is ingesteld op True, worden de partitie statistieken opgehaald uit de meta Store. Als false is ingesteld, wordt de bestands grootte opgehaald uit het bestands systeem en wordt het aantal rijen opgehaald uit het schema van de rij.

    ![Statistieken voor de Hive-partitie instellen](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Tussenliggende compressie inschakelen

Met toewijzings taken maakt u tussenliggende bestanden die worden gebruikt door de verkorter taken. Tussenliggende compressie verkleint de tussenliggende bestands grootte.

Hadoop-taken zijn meestal I/O-knel punten. Het comprimeren van gegevens kan I/O en de algehele netwerk overdracht versnellen.

De beschik bare compressie typen zijn:

| Indeling | Hulpprogramma | Ring | Bestands extensie | Splitsbaar? |
| -- | -- | -- | -- | -- |
| Gzip | Gzip | DEFLATE | . gz | Nee |
| Bzip2 | Bzip2 | Bzip2 |.bz2 | Ja |
| LZO | Lzop | LZO | .lzo | Ja, indien geïndexeerd |
| Snappy | N.v.t. | Snappy | Snappy | Nee |

Een algemene regel is dat de compressie methode splitsbaar belang rijk is, anders worden enkele toewijzingen gemaakt. Als de invoer gegevens tekst zijn, is `bzip2` de beste optie. Voor de indeling ORC is Snappy de snelste compressie optie.

1. Als u tussenliggende compressie wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en stelt u de para meter `hive.exec.compress.intermediate` in op True. De standaard waarde is False.

    ![Component exec-compressie tussen onderdelen](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Als u tussenliggende bestanden wilt comprimeren, kiest u een compressie-codec met lagere CPU-kosten, zelfs als de codec geen hoge compressie-uitvoer heeft.

1. Als u de tussenliggende compressie-codec wilt instellen, voegt u de aangepaste eigenschap `mapred.map.output.compression.codec` toe aan het bestand `hive-site.xml` of `mapred-site.xml`.

1. Een aangepaste instelling toevoegen:

    a. Ga naar het tabblad **configuratie** van Hive en selecteer het tabblad **Geavanceerd** .

    b. Ga naar het tabblad **Geavanceerd** en vouw het deel venster **aangepaste Hive-site** uit.

    c. Klik op de **eigenschap koppeling toevoegen** onder aan het deel venster aangepaste Hive-site.

    d. Voer in het venster Eigenschappen toevoegen `mapred.map.output.compression.codec` in als de sleutel en `org.apache.hadoop.io.compress.SnappyCodec` als de waarde.

    e. Klik op **Toevoegen**.

    ![Aangepaste eigenschappen Apache Hive toevoegen](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Hiermee wordt het tussenliggende bestand gecomprimeerd met Snappy-compressie. Zodra de eigenschap is toegevoegd, wordt deze weer gegeven in het deel venster aangepaste Hive-site.

    > [!NOTE]  
    > Met deze procedure wordt het `$HADOOP_HOME/conf/hive-site.xml` bestand gewijzigd.

### <a name="compress-final-output"></a>Laatste uitvoer comprimeren

De uiteindelijke Hive-uitvoer kan ook worden gecomprimeerd.

1. Als u de laatste Hive-uitvoer wilt comprimeren, gaat u naar het tabblad **configuratie** van Hive en stelt u de para meter `hive.exec.compress.output` in op True. De standaard waarde is False.

1. Als u de compressie-codec voor uitvoer wilt kiezen, voegt u de aangepaste eigenschap `mapred.output.compression.codec` toe aan het deel venster aangepaste Hive-site, zoals wordt beschreven in stap 3 van de vorige sectie.

    ![Aangepaste eigenschap ADD2 Apache Hive](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Speculatieve uitvoering inschakelen

Met een speculatieve uitvoering wordt een bepaald aantal dubbele taken gestart om het langzame taak traceren te detecteren en op te lossen, terwijl de algehele taak wordt uitgevoerd door afzonderlijke taak resultaten te optimaliseren.

Speculatieve uitvoering moet niet worden ingeschakeld voor langlopende MapReduce-taken met grote hoeveel heden invoer.

* Als u speculatieve uitvoering wilt inschakelen, gaat u naar het tabblad **configuratie** van Hive en stelt u de para meter `hive.mapred.reduce.tasks.speculative.execution` in op True. De standaard waarde is False.

    ![Hive-mapred beperken taken speculatieve uitvoering](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dynamische partities afstemmen

Hive maakt het mogelijk om dynamische partities te maken bij het invoegen van records in een tabel, zonder dat elke partitie vooraf wordt gedefinieerd. Dit is een krachtige functie, hoewel dit kan leiden tot het maken van een groot aantal partities en een groot aantal bestanden voor elke partitie.

1. Als u wilt dat Hive dynamische partities doet, moet de `hive.exec.dynamic.partition` parameter waarde True zijn (de standaard instelling).

1. Wijzig de dynamische partitie modus in *strict*. In de strikte modus moet ten minste één partitie statisch zijn. Zo wordt voor komen dat query's zonder het partitie filter in de component WHERE, dat wil zeggen, *strikte* query's die alle partities scannen, worden voor komen. Ga naar het tabblad **configuratie** van Hive en stel `hive.exec.dynamic.partition.mode` in op **strikt**. De standaard waarde is niet **strikt**.

1. Wijzig de para meter `hive.exec.max.dynamic.partitions` om het aantal dynamische partities te beperken dat moet worden gemaakt. De standaard waarde is 5000.

1. Als u het totale aantal dynamische partities per knoop punt wilt beperken, wijzigt u `hive.exec.max.dynamic.partitions.pernode`. De standaard waarde is 2000.

### <a name="enable-local-mode"></a>Lokale modus inschakelen

Met de lokale modus kan Hive alle taken van een taak uitvoeren op één computer of soms in één proces. Dit verbetert de query prestaties als de invoer gegevens klein zijn en de overhead van het starten van taken voor query's een aanzienlijk percentage van de algehele query uitvoering verbruikt.

Als u de lokale modus wilt inschakelen, voegt u de para meter `hive.exec.mode.local.auto` toe aan de aangepaste Hive-site, zoals wordt beschreven in stap 3 van de sectie [tussenliggende compressie inschakelen](#enable-intermediate-compression) .

![Modus voor Apache Hive uitvoering lokale automatisch](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Eén MapReduce instellen op meerdere groepen

Als deze eigenschap is ingesteld op True, genereert een query met meerdere groepen met algemene groep-op sleutels één MapReduce-taak.  

Als u dit gedrag wilt inschakelen, voegt u de para meter `hive.multigroupby.singlereducer` toe aan het deel venster aangepaste Hive-site, zoals wordt beschreven in stap 3 van de sectie [tussenliggende compressie inschakelen](#enable-intermediate-compression) .

![Hive-set met één MapReduce meerdere groepen op](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Aanvullende Hive-optimalisaties

In de volgende secties worden extra optimalisaties voor onderdelen beschreven die u kunt instellen.

#### <a name="join-optimizations"></a>Deelname aan optimalisaties

Het standaard type voor samen voegen in Hive is een *wille keurige samen voeging*. In Hive leest speciale mappers de invoer en wordt een koppelings sleutel/-waardepaar naar een tussenliggend bestand verzenden. Hadoop sorteert en voegt deze paren samen in een wille keurige fase. Deze fase in wille keurige volg orde is duur. Het selecteren van de juiste koppeling op basis van uw gegevens kan de prestaties aanzienlijk verbeteren.

| Type samen voeging | Als | Werking | Hive-instellingen | Opmerkingen |
| -- | -- | -- | -- | -- |
| In wille keurige volg orde | <ul><li>Standaard keuze</li><li>Werkt altijd</li></ul> | <ul><li>Lees bewerkingen van een deel van een van de tabellen</li><li>Buckets en sorteren op koppelings sleutel</li><li>Hiermee wordt één Bucket naar elke reductie verzonden</li><li>De deelname aan de minder kant wordt uitgevoerd</li></ul> | Er is geen belang rijke Hive-instelling vereist | Werkt elke keer |
| Kaart toevoegen | <ul><li>Een tabel kan in het geheugen passen</li></ul> | <ul><li>Hiermee wordt een kleine tabel in de hash-tabel van het geheugen gelezen</li><li>Streamt via een deel van het grote bestand</li><li>Koppelt elke record uit een hash-tabel</li><li>Samen voegingen zijn alleen door de Mapper</li></ul> | `hive.auto.confvert.join=true` | Zeer snel, maar beperkt |
| Bucket voor samen voegen sorteren | Als beide tabellen zijn: <ul><li>Op dezelfde manier gesorteerd</li><li>Hetzelfde gebuckd</li><li>Deel nemen aan de gesorteerde/gebuckeerde kolom</li></ul> | Elk proces: <ul><li>Hiermee wordt een Bucket uit elke tabel gelezen</li><li>De rij met de laagste waarde verwerkt</li></ul> | `hive.auto.convert.sortmerge.join=true` | Zeer efficiënt |

#### <a name="execution-engine-optimizations"></a>Optimalisaties van uitvoerings engine

Aanvullende aanbevelingen voor het optimaliseren van de engine voor het uitvoeren van de Hive:

| Instelling | Aanbevolen | Standaard HDInsight |
| -- | -- | -- |
| `hive.mapjoin.hybridgrace.hashtable` | Waar = veiliger, langzamer; ONWAAR = sneller | onwaar |
| `tez.am.resource.memory.mb` | 4-GB bovengrens voor de meeste | Automatisch afgestemd |
| `tez.session.am.dag.submit.timeout.secs` | 300 + | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000 + | 10.000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000 + | 20000 |

## <a name="apache-pig-optimization"></a>Naoptimalisatie van Apache-varkens

[Apache varken](https://pig.apache.org/) -eigenschappen kunnen worden gewijzigd in de Ambari-webgebruikersinterface om Pig-query's af te stemmen. Als u de Pig-eigenschappen van Ambari wijzigt, worden de Pig-eigenschappen in het `/etc/pig/2.4.2.0-258.0/pig.properties` bestand direct gewijzigd.

1. Als u de eigenschappen van varken wilt wijzigen, gaat u naar het tabblad Pig- **configuratie** en vouwt u het deel venster **Geavanceerde Pig-eigenschappen** uit.

1. Zoek, verwijder de opmerking en wijzig de waarde van de eigenschap die u wilt wijzigen.

1. Selecteer **Opslaan** in de rechter bovenhoek van het venster om de nieuwe waarde op te slaan. Voor sommige eigenschappen is het opnieuw opstarten van de service vereist.

    ![Geavanceerde eigenschappen van Apache-Pig](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Voor alle instellingen op sessie niveau worden de eigenschapwaarden in het `pig.properties` bestand overschreven.

### <a name="tune-execution-engine"></a>Uitvoerings engine afstemmen

Er zijn twee uitvoerings engines beschikbaar voor het uitvoeren van Pig-scripts: MapReduce en TEZ. TEZ is een geoptimaliseerde engine en is veel sneller dan MapReduce.

1. Als u de uitvoerings Engine wilt wijzigen, gaat u in het deel venster **Geavanceerde Pig-eigenschappen** naar de eigenschap `exectype`.

1. De standaard waarde is **MapReduce**. Wijzig deze in **TEZ**.

### <a name="enable-local-mode"></a>Lokale modus inschakelen

Net als bij Hive wordt de lokale modus gebruikt om taken te versnellen met relatief kleinere hoeveel heden gegevens.

1. Als u de lokale modus wilt inschakelen, stelt u `pig.auto.local.enabled` in op **waar**. De standaard waarde is False.

1. Taken met een invoer gegevens die kleiner zijn dan de waarde van de `pig.auto.local.input.maxbytes` eigenschap, worden beschouwd als kleine taken. De standaard waarde is 1 GB.

### <a name="copy-user-jar-cache"></a>Gebruiker jar-cache kopiëren

Varken kopieert de JAR-bestanden die zijn vereist door Udf's naar een gedistribueerde cache om ze beschikbaar te maken voor taak knooppunten. Deze potten veranderen niet regel matig. Als deze optie `pig.user.cache.enabled` is ingeschakeld, kunnen potten in een cache worden geplaatst om ze opnieuw te gebruiken voor taken die door dezelfde gebruiker worden uitgevoerd. Dit resulteert in een kleine toename van de taak prestaties.

1. Stel `pig.user.cache.enabled` in op True om dit in te scha kelen. De standaard waarde is False.

1. Als u het basispad van de in de cache opgeslagen potten wilt instellen, stelt u `pig.user.cache.location` in op het basispad. De standaardwaarde is `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Prestaties optimaliseren met geheugen instellingen

De volgende geheugen instellingen kunnen helpen de prestaties van het Pig-script te optimaliseren.

* `pig.cachedbag.memusage`: de hoeveelheid geheugen die aan een Bag is toegewezen. Een Bag is verzameling Tuples. Een tuple is een geordende set velden en een veld is een stukje gegevens. Als de gegevens in een Bag zich buiten het toegewezen geheugen bevindt, wordt deze overgelopen op schijf. De standaard waarde is 0,2. Dit is 20 procent van het beschik bare geheugen. Dit geheugen wordt gedeeld in alle strafpot van een toepassing.

* `pig.spill.size.threshold`: zakken die groter zijn dan deze drempel waarde voor de grootte van overloop (in bytes) worden overgelopen naar de schijf. De standaard waarde is 5 MB.

### <a name="compress-temporary-files"></a>Tijdelijke bestanden comprimeren

Pig genereert tijdelijke bestanden tijdens het uitvoeren van taken. Het comprimeren van de tijdelijke bestanden resulteert in een prestatie verhoging bij het lezen of schrijven van bestanden naar schijf. De volgende instellingen kunnen worden gebruikt voor het comprimeren van tijdelijke bestanden.

* `pig.tmpfilecompression`: als deze optie is ingesteld op True, wordt tijdelijke bestands compressie ingeschakeld. De standaard waarde is False.

* `pig.tmpfilecompression.codec`: de compressie-codec die moet worden gebruikt voor het comprimeren van de tijdelijke bestanden. De aanbevolen compressie-codecs zijn [lzo](https://www.oberhumer.com/opensource/lzo/) en Snappy voor het lagere CPU-gebruik.

### <a name="enable-split-combining"></a>Splitsen combi neren inschakelen

Wanneer deze functie is ingeschakeld, worden kleine bestanden gecombineerd voor minder toewijzings taken. Dit verbetert de efficiëntie van taken met veel kleine bestanden. Stel `pig.noSplitCombination` in op True om dit in te scha kelen. De standaard waarde is False.

### <a name="tune-mappers"></a>Toewijzings toewijzingen afstemmen

Het aantal mappers wordt bepaald door het wijzigen van de eigenschap `pig.maxCombinedSplitSize`. Hiermee geeft u de grootte van de gegevens die moeten worden verwerkt door een enkele toewijzings taak. De standaard waarde is de standaard blok grootte van het bestands systeem. Het verhogen van deze waarde resulteert in een afname van het aantal toewijzings taken.

### <a name="tune-reducers"></a>Verkleiners afstemmen

Het aantal verminderers wordt berekend op basis van de para meter `pig.exec.reducers.bytes.per.reducer`. De para meter bepaalt het aantal bytes dat per vermindering is verwerkt, standaard 1 GB. Als u het maximum aantal verminderers wilt beperken, stelt u de eigenschap `pig.exec.reducers.max` standaard in op 999.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase Optimization with the Ambari Web UI

De [Apache HBase](https://hbase.apache.org/) -configuratie wordt gewijzigd op het tabblad **HBase configs** . In de volgende secties worden enkele belang rijke configuratie-instellingen beschreven die van invloed zijn op de prestaties van HBase.

### <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE instellen

De HBase-Heap-grootte geeft de maximale hoeveelheid heap op die in mega bytes per *regio* en op de *hoofd* servers moet worden gebruikt. De standaard waarde is 1.000 MB. Dit moet worden afgestemd op de werk belasting van het cluster.

1. Als u wilt wijzigen, gaat u naar het deel venster **Geavanceerd HBase-env** op het tabblad HBase **configuratie** en zoekt u de instelling van de `HBASE_HEAPSIZE`.

1. Wijzig de standaard waarde in 5.000 MB.

    ![Apache Ambari HBase-geheugen heapsize](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Lees zware workloads optimaliseren

De volgende configuraties zijn belang rijk voor het verbeteren van de prestaties van lees-en zware werk belastingen.

#### <a name="block-cache-size"></a>Grootte van blok cache

De blok cache is de Lees cache. De grootte wordt bepaald door de para meter `hfile.block.cache.size`. De standaard waarde is 0,4, 40 procent van het totale server geheugen van de regio. Hoe groter de grootte van de blok cache, hoe sneller de wille keurige Lees bewerkingen worden uitgevoerd.

1. Als u deze para meter wilt wijzigen, gaat u naar het tabblad **instellingen** op het tabblad HBase- **configuratie** en zoekt u vervolgens **% van RegionServer die zijn toegewezen aan lees buffers**.

    ![HBase geheugen blok cache grootte Apache](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Als u de waarde wilt wijzigen, selecteert u het **bewerkings** pictogram.

#### <a name="memstore-size"></a>Grootte van geheugen opslag

Alle bewerkingen worden opgeslagen in de geheugen buffer, een *geheugen opslag*genoemd. Dit verhoogt de totale hoeveelheid gegevens die in één bewerking naar de schijf kan worden geschreven en versnelt de volgende toegang tot de recente bewerkingen. De grootte van de geheugen opslag wordt gedefinieerd door de volgende twee para meters:

* `hbase.regionserver.global.memstore.UpperLimit`: definieert het maximum percentage van de regio server die door geheugen opslag gecombineerd kunnen worden gebruikt.

* `hbase.regionserver.global.memstore.LowerLimit`: definieert het minimale percentage van de regio server die door geheugen opslag gecombineerd kan worden gebruikt.

Als u wilt optimaliseren voor wille keurige Lees bewerkingen, kunt u de geheugen opslag boven-en ondergrenzen verlagen.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Aantal opgehaalde rijen bij scannen vanaf schijf

De instelling `hbase.client.scanner.caching` definieert het aantal rijen dat moet worden gelezen van de schijf wanneer de `next` methode wordt aangeroepen op een scanner.  De standaardwaarde is 100. Hoe hoger het aantal, hoe minder de externe aanroepen van de client naar de regio server, wat resulteert in snellere scans. Hierdoor wordt er echter ook meer geheugen belasting op de client.

![Apache HBase aantal rijen opgehaald](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Stel de waarde zo in dat de tijd tussen het aanroepen van de volgende methode op een scanner groter is dan de time-out van de scanner. De duur van de time-out voor de scanner wordt gedefinieerd door de eigenschap `hbase.regionserver.lease.period`.

### <a name="optimize-write-heavy-workloads"></a>Schrijf zware workloads optimaliseren

De volgende configuraties zijn belang rijk voor het verbeteren van de prestaties van write-zware workloads.

#### <a name="maximum-region-file-size"></a>Maximale bestands grootte van regio

HBase slaat gegevens op in een interne bestands indeling met de naam *HFile*. De eigenschap `hbase.hregion.max.filesize` definieert de grootte van één HFile voor een regio.  Een regio wordt in twee regio's gesplitst als de som van alle HFiles in een regio groter is dan deze instelling.

![Apache HBase HRegion Max bestands grootte](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Hoe groter de grootte van het regio bestand, hoe kleiner het aantal splitsingen is. U kunt de bestands grootte verg Roten om een waarde te bepalen die resulteert in de maximale schrijf prestaties.

#### <a name="avoid-update-blocking"></a>Voor komen dat updates worden geblokkeerd

* De eigenschap `hbase.hregion.memstore.flush.size` definieert de grootte waarmee geheugen opslag wordt leeg gemaakt op de schijf. De standaard grootte is 128 MB.

* De Hbase regio Block multiplier wordt gedefinieerd door `hbase.hregion.memstore.block.multiplier`. De standaardwaarde is 4. Het Maxi maal toegestane aantal is 8.

* HBase blokkeert updates als de geheugen opslag (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) bytes is.

    Met de standaard waarden voor flush grootte en blok-multiplier worden updates geblokkeerd wanneer de geheugen opslag 128 * 4 = 512 MB groot is. Verhoog de waarde van `hbase.hregion.memstore.block.multiplier`om het aantal blokkerende updates te verminderen.

![Apache HBase regio Block multiplier](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Grootte van geheugen opslag definiëren

De grootte van de geheugen opslag wordt gedefinieerd door de para meters `hbase.regionserver.global.memstore.UpperLimit` en `hbase.regionserver.global.memstore.LowerLimit`. Als u deze waarden instelt op elkaar, vermindert het aantal pauzes tijdens het schrijven (dit leidt ook tot meer frequente leegmaak acties) en resulteert dit in betere schrijf prestaties.

### <a name="set-memstore-local-allocation-buffer"></a>Lokale toewijzings buffer voor geheugen opslag instellen

Het gebruik van de geheugen opslag lokale toewijzings buffer wordt bepaald door de eigenschap `hbase.hregion.memstore.mslab.enabled`. Wanneer deze optie is ingeschakeld (waar), voor komt u dat heap-fragmentatie tijdens een zware schrijf bewerking. De standaard waarde is True.

![hbase. hregion. geheugen opslag. mslab. enabled](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de Web-UI van Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
