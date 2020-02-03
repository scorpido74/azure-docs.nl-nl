---
title: 'Functies maken voor gegevens in een Azure HDInsight Hadoop cluster: team data Science process'
description: Voorbeelden van Hive-query's die functies in de gegevens die zijn opgeslagen in een Azure HDInsight Hadoop-cluster genereren.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c926aac3ea4360793ff52b616a55dc6198357c8a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721775"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Functies maken voor gegevens in een Hadoop-cluster met behulp van Hive-query 's
Dit document wordt beschreven hoe u functies maken voor gegevens die zijn opgeslagen in een Azure HDInsight Hadoop-cluster met behulp van Hive-query's. Deze Hive-query's gebruikt ingesloten Hive User-Defined-functies (UDF's), de scripts die worden geleverd.

De bewerkingen die nodig zijn voor het maken van de functies kunnen geheugenintensief zijn. De prestaties van Hive-query's wordt meer kritieke in dergelijke gevallen en kan worden verbeterd door het afstemmen van bepaalde parameters. Het afstemmen van deze parameters wordt in de laatste sectie besproken.

Voor beelden van de query's die worden gepresenteerd, zijn specifiek voor de [NYC-gegevens](https://chriswhong.com/open-data/foil_nyc_taxi/) scenario's in de [github-opslag plaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's al hebben gegevensschema opgegeven en kunnen worden verzonden om uit te voeren. Parameters die gebruikers stemmen kunnen, zodat de prestaties van Hive-query's kan worden verbeterd worden ook beschreven in de laatste sectie.

Deze taak is een stap in het [team data Science process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u hebt:

* Een Azure storage-account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Een aangepaste Hadoop-cluster met de HDInsight-service wordt ingericht.  Zie [Azure HDInsight Hadoop clusters aanpassen voor geavanceerde analyse](customize-hadoop-cluster.md)als u instructies nodig hebt.
* De gegevens is geüpload naar de Hive-tabellen in Azure HDInsight Hadoop-clusters. Als dat niet het geval is, volgt u [gegevens maken en laden in Hive-tabellen](move-hive-tables.md) om eerst gegevens naar Hive-tabellen te uploaden.
* Externe toegang tot het cluster ingeschakeld. Zie [toegang tot het hoofd knooppunt van het Hadoop-cluster](customize-hadoop-cluster.md)als u instructies nodig hebt.

## <a name="hive-featureengineering"></a>Onderdelen genereren
In deze sectie vindt u enkele voorbeelden van de manieren waarop functies kunnen genereren met behulp van Hive-query's. Nadat u extra functies hebt gegenereerd, kunt u ze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de aanvullende functies en de primaire sleutel, die vervolgens kan worden samengevoegd met de oorspronkelijke tabel. Hier volgen de voorbeelden:

1. [Genereren op basis van frequentie onderdelen](#hive-frequencyfeature)
2. [Risico's van Categorische-variabelen in binaire classificatie](#hive-riskfeature)
3. [Het veld datetime extra heren](#hive-datefeatures)
4. [Functies uit het tekst veld extra heren](#hive-textfeatures)
5. [De afstand tussen GPS-coördinaten berekenen](#hive-gpsdistance)

### <a name="hive-frequencyfeature"></a>Genereren op basis van frequentie onderdelen
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


### <a name="hive-riskfeature"></a>Risico's van Categorische-variabelen in binaire classificatie
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

In dit voor beeld worden variabelen `smooth_param1` en `smooth_param2` ingesteld op het vloeiend maken van de risico waarden die worden berekend op basis van de gegevens. Risico's hebben een bereik tussen - INF -bestand en inf-bestand. Een risico > 0 geeft aan dat de kans dat het doel gelijk aan 1 is groter zijn dan 0,5.

Na het risico wordt tabel berekend, wordt gebruikers kunnen risicowaarden toewijzen aan een tabel met het lid met de risico-tabel. Het lid te worden Hive-query is opgegeven in de vorige sectie.

### <a name="hive-datefeatures"></a>Functies extra heren uit datum/tijd-velden
Hive wordt geleverd met een set met UDF's voor het verwerken van datetime-velden. In Hive, de standaard datum/tijd-indeling is ' jjjj-MM-dd 00:00:00 ' ('01-01-1970 12:21:32 ' bijvoorbeeld). In deze sectie bevat voorbeelden van de dag van een maand, de maand van een datum / tijdveld uitpakken en andere voorbeelden die andere dan de standaardindeling naar een datum/tijd-tekenreeks opmaken in een datum/tijd-tekenreeks in een indeling worden geconverteerd.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Deze Hive-query gaat ervan uit dat het *veld\<datetime >* de standaard notatie voor datum/tijd heeft.

Als een datum / tijdveld zich niet in de standaardindeling, moet u de datum / tijdveld eerst converteren naar Unix-tijdstempel, en vervolgens de Unix-tijdstempel converteren naar een datum/tijd-tekenreeks die in de standaardindeling. Wanneer de datum/tijd in indeling is, kunnen gebruikers de ingesloten datum/tijd UDF's om op te halen van functies toepassen.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Als in deze query het *veld Datum/tijd van\<>* het patroon bevat zoals *03/26/2015 12:04:39*, moet het *\<patroon van het veld Datum/tijd >* `'MM/dd/yyyy HH:mm:ss'`zijn. Als u wilt testen, kunnen gebruikers uitvoeren

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

De *hivesampletable* in deze query is standaard vooraf geïnstalleerd op alle Azure HDInsight Hadoop clusters wanneer de clusters zijn ingericht.

### <a name="hive-textfeatures"></a>Functies uit tekst velden extra heren
Wanneer de Hive-tabel een tekstveld die een reeks woorden die worden gescheiden door spaties bevat heeft, wordt de volgende query uit de lengte van de tekenreeks en het aantal woorden in de tekenreeks.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="hive-gpsdistance"></a>De afstanden tussen sets van GPS-coördinaten berekenen
De query die is opgegeven in deze sectie kan rechtstreeks worden toegepast op de reisgegevens NYC over taxi's. Het doel van deze query is om weer te geven over het toepassen van een ingesloten wiskundige functie in de component voor het genereren van functies.

De velden die in deze query worden gebruikt, zijn de GPS-coördinaten van pickup-en dropoff-locaties, met de naam *pickup\_lengte graad*, *ophaling\_breedte graad*, *dropoff\_lengte graad*en *dropoff\_breedte graad*. De query's die het berekenen van de directe afstand tussen de coördinaten ophalen en dropoff zijn:

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

De wiskundige vergelijkingen waarmee de afstand tussen twee GPS-coördinaten worden berekend, vindt u op de site met <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Gebeweegde type scripts</a> , ontworpen door Peter Lapisu. In dit java script is de functie `toRad()` gewoon *lat_or_lon*pi/180, waarmee graden wordt geconverteerd naar radialen. *Lat_or_lon* is hier de breedte graad of lengte graad. Component bevat niet de functie `atan2`, maar biedt de functie `atan`, de functie `atan2` wordt geïmplementeerd door `atan` functie in de bovenstaande Hive-query met behulp van de definitie in <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Werkruimte maken](./media/create-features-hive/atan2new.png)

Een volledige lijst met Inge sloten Hive-Udf's vindt u in de sectie **ingebouwde functies** op de <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">wiki Apache Hive</a>.  

## <a name="tuning"></a>Geavanceerde onderwerpen: Hive-para meters afstemmen om de query snelheid te verbeteren
De standaardinstellingen voor de parameter van Hive-cluster is mogelijk niet geschikt is voor de Hive-query's en de gegevens die de query's worden verwerkt. Deze sectie wordt besproken enkele parameters die gebruikers afstemmen kunnen om de prestaties van Hive-query's te verbeteren. Gebruikers moeten de parameter afstemmen van query's voordat de query's van de verwerking van gegevens toevoegen.

1. **Java-heapruimte**: voor query's waarbij grote gegevens sets worden samengevoegd of waarmee lange **records worden verwerkt, is een** van de meest voorkomende fouten opgetreden. Deze fout kan worden vermeden door het instellen van de para meters *MapReduce. map. java. kiest* en *MapReduce. Task. io.* . Hier volgt een voorbeeld:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Met deze para meter wordt er 4 GB geheugen toegewezen aan de opslag ruimte voor Java-heaps, en wordt het sorteren efficiënter door meer geheugen toe te wijzen. Er is een goed idee om af te spelen met deze toewijzingen als er een taak mislukt fouten met betrekking tot heap-ruimte.

1. **Grootte van DFS-blok**: met deze para meter wordt de kleinste eenheid van gegevens ingesteld die het bestands systeem opslaat. Een voorbeeld: als de DFS-blokgrootte 128 MB, klikt u vervolgens alle gegevens van de grootte kleiner zijn dan en maximaal is 128 MB opgeslagen in één blok. Gegevens die groter is dan 128 MB extra blokken wordt toegewezen. 
2. Het kiezen van een kleine blokgrootte zorgt ervoor dat grote overhead in Hadoop omdat het knooppunt met de naam voor het verwerken van veel meer aanvragen naar de desbetreffende blok met betrekking tot het bestand vinden. Een aanbevolen instelling wanneer betrekking tot gigabytes (of hoger) gegevens zijn:

        set dfs.block.size=128m;

2. De **samenvoegings bewerking in de Hive wordt geoptimaliseerd**: Hoewel samen voegingen in het Framework van de kaart of het raam werk normaal gesp roken plaatsvinden in de reductie fase, kunnen enorm winsten worden gerealiseerd door het plannen van samen voegingen in de kaart fase (ook wel ' mapjoins ' genoemd). Stel deze optie in:
   
       set hive.auto.convert.join=true;

3. **Opgeven van het aantal mappers naar Hive**: Hoewel Hadoop de gebruiker in staat stelt het aantal verminderers in te stellen, wordt het aantal mappers doorgaans niet ingesteld door de gebruiker. Een slag waarmee u rekening moet houden met een zekere mate van controle op dit nummer is het kiezen van de Hadoop-variabelen *mapred. min. split. size* en *mapred. max. split. size* als de grootte van elke kaart taak wordt bepaald door:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Normaal gesproken de standaardwaarde van:
    
   - *mapred. min. split. grootte* is 0, dat van
   - *mapred. max. split. grootte* is **lang. Max** en die van 
   - *DFS. Block. grootte* is 64 MB.

     Zoals we kunt zien, krijgt de gegevensgrootte afstemmen van deze parameters door "instelling" ze kunnen we om af te stemmen van het aantal mappers gebruikt.

4. Hier volgen enkele andere **Geavanceerde opties** voor het optimaliseren van Hive-prestaties. Met deze opties kunt u het toegewezen geheugen instellen om taken te koppelen en te verminderen. Dit kan nuttig zijn bij het verfijnen van de prestaties. Houd er rekening mee dat *MapReduce. Reduc. MB* niet groter mag zijn dan de grootte van het fysieke geheugen van elk worker-knoop punt in het Hadoop-cluster.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

