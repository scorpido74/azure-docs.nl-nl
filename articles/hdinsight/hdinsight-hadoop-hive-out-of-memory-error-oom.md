---
title: Een Hive oplossen uit geheugenfout in Azure HDInsight
description: Fix a Hive out of memory error in HDInsight. Het klantscenario is een query voor veel grote tabellen.
keywords: uit geheugenfout, OOM, Hive-instellingen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687774"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Een Apache Hive oplossen uit geheugenfout in Azure HDInsight

Meer informatie over het oplossen van een Fout van Apache Hive uit het geheugen (OOM) bij het verwerken van grote tabellen door Hive-geheugeninstellingen te configureren.

## <a name="run-apache-hive-query-against-large-tables"></a>Apache Hive-query uitvoeren tegen grote tabellen

Een klant heeft een Hive-query uitgevoerd:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Enkele nuances van deze query:

* T1 is een alias voor een grote tabel, TABLE1, die veel tekenreekskolomtypen heeft.
* Andere tabellen zijn niet zo groot, maar hebben wel veel kolommen.
* Alle tabellen voegen zich bij elkaar, in sommige gevallen met meerdere kolommen in TABEL1 en andere.

De Hive-query duurde 26 minuten om te voltooien op een 24-knooppunt A3 HDInsight-cluster. De klant merkte de volgende waarschuwingsberichten op:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Met behulp van de Apache Tez uitvoeringsmotor. Dezelfde query liep voor 15 minuten, en vervolgens gooide de volgende fout:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

De fout blijft bij het gebruik van een grotere virtuele machine (bijvoorbeeld D12).

## <a name="debug-the-out-of-memory-error"></a>Fout loos geheugen debuggen

Onze ondersteuning en engineering teams samen gevonden een van de problemen waardoor de out of memory fout was een [bekend probleem beschreven in de Apache JIRA:](https://issues.apache.org/jira/browse/HIVE-8306)

"Wanneer hive.auto.convert.join.noconditionaltask = true controleren we noconditionaltask.size en als de som van de tabellen grootte in de kaart join is minder dan noconditionaltask.size het plan zou genereren een kaart join, het probleem met deze is dat de berekening niet te nemen rekening houdend met de overhead die door verschillende HashTable-implementatie wordt geïntroduceerd als resultaten als de som van de invoergrootte kleiner is dan de noconditionaltaskgrootte door een kleine margequery's, zal OOM raken."

De **taak hive.auto.convert.join.noconditionaltask** in het bestand hive-site.xml is ingesteld op **true:**

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Het is waarschijnlijk kaart join was de oorzaak van de Java Heap Space uit het geheugen fout. Zoals uitgelegd in de blog post [Hadoop Garen geheugen instellingen in HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), wanneer Tez uitvoering motor wordt gebruikt de heap ruimte gebruikt eigenlijk behoort tot de Tez container. Zie de volgende afbeelding waarin het tez-containergeheugen wordt beschreven.

![Tez-containergeheugendiagram: Hive out of memory error](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Zoals de blogpost suggereert, definiëren de volgende twee geheugeninstellingen het containergeheugen voor de heap: **hive.tez.container.size** en **hive.tez.java.opts**. Uit onze ervaring, de out of memory uitzondering betekent niet dat de container grootte is te klein. Het betekent dat de Java heap grootte (hive.tez.java.opts) is te klein. Dus wanneer je ziet uit het geheugen, u proberen om **hive.tez.java.opts**te verhogen. Indien nodig moet u misschien **hive.tez.container.size**verhogen. De **instelling java.opts** moet ongeveer 80% van **container.size**zijn.

> [!NOTE]  
> De instelling **hive.tez.java.opts** moet altijd kleiner zijn dan **hive.tez.container.size**.

Omdat een D12-machine 28 GB geheugen heeft, hebben we besloten om een containergrootte van 10 GB (10240 MB) te gebruiken en 80% toe te wijzen aan java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Met de nieuwe instellingen is de query in minder dan 10 minuten uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Het krijgen van een OOM-fout betekent niet noodzakelijkerwijs dat de containergrootte te klein is. In plaats daarvan moet u de geheugeninstellingen zo configureren dat de heapgrootte wordt vergroot en ten minste 80% van de grootte van het containergeheugen is. Zie [Apache Hive-query's optimaliseren voor Apache Hadoop in HDInsight voor](hdinsight-hadoop-optimize-hive-query.md)het optimaliseren van Hive-query's.
