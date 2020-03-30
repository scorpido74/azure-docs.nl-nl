---
title: Apache Pig gebruiken
titleSuffix: Azure HDInsight
description: Meer informatie over het gebruik van Pig with Apache Hadoop op HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: ea960a92aee1c9447bb12d27cffdc42de9fd907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672120"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>Gebruik Apache Pig met Apache Hadoop op HDInsight

Meer informatie over het gebruik [van Apache Pig](https://pig.apache.org/) met HDInsight.

Apache Pig is een platform voor het maken van programma's voor Apache Hadoop met behulp van een procedurele taal bekend als *Pig Latin*. Pig is een alternatief voor Java voor het maken van *MapReduce-oplossingen* en is inbegrepen bij Azure HDInsight. Gebruik de volgende tabel om de verschillende manieren te ontdekken waarop Pig kan worden gebruikt met HDInsight:

## <a name="why-use-apache-pig"></a><a id="why"></a>Waarom Apache Pig gebruiken

Een van de uitdagingen van het verwerken van gegevens met behulp van MapReduce in Hadoop is het implementeren van uw verwerkingslogica door alleen een kaart en een reducefunctie te gebruiken. Voor complexe verwerking moet u de verwerking vaak opsplitsen in meerdere MapReduce-bewerkingen die aan elkaar zijn geketend om het gewenste resultaat te bereiken.

Met Pig u de verwerking definiëren als een reeks transformaties waar de gegevens doorheen stromen om de gewenste output te produceren.

Met de Latijnse varkenstaal u de gegevensstroom van ruwe invoer, via een of meer transformaties, beschrijven om de gewenste uitvoer te produceren. Pig Latin programma's volgen dit algemene patroon:

* **Laden**: Lees gegevens die moeten worden gemanipuleerd vanuit het bestandssysteem.

* **Transformeren:** de gegevens manipuleren.

* **Dump en bewaar:** Uitvoer gegevens naar het scherm of sla deze op voor verwerking.

### <a name="user-defined-functions"></a>Door de gebruiker gedefinieerde functies

Pig Latin ondersteunt ook door de gebruiker gedefinieerde functies (UDF), waarmee u externe componenten aanroepen die logica implementeren die moeilijk te modelleren is in Pig Latin.

Zie Pig Latin Reference [Manual 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) en [Pig Latin Reference Manual 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html)voor meer informatie over Pig Latin.

## <a name="example-data"></a><a id="data"></a>Voorbeeldgegevens

HDInsight biedt verschillende voorbeeldgegevenssets, die `/example/data` `/HdiSamples` worden opgeslagen in de mappen en mappen. Deze mappen bevinden zich in de standaardopslag voor uw cluster. In het voorbeeld Pig in dit document `/example/data/sample.log`wordt het *log4j-bestand* van .

Elk logboek in het bestand bestaat uit `[LOG LEVEL]` een veldenlijn die een veld bevat om het type en de ernst weer te geven, bijvoorbeeld:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

In het vorige voorbeeld is het logboekniveau FOUT.

> [!NOTE]  
> U ook een log4j-bestand genereren met behulp van het logboekregistratieprogramma [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) en dat bestand vervolgens uploaden naar uw blob. Zie [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md) voor instructies. Zie [Azure Blob Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md)voor meer informatie over hoe blobs in Azure-opslag worden gebruikt met HDInsight.

## <a name="example-job"></a><a id="job"></a>Voorbeeldtaak

Met de volgende functie `sample.log` Pig Latin wordt het bestand geladen vanuit de standaardopslag voor uw HDInsight-cluster. Vervolgens voert het een reeks transformaties uit die resulteren in een telling van hoe vaak elk logboekniveau in de invoergegevens heeft plaatsgevonden. De resultaten zijn geschreven naar SOAOUT.

    ```
    LOGS = LOAD 'wasb:///example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

De volgende afbeelding toont een overzicht van wat elke transformatie met de gegevens doet.

![Grafische weergave van de transformaties][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>Voer de baan Pig Latin uit

HDInsight kan Pig Latin-taken uitvoeren met behulp van verschillende methoden. Gebruik de volgende tabel om te beslissen welke methode geschikt is voor u en volg vervolgens de koppeling voor een walkthrough.

## <a name="pig-and-sql-server-integration-services"></a>Pig- en SQL Server-integratieservices

U SQL Server Integration Services (SSIS) gebruiken om een Pig-taak uit te voeren. Het Azure Feature Pack voor SSIS biedt de volgende componenten die werken met Pig-taken op HDInsight.

* [Azure HDInsight-varkenstaak][pigtask]

* [Azure Subscription Connection Manager][connectionmanager]

Lees hier meer over het Azure Feature Pack voor [SSIS.][ssispack]

## <a name="next-steps"></a><a id="nextsteps"></a>Volgende stappen

Nu u hebt geleerd hoe u Pig met HDInsight gebruiken, gebruikt u de volgende koppelingen om andere manieren te verkennen om met Azure HDInsight te werken.

* [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md)
* [Apache Hive gebruiken met HDInsight](./hadoop/hdinsight-use-hive.md)
* [Apache Sqoop gebruiken met HDInsight](hdinsight-use-sqoop.md)
* [MapReduce-taken gebruiken met HDInsight](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
