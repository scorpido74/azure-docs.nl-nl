---
title: Apache Ambari voor het optimaliseren van clusterconfiguraties - Azure HDInsight
description: Gebruik de gebruikersinterface van Apache Ambari om Azure HDInsight-clusters te configureren en te optimaliseren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/16/2020
ms.openlocfilehash: c88882175ff256300dee486e680a9b63e9a65c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532495"
---
# <a name="use-apache-ambari-to-optimize-hdinsight-cluster-configurations"></a>Apache Ambari gebruiken om de configuratie van HDInsight-clusters te optimaliseren

HDInsight biedt [Apache Hadoop-clusters](./hadoop/apache-hadoop-introduction.md) voor grootschalige gegevensverwerkingstoepassingen. Het beheren, bewaken en optimaliseren van deze complexe multi-node clusters kan een uitdaging zijn. [Apache Ambari](https://ambari.apache.org/) is een webinterface voor het beheren en monitoren van HDInsight Linux clusters.  Voor Windows-clusters gebruikt u de [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md).

Zie [HDInsight-clusters beheren met behulp van de Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md) voor een inleiding tot het gebruik van de Ambari Web UI

Meld u aan bij `https://CLUSTERNAME.azurehdidnsight.net` Ambari bij uw clusterreferenties. Het eerste scherm toont een overzichtsdashboard.

![Apache Ambari-gebruikersdashboard weergegeven](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

De Ambari-webgebruikersinterface wordt gebruikt voor het beheren van hosts, services, waarschuwingen, configuraties en weergaven. Ambari kan niet worden gebruikt om een HDInsight-cluster te maken of services te upgraden. U ook geen stapels en versies beheren, hosts buiten gebruik stellen of opnieuw in bedrijf stellen of services aan het cluster toevoegen.

## <a name="manage-your-clusters-configuration"></a>De configuratie van uw cluster beheren

Configuratie-instellingen helpen bij het afstemmen van een bepaalde service. Als u de configuratie-instellingen van een service wilt wijzigen, selecteert u de service op de zijbalk **services** (aan de linkerkant). Navigeer vervolgens naar het tabblad **Configs** op de pagina servicedetails.

![Zijbalk van Apache Ambari Services](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

### <a name="modify-namenode-java-heap-size"></a>Grootte van de Heap NameNode Java wijzigen

De heapgrootte namenode Java is afhankelijk van vele factoren, zoals de belasting op het cluster. Ook het aantal bestanden en het aantal blokken. De standaardgrootte van 1 GB werkt goed bij de meeste clusters, hoewel sommige workloads meer of minder geheugen vereisen.

Ga als u de heapgrootte NameNode Java wijzigen:

1. Selecteer **HDFS** op de zijbalk van Services en navigeer naar het tabblad **Configs.**

    ![Apache Ambari HDFS-configuratie](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Zoek de instelling **NameNode Java heap size**. U het **tekstvak van** het filter ook gebruiken om een bepaalde instelling te typen en te zoeken. Selecteer het **penpictogram** naast de instellingsnaam.

    ![Apache Ambari NameNode Java heap grootte](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Typ de nieuwe waarde in het tekstvak en druk op **Enter** om de wijziging op te slaan.

    ![Ambari Bewerken NameNode Java heap grootte1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. De Heapgrootte namenode Java wordt gewijzigd in 1 GB van 2 GB.

    ![Bewerkte NameNode Java heap size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Sla de wijzigingen op door boven aan het configuratiescherm op de groene knop **Opslaan te** klikken.

    !['Apache Ambari slaat configuraties op'](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="apache-hive-optimization"></a>Apache Hive optimalisatie

In de volgende secties worden configuratieopties beschreven voor het optimaliseren van de algehele Prestaties van Apache Hive.

1. Als u de configuratieparameters van Hive wilt wijzigen, selecteert u **Hive** op de zijbalk van Services.
1. Navigeer naar het tabblad **Configs.**

### <a name="set-the-hive-execution-engine"></a>Stel de Hive-uitvoeringsengine in

Hive biedt twee execution engines: [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) en [Apache TEZ](https://tez.apache.org/). Tez is sneller dan MapReduce. HDInsight Linux clusters hebben Tez als de standaard uitvoeringsengine. Ga als u de uitvoeringsengine wijzigen:

1. Typ op het tabblad Hive **Configs** **de uitvoeringsengine** in het filtervak.

    ![Apache Ambari Search uitvoeringsengine](./media/hdinsight-changing-configs-via-ambari/ambari-search-execution.png)

1. De standaardwaarde van de eigenschap **Optimalisatie** is **Tez**.

    ![Optimalisatie - Apache Tez motor](./media/hdinsight-changing-configs-via-ambari/optimization-apache-tez.png)

### <a name="tune-mappers"></a>Kaartjeskaarten

Hadoop probeert een*map*enkel bestand in meerdere bestanden te splitsen en de resulterende bestanden parallel te verwerken. Het aantal mappers is afhankelijk van het aantal splitsingen. De volgende twee configuratieparameters geven het aantal splitsingen voor de Tez-uitvoeringsengine weer:

* `tez.grouping.min-size`: Ondergrens voor de grootte van een gegroepeerde splitsing, met een standaardwaarde van 16 MB (16.777.216 bytes).
* `tez.grouping.max-size`: Bovengrens voor de grootte van een gegroepeerde splitsing, met een standaardwaarde van 1 GB (1.073.741.824 bytes).

Als prestatierichtlijn verlaagt u beide parameters om de latentie te verbeteren en te verhogen voor meer doorvoer.

Als u bijvoorbeeld vier mappertaken wilt instellen voor een gegevensgrootte van 128 MB, stelt u beide parameters in op 32 MB per stuk (33.554.432 bytes).

1. Als u de limietparameters wilt wijzigen, navigeert u naar het tabblad **Configs** van de Tez-service. Vouw het deelvenster **Algemeen** `tez.grouping.max-size` uit `tez.grouping.min-size` en zoek de parameters en parameters.

1. Stel beide parameters in op **33.554.432** bytes (32 MB).

    ![Apache Ambari Tez groepering maten](./media/hdinsight-changing-configs-via-ambari/apache-tez-grouping-size.png)

Deze wijzigingen zijn van invloed op alle Tez-taken op de server. Als u een optimaal resultaat wilt bereiken, kiest u de juiste parameterwaarden.

### <a name="tune-reducers"></a>Reducers afstemmen

[Apache ORC](https://orc.apache.org/) en [Snappy](https://google.github.io/snappy/) bieden beide hoge prestaties. Hive kan echter standaard te weinig reducers hebben, waardoor knelpunten ontstaan.

Stel dat u een invoergegevensgrootte van 50 GB hebt. Die gegevens in ORC-formaat met Snappy-compressie zijn 1 GB. Hive schat het aantal reducers dat nodig is als: `hive.exec.reducers.bytes.per.reducer`(aantal bytes input voor mappers / ).

Bij de standaardinstellingen zijn dit voorbeeld vier reducers.

De `hive.exec.reducers.bytes.per.reducer` parameter geeft het aantal verwerkte bytes per reducer op. De standaardwaarde is 64 MB. Het afstemmen van deze waarde verhoogt het parallellisme en kan de prestaties verbeteren. Tuning het te laag kan ook produceren te veel reducers, mogelijk nadelige gevolgen voor de prestaties. Deze parameter is gebaseerd op uw specifieke gegevensvereisten, compressie-instellingen en andere omgevingsfactoren.

1. Als u de parameter wilt wijzigen, navigeert u naar het tabblad Hive **Configs** en zoekt u de parameter **Gegevens per reducer** op de pagina Instellingen.

    ![Apache Ambari-gegevens per reducer](./media/hdinsight-changing-configs-via-ambari/ambari-data-per-reducer.png)

1. Selecteer **Bewerken** om de waarde te wijzigen in 128 MB (134.217.728 bytes) en druk op **Enter** om op te slaan.

    ![Ambari-gegevens per reducer - bewerkt](./media/hdinsight-changing-configs-via-ambari/data-per-reducer-edited.png)
  
    Met een invoergrootte van 1.024 MB, met 128 MB aan gegevens per reducer, zijn er acht reducers (1024/128).

1. Een onjuiste waarde voor de parameter **Data per Reducer** kan leiden tot een groot aantal reducers, wat de queryprestaties negatief beïnvloedt. Als u het maximum aantal `hive.exec.reducers.max` reducers wilt beperken, stelt u een geschikte waarde in. De standaardwaarde is 1009.

### <a name="enable-parallel-execution"></a>Parallelle uitvoering inschakelen

Een Hive-query wordt uitgevoerd in een of meer fasen. Als de onafhankelijke fasen parallel kunnen worden uitgevoerd, zal dat de queryprestaties verhogen.

1. Als u parallelle query-uitvoering wilt inschakelen, navigeert `hive.exec.parallel` u naar het tabblad Hive **Config** en zoekt u naar de eigenschap. De standaardwaarde is false. Wijzig de waarde in true en druk op **Enter** om de waarde op te slaan.

1. Als u het aantal taken wilt beperken `hive.exec.parallel.thread.number` dat parallel moet worden uitgevoerd, wijzigt u de eigenschap. De standaardwaarde is 8.

    ![Apache Hive exec parallelle display](./media/hdinsight-changing-configs-via-ambari/apache-hive-exec-parallel.png)

### <a name="enable-vectorization"></a>Vectorisatie inschakelen

Hive verwerkt gegevens rij voor rij. Vectorisatie stuurt Hive om gegevens te verwerken in blokken van 1.024 rijen in plaats van één rij tegelijk. Vectorisatie is alleen van toepassing op de ORC-bestandsindeling.

1. Als u een vectorized query-uitvoering wilt inschakelen, navigeert `hive.vectorized.execution.enabled` u naar het tabblad Hive **Configs** en zoekt u naar de parameter. De standaardwaarde geldt voor Hive 0.13.0 of hoger.

1. Als u vectorized uitvoering voor de reduce-kant van de query wilt inschakelen, stelt u de `hive.vectorized.execution.reduce.enabled` parameter in op true. De standaardwaarde is false.

    ![Uitvoering apache hive vectorized](./media/hdinsight-changing-configs-via-ambari/hive-vectorized-execution.png)

### <a name="enable-cost-based-optimization-cbo"></a>Kostengebaseerde optimalisatie inschakelen (CBO)

Standaard volgt Hive een set regels om één optimaal queryuitvoeringsplan te vinden. Kostengebaseerde optimalisatie (CBO) evalueert meerdere plannen om een query uit te voeren. En wijst een kosten toe aan elk plan en bepaalt vervolgens het goedkoopste plan om een query uit te voeren.

Als u CBO wilt inschakelen, navigeert u naar **Hive** > **Configs-instellingen** > **Settings** en zoekt **u Kostengebaseerdoptimaliseren**inschakelen en schakelt u de schakelknop in op **Aan.**

![HDInsight kostengebaseerde optimizer](./media/hdinsight-changing-configs-via-ambari/hdinsight-cbo-config.png)

De volgende aanvullende configuratieparameters verhogen de prestaties van Hive-query's wanneer CBO is ingeschakeld:

* `hive.compute.query.using.stats`

    Wanneer ingesteld op true, Hive maakt gebruik van statistieken opgeslagen `count(*)`in de metastore om eenvoudige vragen te beantwoorden, zoals .

    ![Apache Hive compute query met behulp van statistieken](./media/hdinsight-changing-configs-via-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolomstatistieken worden gemaakt wanneer CBO is ingeschakeld. Hive gebruikt kolomstatistieken, die worden opgeslagen in metastore, om query's te optimaliseren. Het ophalen van kolomstatistieken voor elke kolom duurt langer wanneer het aantal kolommen hoog is. Wanneer deze instelling is ingesteld op false, schakelt deze instelling kolomstatistieken uit van de metastore.

    ![Apache Hive statistieken set kolom statistieken](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Basispartitiestatistieken zoals het aantal rijen, de gegevensgrootte en de bestandsgrootte worden opgeslagen in metastore. Als deze is ingesteld op true, worden de partitiestatistieken opgehaald uit metastore. Wanneer false, wordt de bestandsgrootte opgehaald uit het bestandssysteem. En het aantal rijen wordt opgehaald uit het rijschema.

    ![Hive statistieken set partitie statistieken](./media/hdinsight-changing-configs-via-ambari/hive-stats-fetch-partition-stats.png)

### <a name="enable-intermediate-compression"></a>Tussenliggende compressie inschakelen

Kaarttaken maken tussenliggende bestanden die worden gebruikt door de reducertaken. Tussencompressie vermindert de tussenliggende bestandsgrootte.

Hadoop banen zijn meestal I / O knelpunten. Het comprimeren van gegevens kan de i/o en de algehele netwerkoverdracht versnellen.

De beschikbare compressietypen zijn:

| Indeling | Hulpprogramma | Algoritme | Bestandsextensie | Splittable? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | Deflate | `.gz` | Nee |
| Bzip2 Bzip2 | Bzip2 Bzip2 | Bzip2 Bzip2 |`.bz2` | Ja |
| Lzo | `Lzop` | Lzo | `.lzo` | Ja, als geïndexeerd |
| Snappy | N.v.t. | Snappy | Snappy | Nee |

Als algemene regel is het belangrijk dat de compressiemethode splittable is, anders worden er weinig mappers gemaakt. Als de invoergegevens `bzip2` tekst zijn, is dit de beste optie. Voor ORC-indeling is Snappy de snelste compressieoptie.

1. Als u tussentijdse compressie wilt inschakelen, navigeert u `hive.exec.compress.intermediate` naar het tabblad Hive **Configs** en stelt u de parameter in op true. De standaardwaarde is false.

    !["Hive exec compress intermediate"](./media/hdinsight-changing-configs-via-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Als u tussenliggende bestanden wilt comprimeren, kiest u een compressiecodec met lagere CPU-kosten, zelfs als de codec geen hoge compressie-uitvoer heeft.

1. Als u de tussenliggende compressiecodec `mapred.map.output.compression.codec` wilt `hive-site.xml` `mapred-site.xml` instellen, voegt u de aangepaste eigenschap toe aan het of-bestand.

1. Een aangepaste instelling toevoegen:

    a. Navigeer naar **Hive** > **Configs** > **Advanced** > **Custom hive-site.**

    b. Selecteer **Eigenschap toevoegen...** onder aan het deelvenster Aangepaste korfsite.

    c. Voer in het venster `mapred.map.output.compression.codec` Eigenschap toevoegen `org.apache.hadoop.io.compress.SnappyCodec` in als de sleutel en als waarde.

    d. Selecteer **Toevoegen**.

    !['Apache Hive aangepaste eigenschap toe te voegen'](./media/hdinsight-changing-configs-via-ambari/hive-custom-property.png)

    Met deze instelling comprimeert u het tussenbestand met Snappy-compressie. Zodra de eigenschap is toegevoegd, wordt deze weergegeven in het deelvenster Aangepaste korfsite.

    > [!NOTE]  
    > Deze procedure wijzigt `$HADOOP_HOME/conf/hive-site.xml` het bestand.

### <a name="compress-final-output"></a>Einduitvoer comprimeren

De uiteindelijke Hive-uitvoer kan ook worden gecomprimeerd.

1. Als u de uiteindelijke Hive-uitvoer wilt comprimeren, navigeert `hive.exec.compress.output` u naar het tabblad Hive **Configs** en stelt u de parameter in op true. De standaardwaarde is false.

1. Als u de uitvoercompressiecodec `mapred.output.compression.codec` wilt kiezen, voegt u de aangepaste eigenschap toe aan het deelvenster Aangepaste korfsite, zoals beschreven in stap 3 van de vorige sectie.

    ![Voeg toevoegtoepassing van de aangepaste eigenschap Apache Hive2](./media/hdinsight-changing-configs-via-ambari/hive-custom-property2.png)

### <a name="enable-speculative-execution"></a>Speculatieve uitvoering inschakelen

Speculatieve uitvoering lanceert een bepaald aantal dubbele taken om de lijst met langzaam lopende taaktrackers te detecteren en te weigeren. Terwijl het verbeteren van de algehele taakuitvoering door het optimaliseren van individuele taakresultaten.

Speculatieve uitvoering mag niet worden ingeschakeld voor langlopende mapReduce taken met grote hoeveelheden invoer.

* Als u speculatieve uitvoering wilt inschakelen, navigeert u `hive.mapred.reduce.tasks.speculative.execution` naar het tabblad Hive **Configs** en stelt u de parameter in op true. De standaardwaarde is false.

    !['Hive mapred vermindert taken speculatieve uitvoering'](./media/hdinsight-changing-configs-via-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

### <a name="tune-dynamic-partitions"></a>Dynamische partities afstemmen

Hive maakt het mogelijk om dynamische partities te maken bij het invoegen van records in een tabel, zonder elke partitie vooraf te definiëren. Dit vermogen is een krachtige functie. Hoewel het kan resulteren in de oprichting van een groot aantal partities. En een groot aantal bestanden voor elke partitie.

1. Als hive dynamische partities `hive.exec.dynamic.partition` moet doen, moet de parameterwaarde true zijn (de standaardwaarde).

1. Wijzig de dynamische partitiemodus in *strikte*. In de strikte modus moet ten minste één partitie statisch zijn. Met deze instelling worden query's zonder het partitiefilter in de WHERE-component voorkomen, dat wil zeggen *dat u query's* voorkomt die alle partities scannen. Navigeer naar het tabblad Hive **Configs** en stel deze in `hive.exec.dynamic.partition.mode` op **streng.** De standaardwaarde is **niet strikt**.

1. Als u het aantal dynamische partities wilt `hive.exec.max.dynamic.partitions` beperken dat moet worden gemaakt, wijzigt u de parameter. De standaardwaarde is 5000.

1. Als u het totale aantal dynamische partities `hive.exec.max.dynamic.partitions.pernode`per knooppunt wilt beperken, wijzigt u . De standaardwaarde is 2000.

### <a name="enable-local-mode"></a>Lokale modus inschakelen

Lokale modus stelt Hive in staat om alle taken van een taak op één machine uit te voeren. Of soms in een enkel proces. Deze instelling verbetert de queryprestaties als de invoergegevens klein zijn. En de overhead van het starten van taken voor query's verbruikt een aanzienlijk percentage van de totale query-uitvoering.

Als u de lokale `hive.exec.mode.local.auto` modus wilt inschakelen, voegt u de parameter toe aan het deelvenster Aangepaste korfsite, zoals uitgelegd in stap 3 van de sectie [Tussenliggende compressie inschakelen.](#enable-intermediate-compression)

![Apache Hive exec-modus lokale auto](./media/hdinsight-changing-configs-via-ambari/hive-exec-mode-local-auto.png)

### <a name="set-single-mapreduce-multigroup-by"></a>Eén kaart instellen Verminderen MultiGROUP BY

Wanneer deze eigenschap is ingesteld op true, genereert een multigroep by-query met algemene groeps-door-sleutels één mapreduce-taak.  

Als u dit gedrag `hive.multigroupby.singlereducer` wilt inschakelen, voegt u de parameter toe aan het deelvenster Aangepaste korfsite, zoals uitgelegd in stap 3 van de sectie [Tussenliggende compressie inschakelen.](#enable-intermediate-compression)

![Hive set single MapReduce MultiGROUP BY](./media/hdinsight-changing-configs-via-ambari/hive-multigroupby-singlereducer.png)

### <a name="additional-hive-optimizations"></a>Aanvullende Hive-optimalisaties

In de volgende secties worden aanvullende Hive-gerelateerde optimalisaties beschreven die u instellen.

#### <a name="join-optimizations"></a>Deelnemen aan optimalisaties

Het standaard jointype in Hive is een *shuffle join*. In Hive lezen speciale mappers de invoer en zenden ze een join-sleutel/waardepaar uit naar een tussenbestand. Hadoop sorteert en voegt deze paren samen in een shuffle stage. Deze shuffle fase is duur. Het selecteren van de juiste join op basis van uw gegevens kan de prestaties aanzienlijk verbeteren.

| Lid worden voor tekst | Wanneer | Procedure | Hive-instellingen | Opmerkingen |
| --- | --- | --- | --- | --- |
| Shuffle Join | <ul><li>Standaardkeuze</li><li>Werkt altijd</li></ul> | <ul><li>Leest uit een deel van een van de tabellen</li><li>Emmers en soorten op Join-toets</li><li>Stuurt één emmer naar elke reduce</li><li>Join gebeurt aan de kant van Reduce</li></ul> | Geen significante Hive-instelling nodig | Werkt elke keer |
| Kaart deelnemen | <ul><li>Eén tabel past in het geheugen</li></ul> | <ul><li>Leest kleine tabel in geheugenhashtabel</li><li>Stromen door een deel van het grote bestand</li><li>Voegt elke record toe vanuit de hashtabel</li><li>Joins zijn door de mapper alleen</li></ul> | `hive.auto.confvert.join=true` | Snel, maar beperkt |
| Samenvoegbucket sorteren | Als beide tabellen: <ul><li>Gesorteerd hetzelfde</li><li>Emmers hetzelfde</li><li>Deelnemen aan de gesorteerde/bucketed kolom</li></ul> | Elk proces: <ul><li>Leest een emmer uit elke tabel</li><li>Verwerkt de rij met de laagste waarde</li></ul> | `hive.auto.convert.sortmerge.join=true` | Efficiënte |

#### <a name="execution-engine-optimizations"></a>Optimalisaties van de uitvoeringsengine

Aanvullende aanbevelingen voor het optimaliseren van de Hive-uitvoeringsengine:

| Instelling | Aanbevolen | HDInsight Standaard |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | True = veiliger, langzamer; false = sneller | false |
| `tez.am.resource.memory.mb` | 4 GB bovengrens voor de meeste | Automatisch afgestemd |
| `tez.session.am.dag.submit.timeout.secs` | 300+ | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000+ | 10.000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000+ | 20.000 |

## <a name="apache-pig-optimization"></a>Apache Pig optimalisatie

[De](https://pig.apache.org/) eigenschappen van Apache Pig kunnen worden gewijzigd vanaf de gebruikersinterface van het Ambari-web om varkensquery's af te stemmen. Als u de eigenschappen van Pig van Ambari `/etc/pig/2.4.2.0-258.0/pig.properties` wijzigt, worden de eigenschappen Van Varken in het bestand direct gewijzigd.

1. Als u de eigenschappen van varken wilt wijzigen, navigeert u naar het tabblad **Varkensconfigs** en vouwt u het deelvenster **Geavanceerde varkenseigenschappen** uit.

1. Zoek, maak geen commentaar en wijzig de waarde van de eigenschap die u wilt wijzigen.

1. Selecteer **Opslaan** aan de rechterbovenzijde van het venster om de nieuwe waarde op te slaan. Voor sommige eigenschappen is een serviceopnieuw opstarten vereist.

    ![Geavanceerde Apache varkenseigenschappen](./media/hdinsight-changing-configs-via-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Instellingen op sessieniveau overschrijven `pig.properties` eigenschapswaarden in het bestand.

### <a name="tune-execution-engine"></a>Uitvoeringsengine afstemmen

Er zijn twee uitvoeringsengines beschikbaar om Pig-scripts uit te voeren: MapReduce en Tez. Tez is een geoptimaliseerde motor en is veel sneller dan MapReduce.

1. Als u de uitvoeringsengine wilt wijzigen, zoekt u `exectype`in het deelvenster **Geavanceerde varkenseigenschappen** de eigenschap .

1. De standaardwaarde is **MapReduce**. Verander het in **Tez.**

### <a name="enable-local-mode"></a>Lokale modus inschakelen

Net als bij Hive wordt de lokale modus gebruikt om taken te versnellen met relatief kleinere hoeveelheden gegevens.

1. Als u de lokale `pig.auto.local.enabled` modus wilt inschakelen, stelt u in op **true**. De standaardwaarde is false.

1. Taken met een invoergegevensgrootte `pig.auto.local.input.maxbytes` die kleiner is dan de waarde van de eigenschap, worden beschouwd als kleine taken. De standaardwaarde is 1 GB.

### <a name="copy-user-jar-cache"></a>Cache van gebruikerspotten kopiëren

Pig kopieert de JAR-bestanden die udf's nodig hebben naar een gedistribueerde cache om ze beschikbaar te maken voor taakknooppunten. Deze potten veranderen niet vaak. Als dit is `pig.user.cache.enabled` ingeschakeld, kunnen potten met de instelling in een cache worden geplaatst om ze opnieuw te gebruiken voor taken die door dezelfde gebruiker worden uitgevoerd. Deze instelling resulteert in een kleine toename van de prestaties van het werk.

1. Om in `pig.user.cache.enabled` te schakelen, ingesteld op true. De standaardinstelling is onwaar.

1. Als u het basispad van de `pig.user.cache.location` potin de cache wilt instellen, stelt u het basispad in. De standaardwaarde is `/tmp`.

### <a name="optimize-performance-with-memory-settings"></a>Prestaties optimaliseren met geheugeninstellingen

Met de volgende geheugeninstellingen u de prestaties van het Pig-script optimaliseren.

* `pig.cachedbag.memusage`: De hoeveelheid geheugen die aan een zak wordt gegeven. Een tas is verzameling tuples. Een tuple is een geordende set velden en een veld is een stuk gegevens. Als de gegevens in een zak buiten het opgegeven geheugen valt, wordt deze op de schijf gemorst. De standaardwaarde is 0,2, wat neerkomt op 20 procent van het beschikbare geheugen. Dit geheugen wordt gedeeld over alle zakken in een toepassing.

* `pig.spill.size.threshold`: Zakken die groter zijn dan deze spill-groottedrempel (in bytes) worden op schijf gemorst. De standaardwaarde is 5 MB.

### <a name="compress-temporary-files"></a>Tijdelijke bestanden comprimeren

Pig genereert tijdelijke bestanden tijdens de uitvoering van de taak. Het comprimeren van de tijdelijke bestanden resulteert in een prestatieverhoging bij het lezen of schrijven van bestanden op schijf. De volgende instellingen kunnen worden gebruikt om tijdelijke bestanden te comprimeren.

* `pig.tmpfilecompression`: Wanneer dit waar is, maakt u tijdelijke bestandscompressie mogelijk. De standaardwaarde is false.

* `pig.tmpfilecompression.codec`: De compressiecode c die moet worden gebruikt voor het comprimeren van de tijdelijke bestanden. De aanbevolen compressiecodecs zijn [LZO](https://www.oberhumer.com/opensource/lzo/) en Snappy voor lager CPU-gebruik.

### <a name="enable-split-combining"></a>Splitsen combineren inschakelen

Wanneer deze zijn ingeschakeld, worden kleine bestanden gecombineerd voor minder kaarttaken. Deze instelling verbetert de efficiëntie van taken met veel kleine bestanden. Om in `pig.noSplitCombination` te schakelen, ingesteld op true. De standaardwaarde is false.

### <a name="tune-mappers"></a>Kaartjeskaarten

Het aantal mappers wordt gecontroleerd door `pig.maxCombinedSplitSize`de eigenschap te wijzigen. Deze eigenschap geeft de grootte aan van de gegevens die door één kaarttaak moeten worden verwerkt. De standaardwaarde is de standaardblokgrootte van het bestandssysteem. Het verhogen van deze waarde resulteert in een lager aantal mappertaken.

### <a name="tune-reducers"></a>Reducers afstemmen

Het aantal reducers wordt berekend `pig.exec.reducers.bytes.per.reducer`op basis van de parameter . De parameter geeft het aantal verwerkte bytes per reducer op, standaard 1 GB. Als u het maximum aantal reducers wilt beperken, stelt u de `pig.exec.reducers.max` eigenschap standaard 999 in.

## <a name="apache-hbase-optimization-with-the-ambari-web-ui"></a>Apache HBase optimalisatie met de Ambari web UI

[Apache HBase-configuratie](https://hbase.apache.org/) wordt gewijzigd op het tabblad **HBase Configs.** In de volgende secties worden enkele belangrijke configuratie-instellingen beschreven die de prestaties van HBase beïnvloeden.

### <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE instellen

De grootte van de HBase-heap geeft de maximale hoeveelheid heap aan die moet worden gebruikt in megabytes per *regio* en *hoofdservers.* De standaardwaarde is 1000 MB. Deze waarde moet worden afgestemd op de clusterworkload.

1. Als u wilt wijzigen, navigeert u naar het deelvenster **Geavanceerd HBase-env** op het tabblad HBase **Configs** en zoekt u de `HBASE_HEAPSIZE` instelling.

1. Wijzig de standaardwaarde in 5000 MB.

    !['Apache Ambari HBase geheugen heapsize'](./media/hdinsight-changing-configs-via-ambari/ambari-hbase-heapsize.png)

### <a name="optimize-read-heavy-workloads"></a>Leeszware workloads optimaliseren

De volgende configuraties zijn belangrijk om de prestaties van leeszware workloads te verbeteren.

#### <a name="block-cache-size"></a>Cachegrootte blokkeren

De blokcache is de leescache. De grootte wordt `hfile.block.cache.size` gecontroleerd door de parameter. De standaardwaarde is 0,4, dat is 40 procent van het totale werkgeheugen van de regioserver. Hoe groter de grootte van de blokcache, hoe sneller de willekeurige leest zal zijn.

1. Als u deze parameter wilt wijzigen, navigeert u naar het tabblad **Instellingen** op het tabblad HBase **Configs** en zoekt **u % van regioserver toegewezen aan Leesbuffers**.

    ![Apache HBase-geheugenblokcachegrootte](./media/hdinsight-changing-configs-via-ambari/hbase-block-cache-size.png)

1. Als u de waarde wilt wijzigen, selecteert u het pictogram **Bewerken.**

#### <a name="memstore-size"></a>Memstore grootte

Alle bewerkingen worden opgeslagen in de geheugenbuffer, een *Memstore*genoemd. Deze buffer verhoogt de totale hoeveelheid gegevens die in één bewerking naar de schijf kan worden geschreven. Het versnelt ook de toegang tot de recente bewerkingen. De grootte van de Memstore wordt gedefinieerd door de volgende twee parameters:

* `hbase.regionserver.global.memstore.UpperLimit`: Hiermee definieert u het maximale percentage van de regioserver die Memstore samen kan gebruiken.

* `hbase.regionserver.global.memstore.LowerLimit`: Hiermee definieert u het minimumpercentage van de regioserver die Memstore samen kan gebruiken.

Om te optimaliseren voor willekeurige reads, u de memstore boven- en ondergrenzen verlagen.

#### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Aantal rijen dat is opgehaald bij het scannen vanaf de schijf

De `hbase.client.scanner.caching` instelling definieert het aantal rijen `next` dat van de schijf wordt gelezen wanneer de methode op een scanner wordt aangeroepen.  De standaardwaarde is 100. Hoe hoger het getal, hoe minder externe oproepen van de client naar de regioserver, wat resulteert in snellere scans. Deze instelling verhoogt echter ook de geheugendruk op de client.

![Apache HBase aantal opgehaalde rijen](./media/hdinsight-changing-configs-via-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Stel de waarde niet zodanig in dat de tijd tussen het aanroepen van de volgende methode op een scanner groter is dan de time-out van de scanner. De time-outduur van de `hbase.regionserver.lease.period` scanner wordt bepaald door de eigenschap.

### <a name="optimize-write-heavy-workloads"></a>Schrijfzware workloads optimaliseren

De volgende configuraties zijn belangrijk om de prestaties van schrijfzware workloads te verbeteren.

#### <a name="maximum-region-file-size"></a>Maximale regiobestandsgrootte

HBase slaat gegevens op in een interne bestandsindeling, *hfile*genaamd. De `hbase.hregion.max.filesize` eigenschap definieert de grootte van één HFile voor een regio.  Een regio wordt opgesplitst in twee regio's als de som van alle HFiles in een regio groter is dan deze instelling.

!['Apache HBase HRegion max filesize'](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-max-filesize.png)

Hoe groter de bestandsgrootte van de regio, hoe kleiner het aantal splitsingen. U de bestandsgrootte vergroten om een waarde te bepalen die resulteert in de maximale schrijfprestaties.

#### <a name="avoid-update-blocking"></a>Voorkom het blokkeren van updates

* De `hbase.hregion.memstore.flush.size` eigenschap definieert de grootte waarop Memstore wordt gespoeld naar schijf. De standaardgrootte is 128 MB.

* De HBase-regioblokvermenigvuldiger `hbase.hregion.memstore.block.multiplier`wordt gedefinieerd door . De standaardwaarde is 4. Het maximaal toegestane is 8.

* HBase blokkeert updates als de`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`Memstore ( ) bytes is.

    Met de standaardwaarden van flushgrootte en blokvermenigvuldiger worden updates geblokkeerd wanneer Memstore 128 * 4 = 512 MB groot is. Als u het aantal updateblokkeringen wilt verminderen, verhoogt u de waarde van `hbase.hregion.memstore.block.multiplier`.

![Apache HBase-regioblokvermenigvuldiger](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-block-multiplier.png)

### <a name="define-memstore-size"></a>Memstore-grootte definiëren

Memstore grootte wordt bepaald `hbase.regionserver.global.memstore.UpperLimit` `hbase.regionserver.global.memstore.LowerLimit` door de en parameters. Als u deze waarden gelijk aan elkaar stelt, worden pauzes tijdens het schrijven verminderd (waardoor ook vaker spoelen wordt veroorzaakt) en resulteert dit in hogere schrijfprestaties.

### <a name="set-memstore-local-allocation-buffer"></a>Lokale toewijzingsbuffer voor Memstore instellen

Het gebruik van de lokale toewijzingsbuffer van Memstore wordt bepaald door de eigenschap `hbase.hregion.memstore.mslab.enabled`. Wanneer ingeschakeld (true), voorkomt deze instelling heapfragmentatie tijdens zware schrijfbewerking. De standaardwaarde is waar.

![hbase.hregion.memstore.mslab.enabled hbase.hregion.memstore.mslab.enabled hbase.hregion.memstore.mslab.enabled hbase](./media/hdinsight-changing-configs-via-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Volgende stappen

* [HDInsight-clusters beheren met de gebruikersinterface van apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
