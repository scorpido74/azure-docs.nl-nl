---
title: Functies maken voor gegevens in een Hadoop-cluster - Team Data Science Process
description: Voorbeelden van Hive-query's die functies in de gegevens die zijn opgeslagen in een Azure HDInsight Hadoop-cluster genereren.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 979652a467ea91c05884d2f7a24781f82035e505
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982047"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query 's
Dit document wordt beschreven hoe u functies maken voor gegevens die zijn opgeslagen in een Azure HDInsight Hadoop-cluster met behulp van Hive-query's. Deze Hive-query's gebruikt ingesloten Hive User-Defined-functies (UDF's), de scripts die worden geleverd.

De bewerkingen die nodig zijn voor het maken van de functies kunnen geheugenintensief zijn. De prestaties van Hive-query's wordt meer kritieke in dergelijke gevallen en kan worden verbeterd door het afstemmen van bepaalde parameters. Het afstemmen van deze parameters wordt in de laatste sectie besproken.

Voorbeelden van de query's die worden gepresenteerd zijn specifiek voor de [NYC Taxi reisgegevens](https://chriswhong.com/open-data/foil_nyc_taxi/) scenario's zijn ook beschikbaar [GitHub-opslagplaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's al hebben gegevensschema opgegeven en kunnen worden verzonden om uit te voeren. Parameters die gebruikers stemmen kunnen, zodat de prestaties van Hive-query's kan worden verbeterd worden ook beschreven in de laatste sectie.

Deze taak is een stap in de [Team Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u hebt:

* Een Azure storage-account gemaakt. Als u instructies nodig hebt, raadpleegt u [maken van een Azure Storage-account](../../storage/common/storage-account-create.md)
* Een aangepaste Hadoop-cluster met de HDInsight-service wordt ingericht.  Als u instructies nodig hebt, raadpleegt u [aanpassen Azure HDInsight Hadoop-Clusters voor Advanced Analytics](customize-hadoop-cluster.md).
* De gegevens is geüpload naar de Hive-tabellen in Azure HDInsight Hadoop-clusters. Als dit niet het geval is, voert u de [maken en gegevens laden met Hive-tabellen](move-hive-tables.md) het uploaden van gegevens met Hive-tabellen eerst.
* Externe toegang tot het cluster ingeschakeld. Als u instructies nodig hebt, raadpleegt u [toegang tot de hoofd-knooppunt van Hadoop Cluster](customize-hadoop-cluster.md).

## <a name="hive-featureengineering"></a>Functie genereren
In deze sectie vindt u enkele voorbeelden van de manieren waarop functies kunnen genereren met behulp van Hive-query's. Nadat u extra functies hebt gegenereerd, kunt u ze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de aanvullende functies en de primaire sleutel, die vervolgens kan worden samengevoegd met de oorspronkelijke tabel. Hier volgen de voorbeelden:

1. [Genereren van de frequentie op basis van functie](#hive-frequencyfeature)
2. [Risico's van Categorische variabelen in binaire classificatie](#hive-riskfeature)
3. [Functies onttrekken aan Datetime Field](#hive-datefeatures)
4. [Functies onttrekken aan tekstveld](#hive-textfeatures)
5. [Afstand tussen de GPS-coördinaten berekenen](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Genereren van de frequentie op basis van functie
Vaak is het handig om de frequentie van de niveaus van een categorische variabele of de frequenties van bepaalde combinaties van niveaus van meerdere categorische variabelen te berekenen. Gebruikers kunnen het volgende script gebruiken voor het berekenen van deze frequenties:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="hive-riskfeature"></a>Risico's van categorische variabelen in binaire classificatie
In binaire indeling, moeten niet-numerieke categorische variabelen worden geconverteerd naar numerieke functies numerieke functies worden uitgevoerd door de modellen die alleen wordt gebruikt. Deze conversie wordt uitgevoerd door elk niveau van de niet-numerieke vervangen door een numerieke risico. In deze sectie ziet u enkele algemene Hive-query's die de waarden van de risico's (log kans) van een categorische variabele berekenen.

        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

In dit voorbeeld variabelen `smooth_param1` en `smooth_param2` te gebruiken van de risicowaarden berekend op basis van de gegevens zijn ingesteld. Risico's hebben een bereik tussen - INF -bestand en inf-bestand. Een risico > 0 geeft aan dat de kans dat het doel gelijk aan 1 is groter zijn dan 0,5.

Na het risico wordt tabel berekend, wordt gebruikers kunnen risicowaarden toewijzen aan een tabel met het lid met de risico-tabel. Het lid te worden Hive-query is opgegeven in de vorige sectie.

### <a name="hive-datefeatures"></a>Functies van datetime-velden ophalen
Hive wordt geleverd met een set met UDF's voor het verwerken van datetime-velden. In Hive, de standaard datum/tijd-indeling is ' jjjj-MM-dd 00:00:00 ' ('01-01-1970 12:21:32 ' bijvoorbeeld). In deze sectie bevat voorbeelden van de dag van een maand, de maand van een datum / tijdveld uitpakken en andere voorbeelden die andere dan de standaardindeling naar een datum/tijd-tekenreeks opmaken in een datum/tijd-tekenreeks in een indeling worden geconverteerd.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Deze Hive-query gaat ervan uit dat het *veld\<datetime >* de standaard notatie voor datum/tijd heeft.

Als een datum / tijdveld zich niet in de standaardindeling, moet u de datum / tijdveld eerst converteren naar Unix-tijdstempel, en vervolgens de Unix-tijdstempel converteren naar een datum/tijd-tekenreeks die in de standaardindeling. Wanneer de datum/tijd in indeling is, kunnen gebruikers de ingesloten datum/tijd UDF's om op te halen van functies toepassen.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Als in deze query het *veld Datum/tijd van\<* het patroon bevat zoals *03/26/2015 12:04:39*, moet het *\<patroon van het veld Datum/tijd >* `'MM/dd/yyyy HH:mm:ss'`zijn. Als u wilt testen, kunnen gebruikers uitvoeren

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

De *hivesampletable* in deze query is geïnstalleerd op alle Azure HDInsight Hadoop-clusters standaard wanneer de clusters zijn ingericht.

### <a name="hive-textfeatures"></a>Functies onttrekken aan tekstvelden
Wanneer de Hive-tabel een tekstveld die een reeks woorden die worden gescheiden door spaties bevat heeft, wordt de volgende query uit de lengte van de tekenreeks en het aantal woorden in de tekenreeks.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>De afstand tussen de sets met GPS-coördinaten berekenen
De query die is opgegeven in deze sectie kan rechtstreeks worden toegepast op de reisgegevens NYC over taxi's. Het doel van deze query is om weer te geven over het toepassen van een ingesloten wiskundige functie in de component voor het genereren van functies.

De velden die worden gebruikt in deze query worden de GPS-coördinaten van ophalen en dropoff locaties, met de naam *ophalen\_lengtegraad*, *ophalen\_breedtegraad*,  *dropoff\_lengtegraad*, en *dropoff\_breedtegraad*. De query's die het berekenen van de directe afstand tussen de coördinaten ophalen en dropoff zijn:

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

De vergelijkingen die het berekenen van de afstand tussen de twee GPS-coördinaten te vinden in de <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">roerende Type Scripts</a> Peter Lapisu bijdragne-site. In dit java script is de functie `toRad()` gewoon *lat_or_lon*pi/180, waarmee graden wordt geconverteerd naar radialen. Hier *lat_or_lon* is de breedtegraad of lengtegraad. Aangezien Hive de functie biedt `atan2`, maar biedt de functie `atan`, wordt de `atan2` functie wordt geïmplementeerd door `atan` functie in de bovenstaande Hive-query met behulp van de definitie van de opgegeven in <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Werkruimte maken](./media/create-features-hive/atan2new.png)

Een volledige lijst met Hive ingesloten UDF's kunnen u vinden in de **ingebouwde functies** sectie op de <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive-wiki</a>).  

## <a name="tuning"></a> Geavanceerde onderwerpen: afstemmen Hive-parameters voor het verbeteren van de snelheid van query
De standaardinstellingen voor de parameter van Hive-cluster is mogelijk niet geschikt is voor de Hive-query's en de gegevens die de query's worden verwerkt. Deze sectie wordt besproken enkele parameters die gebruikers afstemmen kunnen om de prestaties van Hive-query's te verbeteren. Gebruikers moeten de parameter afstemmen van query's voordat de query's van de verwerking van gegevens toevoegen.

1. **Java-heap ruimte**: voor query's met betrekking tot het toevoegen van grote gegevenssets of verwerken van lange records **is bijna vol. heap** is een van de veelvoorkomende fouten. Deze fout kan worden vermeden door in te stellen parameters *mapreduce.map.java.opts* en *mapreduce.task.io.sort.mb* naar de gewenste waarden. Hier volgt een voorbeeld:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Deze parameter 4GB geheugen voor Java-heap ruimte toegewezen en maakt ook sorteren efficiënter door het toewijzen van meer geheugen voor deze. Er is een goed idee om af te spelen met deze toewijzingen als er een taak mislukt fouten met betrekking tot heap-ruimte.

1. **DFS-blokgrootte**: met deze parameter stelt de kleinste gegevenseenheid die het bestandssysteem opslaat. Een voorbeeld: als de DFS-blokgrootte 128 MB, klikt u vervolgens alle gegevens van de grootte kleiner zijn dan en maximaal is 128 MB opgeslagen in één blok. Gegevens die groter is dan 128 MB extra blokken wordt toegewezen. 
2. Het kiezen van een kleine blokgrootte zorgt ervoor dat grote overhead in Hadoop omdat het knooppunt met de naam voor het verwerken van veel meer aanvragen naar de desbetreffende blok met betrekking tot het bestand vinden. Een aanbevolen instelling wanneer betrekking tot gigabytes (of hoger) gegevens zijn:

        set dfs.block.size=128m;

2. **Join-bewerking in Hive optimaliseren**: terwijl join-bewerkingen in het kader voor toewijzen/verminderen doorgaans in de verminderen fase soms plaatsvinden enorme winsten kunnen worden bereikt door het plannen van joins in de fase van de kaart (ook wel 'mapjoins' genoemd). Om de Hive om dit te doen waar mogelijk, instellen:
   
       set hive.auto.convert.join=true;

3. **Het aantal mappers naar Hive**: terwijl Hadoop kan de gebruiker om het aantal reducers tegelijkertijd te stellen, het aantal mappers is doorgaans niet worden ingesteld door de gebruiker. Een kunst waarmee een zekere mate van controle voor dit nummer is de Hadoop-variabelen *mapred.min.split.size* en *mapred.max.split.size* als de grootte van elke kaart taak wordt bepaald door:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normaal gesproken de standaardwaarde van:
    
   - *mapred.min.split.Size* is ingesteld op 0 van
   - *mapred.Max.split.Size* is **Long.MAX** en die van 
   - *DFS.Block.Size* is 64 MB.

     Zoals we kunt zien, krijgt de gegevensgrootte afstemmen van deze parameters door "instelling" ze kunnen we om af te stemmen van het aantal mappers gebruikt.

4. Hier volgen enkele andere meer **geavanceerde opties** voor Hive-prestaties optimaliseren. Dit kunnen u instellen van het geheugen toegewezen als u wilt toewijzen en het aantal taken te verkleinen en kunnen nuttig zijn bij het afstemmen van prestaties. Houd er rekening mee dat de *mapreduce.reduce.memory.mb* mag niet groter zijn dan de fysieke geheugengrootte van elk werkrolknooppunt in het Hadoop-cluster.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

