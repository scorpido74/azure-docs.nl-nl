---
title: Hive-query's optimaliseren in Azure HDInsight
description: In dit artikel wordt beschreven hoe u uw Apache Hive-query's optimaliseren voor Hadoop in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 144d51d08a61526ec0f183a63e1fdf5658136293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272329"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Apache Hive-query's in Azure HDInsight optimaliseren

In Azure HDInsight zijn er verschillende clustertypen en -technologieën waarmee Apache Hive-query's kunnen worden uitgevoerd. Wanneer u uw HDInsight-cluster maakt, kiest u het juiste clustertype om de prestaties voor uw werkbelastingbehoeften te optimaliseren.

Kies bijvoorbeeld **het** clustertype Interactieve query om te optimaliseren voor ad hoc, interactieve query's. Kies het clustertype Apache **Hadoop** om te optimaliseren voor Hive-query's die als batchproces worden gebruikt. **Spark-** en **HBase-clustertypen** kunnen ook Hive-query's uitvoeren. Zie [Wat is Apache Hive en HiveQL op Azure HDInsight voor](hadoop/hdinsight-use-hive.md)meer informatie over het uitvoeren van Hive-query's op verschillende HDInsight-clustertypen.

HDInsight-clusters van het Hadoop-clustertype zijn standaard niet geoptimaliseerd voor prestaties. In dit artikel worden enkele van de meest voorkomende methoden voor prestatieoptimalisatie van Hive beschreven die u op uw query's toepassen.

## <a name="scale-out-worker-nodes"></a>Werknemersknooppunten uitschalen

Door het aantal werknemersknooppunten in een HDInsight-cluster te verhogen, kan het werk meer mappers en reducers gebruiken om parallel te worden uitgevoerd. Er zijn twee manieren waarop u de schaal vergroten in HDInsight:

