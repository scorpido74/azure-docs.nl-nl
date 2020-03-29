---
title: Functies maken voor gegevens in een Azure HDInsight Hadoop-cluster - Team Data Science-proces
description: Voorbeelden van Hive-query's die functies genereren in gegevens die zijn opgeslagen in een Azure HDInsight Hadoop-cluster.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721775"
---
# <a name="create-features-for-data-in-a-hadoop-cluster-using-hive-queries"></a>Functies maken voor gegevens in een Hadoop-cluster met Hive-query's
In dit document ziet u hoe u functies maakt voor gegevens die zijn opgeslagen in een Azure HDInsight Hadoop-cluster met behulp van Hive-query's. Deze Hive-query's maken gebruik van ingesloten Hive-gebruikersgedefinieerde functies (UDF's), waarvan de scripts worden geleverd.

De bewerkingen die nodig zijn om functies te maken, kunnen geheugenintensief zijn. De prestaties van Hive-query's worden in dergelijke gevallen kritischer en kunnen worden verbeterd door bepaalde parameters af te stemmen. De afstemming van deze parameters wordt besproken in het laatste deel.

Voorbeelden van de query's die worden gepresenteerd zijn specifiek voor de [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) scenario's zijn ook opgenomen in [GitHub repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's hebben al een gegevensschema opgegeven en zijn klaar om te worden verzonden om uit te voeren. In het laatste gedeelte worden ook parameters besproken die gebruikers kunnen afstemmen, zodat de prestaties van Hive-query's kunnen worden verbeterd.

Deze taak is een stap in het [Team Data Science Process (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u:

* Een Azure-opslagaccount maken. Als u instructies nodig hebt, [raadpleegt u Een Azure Storage-account maken](../../storage/common/storage-account-create.md)
* Ingericht een aangepaste Hadoop cluster met de HDInsight service.  Zie [Azure HDInsight Hadoop Clusters voor geavanceerde analyse aanpassen](customize-hadoop-cluster.md)als u instructies nodig hebt.
* De gegevens zijn geüpload naar Hive-tabellen in Azure HDInsight Hadoop-clusters. Als dit niet het zo is, volgt [u gegevens maken en laden naar Hive-tabellen](move-hive-tables.md) om eerst gegevens naar Hive-tabellen te uploaden.
* Ingeschakelde externe toegang tot het cluster. Zie Toegang tot [het hoofdknooppunt van hadoopcluster](customize-hadoop-cluster.md)als u instructies nodig hebt.

## <a name="feature-generation"></a><a name="hive-featureengineering"></a>Functiegeneratie
In deze sectie worden verschillende voorbeelden beschreven van de manieren waarop functies kunnen worden genereren met Hive-query's. Zodra u extra functies hebt gegenereerd, u deze toevoegen als kolommen aan de bestaande tabel of een nieuwe tabel maken met de extra functies en primaire sleutel, die vervolgens kan worden samengevoegd met de oorspronkelijke tabel. Hier zijn de voorbeelden gepresenteerd:

1. [Frequentiegebaseerde functiegeneratie](#hive-frequencyfeature)
2. [Risico's van categorische variabelen in binaire classificatie](#hive-riskfeature)
3. [Functies uit Datetime-veld extraheren](#hive-datefeatures)
4. [Functies uit tekstveld extraheren](#hive-textfeatures)
5. [De afstand tussen GPS-coördinaten berekenen](#hive-gpsdistance)

### <a name="frequency-based-feature-generation"></a><a name="hive-frequencyfeature"></a>Frequentiegebaseerde functiegeneratie
Het is vaak nuttig om de frequenties van de niveaus van een categorische variabele, of de frequenties van bepaalde combinaties van niveaus van meerdere categorische variabelen te berekenen. Gebruikers kunnen het volgende script gebruiken om deze frequenties te berekenen:

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


### <a name="risks-of-categorical-variables-in-binary-classification"></a><a name="hive-riskfeature"></a>Risico's van categorische variabelen in binaire classificatie
In binaire classificatie moeten niet-numerieke categorische variabelen worden omgezet in numerieke kenmerken wanneer de gebruikte modellen alleen numerieke kenmerken aannemen. Deze conversie wordt uitgevoerd door elk niet-numeriek niveau te vervangen door een numeriek risico. In deze sectie worden enkele algemene Hive-query's weergegeven die de risicowaarden (logodds) van een categorische variabele berekenen.

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

In dit voorbeeld `smooth_param1` worden `smooth_param2` variabelen en ingesteld om de risicowaarden die op basis van de gegevens zijn berekend, glad te strijken. Risico's hebben een bereik tussen -Inf en Inf. Een risico > 0 geeft aan dat de kans dat het doel gelijk is aan 1 groter is dan 0,5.

Nadat de risicotabel is berekend, kunnen gebruikers risicowaarden toewijzen aan een tabel door deze aan te sluiten bij de risicotabel. De samenzoekquery voor hive is in de vorige sectie opgenomen.

### <a name="extract-features-from-datetime-fields"></a><a name="hive-datefeatures"></a>Functies uit datumtijdvelden extraheren
Hive wordt geleverd met een set UDF's voor het verwerken van datetime-velden. In Hive is de standaarddatumnotatie 'yyyy-MM-dd 00:00:00' ('1970-01-01 12:21:32' bijvoorbeeld). In deze sectie worden voorbeelden weergegeven die de dag van een maand, de maand uit een datumtijdveld en andere voorbeelden halen die een datumtekenreeks in een andere indeling dan de standaardnotatie converteren naar een datumtijdtekenreeks in standaardnotatie.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Deze bijenkorfquery gaat ervan uit dat het * \<veld datetime>* in de standaarddatumnotatie staat.

Als een datumtijdveld niet in de standaardnotatie staat, moet u eerst het datumtijdveld converteren naar Unix-tijdstempel en vervolgens de Unix-tijdstempel converteren naar een datumtekenreeks die zich in de standaardindeling bevindt. Wanneer de datumdatum in standaardnotatie is, kunnen gebruikers de ingesloten uDF's met datumtijd toepassen om functies te extraheren.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Als het * \<veld datetime>* in deze query het patroon heeft zoals *03/26/2015 12:04:39,* moet het * \<patroon van het veld datetime>'* zijn `'MM/dd/yyyy HH:mm:ss'`. Om het te testen, kunnen gebruikers

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

De *hivesampletabel* in deze query wordt standaard vooraf geïnstalleerd op alle Azure HDInsight Hadoop-clusters wanneer de clusters zijn ingericht.

### <a name="extract-features-from-text-fields"></a><a name="hive-textfeatures"></a>Functies uit tekstvelden extraheren
Wanneer de tabel Hive een tekstveld heeft dat een tekenreeks bevat die wordt afgebakend door spaties, wordt in de volgende query de lengte van de tekenreeks en het aantal woorden in de tekenreeks uitgepakt.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

### <a name="calculate-distances-between-sets-of-gps-coordinates"></a><a name="hive-gpsdistance"></a>Afstanden tussen sets GPS-coördinaten berekenen
De query in deze sectie kan direct worden toegepast op de NYC Taxi Trip Data. Het doel van deze query is om te laten zien hoe u een ingesloten wiskundige functie in Hive toepassen om functies te genereren.

De velden die worden gebruikt in deze query zijn de GPS-coördinaten van pick-up en dropoff locaties, genaamd *pick-up\_lengte,* *\_pick-up breedte,* *dropoff\_lengte,* en *dropoff\_breedtegraad*. De query's die de directe afstand tussen de ophaal- en dropoff-coördinaten berekenen, zijn:

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

De wiskundige vergelijkingen die de afstand tussen twee GPS-coördinaten berekenen, zijn te vinden op de site <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Movable Type Scripts,</a> geschreven door Peter Lapisu. In deze Javascript `toRad()` is de functie slechts *lat_or_lon*pi/180, die graden omzet in radialen. Hier is *lat_or_lon* de breedte- of lengtegraad. Aangezien Hive de functie `atan2`niet biedt, `atan`maar `atan2` de functie `atan` biedt, wordt de functie per functie geïmplementeerd in de bovenstaande Hive-query met behulp van de definitie in <a href="https://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipedia</a>.

![Werkruimte maken](./media/create-features-hive/atan2new.png)

Een volledige lijst van Hive embedded UDFs is te vinden in de **sectie Ingebouwde functies** op de <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache Hive wiki</a>).  

## <a name="advanced-topics-tune-hive-parameters-to-improve-query-speed"></a><a name="tuning"></a>Geavanceerde onderwerpen: Hive-parameters afstemmen om de querysnelheid te verbeteren
De standaardparameterinstellingen van hive-cluster zijn mogelijk niet geschikt voor de Hive-query's en de gegevens die de query's verwerken. In dit gedeelte worden enkele parameters besproken die gebruikers kunnen afstemmen om de prestaties van Hive-query's te verbeteren. Gebruikers moeten de parametertuningquery's toevoegen voordat de query's van verwerkingsgegevens worden gebruikt.

1. **Java heap ruimte:** Voor query's waarbij het samenvoegen van grote gegevenssets, of het verwerken van lange records, **opraken van heap ruimte** is een van de veelvoorkomende fouten. Deze fout kan worden voorkomen door parameters *mapreduce.map.java.opts* en *mapreduce.task.io.sort.mb* in te stellen op de gewenste waarden. Hier volgt een voorbeeld:
   
        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;

    Deze parameter wijst 4 GB geheugen toe aan Java heap ruimte en maakt ook sorteren efficiënter door het toewijzen van meer geheugen voor. Het is een goed idee om te spelen met deze toewijzingen als er geen fouten in het mislukken van de baan met betrekking tot heap ruimte.

1. **DFS-blokgrootte**: met deze parameter wordt de kleinste eenheid gegevens ingesteld die het bestandssysteem opslaat. Als de DFS-blokgrootte bijvoorbeeld 128 MB bedraagt, worden alle gegevens van minder dan en maximaal 128 MB in één blok opgeslagen. Gegevens die groter zijn dan 128 MB, worden extra blokken toegewezen. 
2. Het kiezen van een kleine blokgrootte veroorzaakt grote overheadkosten in Hadoop, omdat het naamknooppunt veel meer aanvragen moet verwerken om het relevante blok met betrekking tot het bestand te vinden. Een aanbevolen instelling bij het omgaan met gigabytes (of grotere) gegevens is:

        set dfs.block.size=128m;

2. **Optimaliseren join operatie in Hive**: Terwijl join operaties in de map / reduce framework meestal plaatsvinden in de vermindering fase, soms, enorme winsten kunnen worden bereikt door het plannen van joins in de kaartfase (ook wel "mapjoins"). Stel deze optie in:
   
       set hive.auto.convert.join=true;

3. **Het opgeven van het aantal mappers naar Hive:** Terwijl Hadoop de gebruiker in staat stelt om het aantal reducers in te stellen, wordt het aantal mappers meestal niet door de gebruiker ingesteld. Een truc die een zekere mate van controle op dit nummer mogelijk maakt, is om de Hadoop-variabelen *mapred.min.split.size* en *mapred.max.split.size* te kiezen, omdat de grootte van elke kaarttaak wordt bepaald door:
   
        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))
   
    Meestal is de standaardwaarde van:
    
   - *mapred.min.split.size* is 0, die van
   - *mapred.max.split.size* is **Long.MAX** en die van 
   - *dfs.block.size* is 64 MB.

     Zoals we kunnen zien, gezien de grootte van de gegevens, het afstemmen van deze parameters door "instelling" hen stelt ons in staat om het aantal mappers gebruikt af te stemmen.

4. Hier zijn een paar andere meer **geavanceerde opties** voor het optimaliseren van Hive prestaties. Met deze opties u het geheugen instellen dat is toegewezen om taken in kaart te brengen en taken te verminderen, en kan dit handig zijn bij het aanpassen van de prestaties. Houd er rekening mee dat de *mapreduce.reduce.memory.mb* niet groter mag zijn dan de fysieke geheugengrootte van elk werknemersknooppunt in het Hadoop-cluster.
   
        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;

