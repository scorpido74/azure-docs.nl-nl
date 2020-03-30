---
title: Azure Data Lake Storage Gen1 - prestatieafstemming
description: Beschrijft hoe u Azure Data Lake Storage Gen1 afstemmen op prestaties.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: stewu
ms.openlocfilehash: 2521700e0f07691541ee6cbbf085a8be72f08129
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904628"
---
# <a name="tune-azure-data-lake-storage-gen1-for-performance"></a>Azure Data Lake Storage Gen1 afstemmen op prestaties

Data Lake Storage Gen1 ondersteunt een hoge doorvoer voor I/O-intensieve analyses en gegevensverplaatsing. In Data Lake Storage Gen1 is het belangrijk om met alle beschikbare doorvoer – de hoeveelheid gegevens die per seconde kan worden gelezen of geschreven – te gebruiken om de beste prestaties te krijgen. Dit wordt bereikt door zoveel mogelijk lees- en schrijfbewerkingen parallel uit te voeren.

![Prestaties data lake storage Gen1](./media/data-lake-store-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen1 kan schalen om de benodigde doorvoer te bieden voor alle analysescenario's. Standaard biedt een Data Lake Storage Gen1-account automatisch voldoende doorvoer om te voldoen aan de behoeften van een brede categorie use cases. Voor de gevallen waarin klanten tegen de standaardlimiet aanlopen, kan het Data Lake Storage Gen1-account worden geconfigureerd om meer doorvoer te bieden door contact op te nemen met Microsoft-ondersteuning.

## <a name="data-ingestion"></a>Gegevensopname

Bij het innemen van gegevens van een bronsysteem naar Data Lake Storage Gen1 is het belangrijk om te overwegen dat de bronhardware, bronnetwerkhardware en netwerkconnectiviteit met Data Lake Storage Gen1 de bottleneck kunnen zijn.

![Prestaties data lake storage Gen1](./media/data-lake-store-performance-tuning-guidance/bottleneck.png)

Het is belangrijk om ervoor te zorgen dat de gegevensbeweging niet wordt beïnvloed door deze factoren.

### <a name="source-hardware"></a>Bronhardware

Of u nu on-premises machines of VM's in Azure gebruikt, u moet zorgvuldig de juiste hardware selecteren. Geef voor Source Disk Hardware de voorkeur aan HDD's boven HDD's en kies schijfhardware met snellere spindels. Gebruik voor Source Network Hardware de snelst mogelijke NIC's. Op Azure raden we Azure D14 VM's aan die over de juiste krachtige schijf- en netwerkhardware beschikken.

### <a name="network-connectivity-to-data-lake-storage-gen1"></a>Netwerkconnectiviteit met Data Lake Storage Gen1

De netwerkconnectiviteit tussen uw brongegevens en Data Lake Storage Gen1 kan soms de bottleneck zijn. Wanneer uw brongegevens on-premises zijn, u overwegen een speciale koppeling met [Azure ExpressRoute te gebruiken.](https://azure.microsoft.com/services/expressroute/) Als uw brongegevens zich in Azure bevinden, zijn de prestaties het beste wanneer de gegevens zich in dezelfde Azure-regio bevinden als het Gen1-account voor Gegevensopslag- opslag.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Gegevensopnametools configureren voor maximale parallelisatie

Nadat u de knelpunten in de bronhardware en netwerkconnectiviteit hebt opgelost, u uw hulpprogramma's configureren. In de volgende tabel worden de belangrijkste instellingen voor verschillende populaire innametools samengevat en worden diepgaande artikelen voor het afstemmen van prestaties voor hen verstrekt. Ga naar dit [artikel](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios)voor meer informatie over welke tool u moet gebruiken voor uw scenario.

| Hulpprogramma          | Instellingen | Meer details                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| PowerShell       | PerFileThreadCount, Gelijktijdigaantal | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-powershell) |
| AdlCopy    | Azure Data Lake Analytics-eenheden | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob#performance-considerations-for-using-adlcopy)         |
| DistCp            | -m (mapper) | [Koppeling](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-wasb-distcp#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallellekopieën | [Koppeling](../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (mapper) | [Koppeling](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Uw gegevensset structureren

Wanneer gegevens worden opgeslagen in Data Lake Storage Gen1, zijn de bestandsgrootte, het aantal bestanden en de mapstructuur van invloed op de prestaties. In de volgende sectie worden best practices op deze gebieden beschreven.

### <a name="file-size"></a>Bestandsgrootte

Analytics-engines zoals HDInsight en Azure Data Lake Analytics hebben doorgaans een overhead per bestand. Als u uw gegevens zoveel kleine bestanden opslaat, kan dit een negatieve invloed hebben op de prestaties.

In het algemeen, ordenen van uw gegevens in grotere formaat bestanden voor betere prestaties. Als vuistregel u gegevenssets ordenen in bestanden van 256 MB of groter. In sommige gevallen, zoals afbeeldingen en binaire gegevens, is het niet mogelijk om ze parallel te verwerken. In deze gevallen wordt aanbevolen om afzonderlijke bestanden onder 2 GB te bewaren.

Soms hebben gegevenspijplijnen beperkte controle over de ruwe gegevens die veel kleine bestanden hebben. Het wordt aanbevolen om een "koken" proces dat grotere bestanden te gebruiken voor downstream-toepassingen genereert.

### <a name="organize-time-series-data-in-folders"></a>Tijdreeksgegevens ordenen in mappen

Voor Hive- en ADLA-workloads kan het snoeien van tijdreeksgegevens door partities helpen om sommige query's slechts een subset van de gegevens te lezen, wat de prestaties verbetert.

Die pijplijnen die tijdreeksgegevens opnemen, plaatsen hun bestanden vaak met een gestructureerde naamgeving voor bestanden en mappen. Het volgende is een veelvoorkomend voorbeeld dat we zien voor gegevens die zijn gestructureerd op datum:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

De datumtijdsinformatie wordt zowel als mappen als in de bestandsnaam weergegeven.

Voor datum en tijd is het volgende een gemeenschappelijk patroon

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Nogmaals, de keuze die u maakt met de map en bestandsorganisatie moet optimaliseren voor de grotere bestandsgrootte en een redelijk aantal bestanden in elke map.

## <a name="optimize-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>I/O-intensieve taken op Hadoop- en Spark-workloads op HDInsight optimaliseren

Vacatures vallen in een van de volgende drie categorieën:

* **CPU-intensief.** Deze taken hebben lange rekentijden met minimale I/O-tijden. Voorbeelden hiervan zijn machine learning en het verwerken van natuurlijke talen.
* **Geheugenintensief.** Deze taken gebruiken veel geheugen. Voorbeelden hiervan zijn PageRank en real-time analytics-taken.
* **I/O intensief.** Deze banen besteden het grootste deel van hun tijd aan I/O. Een veelvoorkomend voorbeeld is een kopieertaak die alleen lees- en schrijfbewerkingen uitvoert. Andere voorbeelden zijn gegevensvoorbereidingstaken die veel gegevens lezen, een bepaalde gegevenstransformatie uitvoeren en vervolgens de gegevens terugschrijven naar het archief.

De volgende richtlijnen zijn alleen van toepassing op I/O-intensieve banen.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Algemene overwegingen voor een HDInsight-cluster

* **HDInsight-versies.** Gebruik voor de beste prestaties de nieuwste versie van HDInsight.
* **Regio 's.** Plaats het Data Lake Storage Gen1-account in dezelfde regio als het HDInsight-cluster.

Een HDInsight-cluster bestaat uit twee hoofdknooppunten en enkele werkknooppunten. Elk werkknooppunt biedt een specifiek aantal kernen en geheugen, dat wordt bepaald door het VM-type. Bij het uitvoeren van een taak is YARN de resourceonderhandelaar die het beschikbare geheugen en de beschikbare kernen toewijst om containers te maken. Elke container voert de taken uit die nodig zijn om de taak te voltooien. Containers lopen parallel om taken snel te verwerken. Daarom worden de prestaties verbeterd door zoveel mogelijk parallelle containers te laten uitvoeren.

Er zijn drie lagen binnen een HDInsight-cluster die kunnen worden afgestemd om het aantal containers te verhogen en alle beschikbare doorvoer te gebruiken.

* **Fysieke laag**
* **GARENlaag**
* **Werkbelastinglaag**

### <a name="physical-layer"></a>Fysieke laag

**Voer cluster uit met meer knooppunten en/of vm's van grotere grootte.** Een groter cluster zal u toelaten om meer GAREN containers draaien zoals weergegeven in de afbeelding hieronder.

![Prestaties data lake storage Gen1](./media/data-lake-store-performance-tuning-guidance/VM.png)

**Gebruik VM's met meer netwerkbandbreedte.** De hoeveelheid netwerkbandbreedte kan een knelpunt zijn als er minder netwerkbandbreedte is dan de doorvoer van Data Lake Storage Gen1. Verschillende VM's hebben verschillende netwerkbandbreedtegroottes. Kies een VM-type met de grootst mogelijke netwerkbandbreedte.

### <a name="yarn-layer"></a>GARENlaag

**Gebruik kleinere YARN containers.** Verklein de grootte van elke GAREN-container om meer containers met dezelfde hoeveelheid resources te maken.

![Prestaties data lake storage Gen1](./media/data-lake-store-performance-tuning-guidance/small-containers.png)

Afhankelijk van uw werklast is er altijd een minimale GAREN-containergrootte nodig. Als u een te kleine container kiest, lopen uw taken in problemen met het geheugen. Meestal yarn containers mogen niet kleiner zijn dan 1 GB. Het is gebruikelijk om te zien 3 GB GAREN containers. Voor sommige workloads hebt u mogelijk grotere GAREN-containers nodig.

**Verhoog de kernen per YARN-container.** Verhoog het aantal kernen dat aan elke container is toegewezen om het aantal parallelle taken dat in elke container wordt uitgevoerd, te verhogen. Dit werkt voor toepassingen zoals Spark, die meerdere taken per container uitvoeren. Voor toepassingen zoals Hive die één thread in elke container uitvoeren, is het beter om meer containers te hebben in plaats van meer kernen per container.

### <a name="workload-layer"></a>Werkbelastinglaag

**Gebruik alle beschikbare containers.** Stel het aantal taken in dat gelijk of groter is dan het aantal beschikbare containers, zodat alle resources worden gebruikt.

![Prestaties data lake storage Gen1](./media/data-lake-store-performance-tuning-guidance/use-containers.png)

**Mislukte taken zijn kostbaar.** Als elke taak een grote hoeveelheid gegevens moet verwerken, resulteert het mislukken van een taak in een dure nieuwe poging. Daarom is het beter om meer taken te maken, die elk een kleine hoeveelheid gegevens verwerken.

Naast de algemene richtlijnen hierboven, elke toepassing heeft verschillende parameters beschikbaar om af te stemmen voor die specifieke toepassing. De onderstaande tabel bevat een aantal van de parameters en links om aan de slag te gaan met prestatieafstemming voor elke toepassing.

| Workload               | Parameter om taken in te stellen                                                         |
|--------------------|-------------------------------------------------------------------------------------|
| [Spark in HDInsight](data-lake-store-performance-tuning-spark.md)  | <ul><li>Num-executeors</li><li>Executor-geheugen</li><li>Executor-cores</li></ul> |
| [Hive op HDInsight](data-lake-store-performance-tuning-hive.md)    | <ul><li>hive.tez.container.size</li></ul>         |
| [MapReduce op HDInsight](data-lake-store-performance-tuning-mapreduce.md)            | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm op HDInsight](data-lake-store-performance-tuning-storm.md)| <ul><li>Aantal werkprocessen</li><li>Aantal exemplaren van de tuituitvoerder</li><li>Aantal exemplaren van de boutuitvoerder </li><li>Aantal uitlooptaken</li><li>Aantal bouttaken</li></ul>|

## <a name="see-also"></a>Zie ook

* [Overzicht van Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Aan de slag met Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
