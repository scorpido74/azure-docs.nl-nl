---
title: Hive-query's in azure HDInsight optimaliseren
description: In dit artikel wordt beschreven hoe u uw Apache Hive query's voor Hadoop in HDInsight optimaliseert.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/21/2019
ms.openlocfilehash: d545cd997b35cfa5e7fec58b17507ce63097fd20
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898768"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Apache Hive query's optimaliseren in azure HDInsight

In azure HDInsight zijn er verschillende cluster typen en-technologieën die Apache Hive query's kunnen uitvoeren. Wanneer u uw HDInsight-cluster maakt, kiest u het juiste cluster type om de prestaties te optimaliseren voor de behoeften van uw werk belasting.

Kies bijvoorbeeld **interactieve query** cluster type om te optimaliseren voor ad hoc, interactieve query's. Kies Apache **Hadoop** -cluster type om te optimaliseren voor Hive-query's die als batch proces worden gebruikt. **Spark** -en **HBase** -cluster typen kunnen ook Hive-query's uitvoeren. Zie [Wat is Apache Hive en HiveQL in azure HDInsight?](hadoop/hdinsight-use-hive.md)voor meer informatie over het uitvoeren van Hive-query's op verschillende typen HDInsight-clusters.

HDInsight-clusters van het Hadoop-cluster type zijn standaard niet geoptimaliseerd voor prestaties. In dit artikel worden enkele van de meest voorkomende methoden voor het optimaliseren van Hive-onderdelen beschreven die u kunt Toep assen op uw query's.

## <a name="scale-out-worker-nodes"></a>Werk knooppunten uitschalen

Door het aantal worker-knoop punten in een HDInsight-cluster te verg Roten, kan het werk gebruikmaken van meer toewijzingen en minderers om parallel te worden uitgevoerd. Er zijn twee manieren om uitschalen in HDInsight te verg Roten:

* Op het moment dat u een cluster maakt, kunt u het aantal worker-knoop punten opgeven met behulp van de Azure Portal, Azure PowerShell of de opdracht regel interface.  Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. Op de volgende scherm afbeelding ziet u de configuratie van het worker-knoop punt op de Azure Portal:
  
    ![scaleout_1](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-scaleout-1.png "scaleout_1")
    
* Nadat u hebt gemaakt, kunt u het aantal worker-knoop punten ook bewerken om een cluster verder te schalen zonder dat u er een hoeft te maken:

    ![scaleout_2](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-scaleout-2.png "scaleout_2")

Zie [hdinsight-clusters schalen](hdinsight-scaling-best-practices.md) voor meer informatie over het schalen van hdinsight.

## <a name="use-apache-tez-instead-of-map-reduce"></a>Apache TEZ gebruiken in plaats van toewijzings reductie