* Op het moment dat u een cluster maakt, u het aantal werknemersknooppunten opgeven met behulp van de Azure-portal, Azure PowerShell of de commandline-interface.  Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md) voor meer informatie. In de volgende schermafbeelding wordt de configuratie van het werknemersknooppunt op de Azure-portal weergegeven:
  
    ![Knooppunten voor azure-portalclustergrootte](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* Na het maken u ook het aantal werknemersknooppunten bewerken om een cluster verder uit te schalen zonder er een opnieuw te maken:

    ![Clustergrootte azure-portalschaal](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

Zie [Scale HDInsight clusters](hdinsight-scaling-best-practices.md) voor meer informatie over het schalen van HDInsight

## <a name="use-apache-tez-instead-of-map-reduce"></a>Apache Tez gebruiken in plaats van Map Reduce

[Apache Tez](https://tez.apache.org/) is een alternatieve uitvoeringsmotor voor de MapReduce-engine. Linux-gebaseerde HDInsight clusters hebben Tez standaard ingeschakeld.

![HDInsight Apache Tez overzichtsdiagram](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez is sneller omdat:

* **Gerichte Acyclische grafiek (DAG) uitvoeren als één taak in de MapReduce-engine**. De DAG vereist dat elke set mappers wordt gevolgd door één set reducers. Dit zorgt ervoor dat meerdere MapReduce-taken worden afgesponnen voor elke Hive-query. Tez heeft niet zo'n beperking en kan complexe DAG verwerken als één taak waardoor de overhead voor het opstarten van banen wordt geminimaliseerd.
* **Vermijdt onnodige schrijfbewerkingen.** Meerdere taken worden gebruikt om dezelfde Hive-query te verwerken in de MapReduce-engine. De uitvoer van elke mapReduce-taak wordt naar HDFS geschreven voor tussenliggende gegevens. Omdat Tez het aantal taken voor elke Hive-query minimaliseert, kan het onnodige schrijfbewerkingen voorkomen.
* **Minimaliseert opstartvertragingen.** Tez is beter in staat om opstartvertraging te minimaliseren door het aantal mappers dat het moet starten te verminderen en ook de optimalisatie te verbeteren.
* **Hergebruikt containers.** Waar mogelijk kan Tez containers hergebruiken om ervoor te zorgen dat de latentie door het opstarten van containers wordt verminderd.
* **Continue optimalisatie technieken**. Traditioneel optimalisatie werd gedaan tijdens de compilatie fase. Er is echter meer informatie over de ingangen beschikbaar die een betere optimalisatie tijdens runtime mogelijk maken. Tez maakt gebruik van continue optimalisatietechnieken waarmee het plan verder in de runtimefase kan worden geoptimaliseerd.

Zie [Apache TEZ](https://tez.apache.org/)voor meer informatie over deze concepten.

U elke Hive-query Tez inschakelen door de query vooraf te maken met de volgende opdracht:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive partitionering

I/O-bewerkingen zijn het belangrijkste prestatieknelpunt voor het uitvoeren van Hive-query's. De prestaties kunnen worden verbeterd als de hoeveelheid gegevens die moet worden gelezen, kan worden verminderd. Standaard scannen Hive-query's hele Hive-tabellen. Voor query's die slechts een kleine hoeveelheid gegevens hoeven te scannen (bijvoorbeeld query's met filtering), creëert dit gedrag onnodige overhead. Hive partitionering stelt Hive-query's in staat om alleen toegang te krijgen tot de benodigde hoeveelheid gegevens in Hive-tabellen.

Hive partitionering wordt geïmplementeerd door de ruwe gegevens te reorganiseren in nieuwe mappen. Elke partitie heeft zijn eigen bestandsmap. De partitionering wordt gedefinieerd door de gebruiker. In het volgende diagram wordt het verdelen van een Hive-tabel door het *kolomjaar*geïllustreerd. Voor elk jaar wordt een nieuwe map gemaakt.

![HDInsight Apache Hive partitionering](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

Enkele overwegingen voor het partitioneren:

* **Niet onder partitie** - Partitioneren op kolommen met slechts een paar waarden kan leiden tot weinig partities. Als u bijvoorbeeld slechts twee partities op geslacht maakt die moeten worden gemaakt (mannelijk en vrouwelijk), waardoor de latentie slechts met maximaal de helft wordt verminderd.
* **Niet over partitie -** Aan de andere kant veroorzaakt het maken van een partitie op een kolom met een unieke waarde (bijvoorbeeld userid) meerdere partities. Over partitie veroorzaakt veel stress op het cluster naamknooppunt als het heeft om het grote aantal mappen te behandelen.
* **Vermijd gegevensscheefheid** - Kies uw partitioneringssleutel verstandig, zodat alle partities gelijkmatig groot zijn. Partitionering in *de kolom Status* kan bijvoorbeeld de verdeling van gegevens scheeftrekken. Aangezien de staat Californië heeft een bevolking bijna 30x die van Vermont, de partitie grootte is potentieel scheef en de prestaties kunnen enorm variëren.

Als u een partitietabel wilt maken, gebruikt u de component *Partitie per:*

```sql
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

Zodra de partitietabel is gemaakt, u statische partitionering of dynamische partitionering maken.

* **Statische partitionering** betekent dat u al gegevens hebt gehard in de juiste mappen. Met statische partities voegt u Hive-partities handmatig toe op basis van de directorylocatie. Het volgende codefragment is een voorbeeld.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **Dynamische partitionering** betekent dat u wilt dat Hive automatisch partities voor u maakt. Aangezien u de partitietabel al hebt gemaakt vanuit de faseringstabel, hoeft u alleen gegevens in te voegen in de partitietabel:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

Zie [Partitietabellen voor](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)meer informatie .

## <a name="use-the-orcfile-format"></a>De ORCFile-indeling gebruiken

Hive ondersteunt verschillende bestandsindelingen. Bijvoorbeeld:

* **Tekst:** de standaardbestandsindeling en werkt met de meeste scenario's.
* **Avro**: werkt goed voor interoperabiliteitsscenario's.
* **ORC/Parket**: het meest geschikt voor prestaties.

ORC -indeling (Optimized Row Columnar) is een zeer efficiënte manier om Hive-gegevens op te slaan. In vergelijking met andere formaten heeft ORC de volgende voordelen:

* ondersteuning voor complexe typen, waaronder DateTime en complexe en semi-gestructureerde typen.
* tot 70% compressie.
* indexeert elke 10.000 rijen, waardoor rijen kunnen worden overgeslagen.
* een aanzienlijke daling van de run-time uitvoering.

Als u ORC-indeling wilt inschakelen, maakt u eerst een tabel met de clausule *Die is opgeslagen als ORC:*

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Vervolgens voegt u gegevens in de ORC-tabel in vanuit de faseringstabel. Bijvoorbeeld:

```sql
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

U meer lezen over de ORC-indeling in de [Apache Hive Language handleiding.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)

## <a name="vectorization"></a>Vectorisatie

Vectorisatie stelt Hive in staat om een batch van 1024 rijen samen te verwerken in plaats van één rij tegelijk te verwerken. Dit betekent dat eenvoudige bewerkingen sneller worden uitgevoerd omdat er minder interne code hoeft uit te voeren.

Ga als volgt te werk om vectorisatie-voorvoegquery in te schakelen met de volgende instelling:

```hive
set hive.vectorized.execution.enabled = true;
```

Zie [Vectorized query-uitvoering](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)voor meer informatie .

## <a name="other-optimization-methods"></a>Andere optimalisatiemethoden

Er zijn meer optimalisatiemethoden die u overwegen, bijvoorbeeld:

* **Hive bucketing:** een techniek die het mogelijk maakt om grote sets gegevens te clusteren of te segmenteren om de queryprestaties te optimaliseren.
* **Join optimalisatie:** optimalisatie van hive's query uitvoeringsplanning om de efficiëntie van joins te verbeteren en de behoefte aan gebruikershints te verminderen. Zie [Optimalisatie deelnemen voor](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)meer informatie .
* **Verhoog reducers**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende algemene methoden voor het optimaliseren van hivequery's geleerd. Zie voor meer informatie de volgende artikelen:

* [Apache Hive gebruiken in HDInsight](hadoop/hdinsight-use-hive.md)
* [Gegevens over vluchtvertraging analyseren met behulp van Interactieve Query in HDInsight](/azure/hdinsight/interactive-query/interactive-query-tutorial-analyze-flight-data)
* [Twitter-gegevens analyseren met Apache Hive in HDInsight](hdinsight-analyze-twitter-data-linux.md)