[Apache TEZ](https://tez.apache.org/) is een alternatieve uitvoerings engine voor de MapReduce-engine. Voor HDInsight-clusters op basis van Linux is TEZ standaard ingeschakeld.

![tez_1](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

TEZ is sneller omdat:

* **Voer gerichte acyclische grafiek (dag) uit als één taak in de MapReduce-engine**. Voor de DAG moet elke set mappers worden gevolgd door één set verminderers. Dit leidt ertoe dat meerdere MapReduce-taken worden uitgeschakeld voor elke Hive-query. TEZ heeft deze beperking niet en kan complexe DAG als één taak verwerken, waardoor de opstart overhead van taken wordt geminimaliseerd.
* **Vermijd overbodige schrijf bewerkingen**. Meerdere taken worden gebruikt voor het verwerken van dezelfde Hive-query in de MapReduce-engine. De uitvoer van elke MapReduce-taak wordt naar HDFS geschreven voor tussenliggende gegevens. Omdat TEZ minimaliseert het aantal taken voor elke Hive-query, kan er onnodig schrijf bewerkingen worden voor komen.
* **Minimaliseert de opstart vertraging**. TEZ is een betere manier om de vertraging op te lossen door te verminderen van het aantal mappers dat moet worden gestart en verbetert ook de optimalisatie in.
* **Containers worden opnieuw gebruikt**. Wanneer mogelijke TEZ kunnen containers opnieuw worden gebruikt om ervoor te zorgen dat latentie als gevolg van het starten van containers wordt gereduceerd.
* **Continue optimalisatie technieken**. De traditionele optimalisatie is uitgevoerd tijdens de compilatie fase. Er is echter meer informatie over de invoer beschikbaar waarmee tijdens runtime betere optimalisatie mogelijk is. TEZ maakt gebruik van voortdurende optimalisatie technieken waarmee het plan verder in de runtime fase kan worden geoptimaliseerd.

Zie [Apache TEZ](https://tez.apache.org/)(Engelstalig) voor meer informatie over deze concepten.

U kunt alle Hive-query TEZ inschakelen door de volgende set-opdracht voor het voor voegsel van de query in te stellen:

   ```hive
   set hive.execution.engine=tez;
   ```

## <a name="hive-partitioning"></a>Hive partitioneren

I/O-bewerkingen zijn de belangrijkste prestatie knelpunt voor het uitvoeren van Hive-query's. De prestaties kunnen worden verbeterd als de hoeveelheid gegevens die moet worden gelezen, kan worden verminderd. Standaard worden in Hive-query's volledige Hive-tabellen gecontroleerd. Voor query's waarbij alleen een kleine hoeveelheid gegevens moeten worden gescand (bijvoorbeeld query's met filters), maakt dit gedrag onnodig overhead. Met hive-partitionering kunnen Hive-query's alleen toegang krijgen tot de benodigde hoeveelheid gegevens in Hive-tabellen.

Hive-partitionering wordt geïmplementeerd door de onbewerkte gegevens opnieuw in te delen in nieuwe directory's. Elke partitie heeft een eigen Bestands Directory. De partities worden gedefinieerd door de gebruiker. Het volgende diagram illustreert het partitioneren van een Hive-tabel op basis van het kolom *jaar*. Voor elk jaar wordt een nieuwe map gemaakt.

![Hive partitioneren](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Enkele overwegingen voor partitioneren:

* **Niet onder partitie** -partitionering op kolommen met slechts een paar waarden kunnen enkele partities veroorzaken. Bij partitioneren op geslacht worden bijvoorbeeld alleen twee te maken partities gemaakt (mannelijk en vrouwelijk), waardoor de latentie met Maxi maal de helft wordt beperkt.
* **Maak geen gebruik** van de partitie-aan de andere extreem, waardoor het maken van een partitie in een kolom met een unieke waarde (bijvoorbeeld userid) meerdere partities veroorzaakt. Bij overschrijding van de partitie wordt het namenode van het cluster veel zwaar belast, omdat het een groot aantal directory's moet afhandelen.
* **Vermijd gegevens scheefheid** : Kies uw partitie sleutel zodanig dat alle partities even groot zijn. Bijvoorbeeld: partitioneren op *status* kolom kan de distributie van gegevens scheef trekken. Omdat de status van Californië een populatie heeft die bijna 30x beter worden is van Vermont, kan de grootte van de partitie mogelijk afnemen en kunnen de prestaties enorm verschillen.

Als u een partitie tabel wilt maken, gebruikt u de *gepartitioneerde by* -component:

   ```hive
   CREATE TABLE lineitem_part
       (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING, 
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
   STORED AS TEXTFILE;
   ```
   
Zodra de gepartitioneerde tabel is gemaakt, kunt u statische partitionering of dynamische partitionering maken.

* **Statische partitionering** betekent dat u al Shard gegevens in de juiste directory's hebt. Met statische partities voegt u Hive-partities hand matig toe op basis van de maplocatie. Het volgende code fragment is een voor beeld.
  
   ```hive
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’)
   SELECT * FROM lineitem 
   WHERE lineitem.L_SHIPDATE = ‘5/23/1996 12:00:00 AM’
   
   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = ‘5/23/1996 12:00:00 AM’))
   LOCATION ‘wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```
   
* **Dynamische partitionering** houdt in dat componenten automatisch partities voor u moeten maken. Omdat u de partitie tabel al hebt gemaakt vanuit de faserings tabel, hoeft u alleen gegevens in de gepartitioneerde tabel in te voegen:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY , 
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE, 
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```
   
Zie [gepartitioneerde tabellen](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)voor meer informatie.

## <a name="use-the-orcfile-format"></a>De ORCFile-indeling gebruiken
Hive ondersteunt verschillende bestands indelingen. Bijvoorbeeld:

* **Text**: de standaard bestands indeling en werkt met de meeste scenario's.
* **Avro**: geschikt voor interoperabiliteits scenario's.
* **Orc/Parquet**: het meest geschikt voor prestaties.

De indeling ORC (geoptimaliseerde rij in kolommen) is een zeer efficiënte manier om Hive-gegevens op te slaan. Vergeleken met andere indelingen heeft ORC de volgende voor delen:

* ondersteuning voor complexe typen, waaronder DateTime en complexe en semi-gestructureerde typen.
* Maxi maal 70% compressie.
* indexeert elke 10.000 rijen, waarmee rijen overs Laan worden toegestaan.
* een aanzienlijke verwijdering tijdens runtime.

Als u de ORC-indeling wilt inschakelen, maakt u eerst een tabel met de-component *opgeslagen als Orc*:

   ```hive
   CREATE TABLE lineitem_orc_part
       (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
        L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
        L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
        L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
        L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
   PARTITIONED BY(L_SHIPDATE STRING)
   STORED AS ORC;
   ```
   
Vervolgens voegt u gegevens in de tabel ORC in vanuit de faserings tabel. Bijvoorbeeld:

   ```hive
   INSERT INTO TABLE lineitem_orc
   SELECT L_ORDERKEY as L_ORDERKEY, 
          L_PARTKEY as L_PARTKEY , 
          L_SUPPKEY as L_SUPPKEY,
          L_LINENUMBER as L_LINENUMBER,
          L_QUANTITY as L_QUANTITY, 
          L_EXTENDEDPRICE as L_EXTENDEDPRICE,
          L_DISCOUNT as L_DISCOUNT,
          L_TAX as L_TAX,
          L_RETURNFLAG as L_RETURNFLAG,
          L_LINESTATUS as L_LINESTATUS,
          L_SHIPDATE as L_SHIPDATE,
           L_COMMITDATE as L_COMMITDATE,
           L_RECEIPTDATE as L_RECEIPTDATE, 
           L_SHIPINSTRUCT as L_SHIPINSTRUCT,
           L_SHIPMODE as L_SHIPMODE,
           L_COMMENT as L_COMMENT
    FROM lineitem;
   ```
   
Meer informatie over de ORC-indeling vindt u in de [hand leiding voor de Apache Hive taal](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC).

## <a name="vectorization"></a>Vectorization

Met Vectorization kan een batch van 1024 rijen samen worden verwerkt in plaats van één rij tegelijk te verwerken. Dit betekent dat eenvoudige bewerkingen sneller worden uitgevoerd omdat minder interne code moet worden uitgevoerd.

Als u de vectorization voor voegsel van uw Hive-query wilt inschakelen met de volgende instelling:

   ```hive
    set hive.vectorized.execution.enabled = true;
   ```

Zie voor meer informatie [Vector query's uitvoeren](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Andere optimalisatie methoden
Er zijn meer optimalisatie methoden die u kunt overwegen, bijvoorbeeld:

* **Bucket van onderdelen:** een techniek waarmee grote gegevens sets kunnen worden geclusterd of gesegmenteerd om de query prestaties te optimaliseren.
* **Deelname aan optimalisatie:** optimalisatie van de query-uitvoerings planning van de component om de efficiëntie van deelname te verbeteren en de behoefte aan gebruikers hints te verminderen. Zie [deelname aan optimalisatie](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)voor meer informatie.
* **Verkleiners verhogen**.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u verschillende algemene optimalisatie methoden voor Hive-query's geleerd. Raadpleeg de volgende artikelen voor meer informatie:

* [Apache Hive in HDInsight gebruiken](hadoop/hdinsight-use-hive.md)
* [Vertraagde vlucht gegevens analyseren met behulp van interactieve Query's in HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Twitter-gegevens analyseren met Apache Hive in HDInsight](hdinsight-analyze-twitter-data-linux.md)
