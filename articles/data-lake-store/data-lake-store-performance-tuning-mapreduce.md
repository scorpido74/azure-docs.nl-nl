---
title: Azure Data Lake Storage Gen1 performance tuning - MapReduce
description: Azure Data Lake Storage Gen1 MapReduce Performance Tuning Guidelines
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: a645049665bc1d51efa94a879b9d2e4e5529282f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904592"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Richtlijnen voor prestatieafstemming voor kaartverminderen op HDInsight en Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen1-account. Zie [Een HDInsight-cluster maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat u Extern bureaublad inschakelt voor het cluster.
* **MapReduce gebruiken op HDInsight**. Zie [MapReduce gebruiken in Hadoop op HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce) voor meer informatie
* **Bekijk richtlijnen voor prestatieafstemming voor Data Lake Storage Gen1**. Zie Richtlijnen voor [de prestaties van Data Lake Storage Gen1 Performance Tuning voor](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) algemene prestatieconcepten

## <a name="parameters"></a>Parameters

Bij het uitvoeren van MapReduce-taken zijn dit de belangrijkste parameters die u configureren om de prestaties op Data Lake Storage Gen1 te verhogen:

|Parameter      | Beschrijving  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  De hoeveelheid geheugen die aan elke mapper moet worden toegewezen.  |
|`Mapreduce.job.maps`     |  Het aantal kaarttaken per taak.  |
|`Mapreduce.reduce.memory.mb`     |  De hoeveelheid geheugen die aan elke reducer moet worden toegewezen.  |
|`Mapreduce.job.reduces`    |   Het aantal taken per taak verminderen.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>Mapreduce.map.memory / Mapreduce.reduce.memory

Pas dit getal aan op basis van hoeveel geheugen nodig is voor de kaart en/of de taak te verminderen. U de standaardwaarden van `mapreduce.map.memory` en `mapreduce.reduce.memory` in Ambari bekijken via de garenconfiguratie. Navigeer in Ambari naar YARN en bekijk het tabblad **Configs.** Het YARN-geheugen wordt weergegeven.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>Mapreduce.job.maps / Mapreduce.job.reduces

Dit bepaalt het maximum aantal mappers of reducers om te maken. Het aantal splitsingen bepaalt hoeveel mappers er zijn gemaakt voor de taak MapReduce. Daarom u minder mappers krijgen dan u had gevraagd als er minder splitsingen zijn dan het aantal gevraagde mappers.

## <a name="guidance"></a>Richtlijnen

### <a name="step-1-determine-number-of-jobs-running"></a>Stap 1: Aantal taken bepalen dat wordt uitgevoerd

MapReduce gebruikt standaard het hele cluster voor uw taak. U minder van het cluster gebruiken door minder mappers te gebruiken dan er beschikbare containers zijn. De richtlijnen in dit document gaan ervan uit dat uw toepassing de enige toepassing is die op uw cluster wordt uitgevoerd.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Stap 2: Mapreduce.map.memory/mapreduce.reduce.memory instellen

De grootte van het geheugen voor kaart- en takenvermindering is afhankelijk van uw specifieke taak. U de geheugengrootte verkleinen als u de gelijktijdigheid wilt verhogen. Het aantal gelijktijdig lopende taken is afhankelijk van het aantal containers. Door de hoeveelheid geheugen per mapper of reducer te verminderen, kunnen meer containers worden gemaakt, waardoor meer mappers of reducers gelijktijdig kunnen worden uitgevoerd. Het verminderen van de hoeveelheid geheugen te veel kan leiden tot sommige processen opraken van het geheugen. Als u een heapfout krijgt bij het uitvoeren van uw taak, verhoogt u het geheugen per mapper of reducer. Houd er rekening mee dat het toevoegen van meer containers extra overhead toevoegt voor elke extra container, wat de prestaties mogelijk kan verslechteren. Een ander alternatief is om meer geheugen te krijgen door een cluster te gebruiken met hogere hoeveelheden geheugen of het aantal knooppunten in uw cluster te verhogen. Meer geheugen zal het mogelijk maken meer containers te gebruiken, wat betekent dat meer gelijktijdigheid.

### <a name="step-3-determine-total-yarn-memory"></a>Stap 3: Bepaal het totale YARN-geheugen

Als u mapreduce.job.maps/mapreduce.job.reduces wilt afstemmen, moet u rekening houden met de hoeveelheid totaal GAREN-geheugen dat beschikbaar is voor gebruik. Deze informatie is beschikbaar in Ambari. Navigeer naar YARN en bekijk het tabblad **Configs.** Het YARN-geheugen wordt in dit venster weergegeven. Vermenigvuldig het YARN-geheugen met het aantal knooppunten in uw cluster om het totale YARN-geheugen te krijgen.

`Total YARN memory = nodes * YARN memory per node`

Als u een leeg cluster gebruikt, kan het geheugen het totale GAREN-geheugen voor uw cluster zijn. Als andere toepassingen geheugen gebruiken, u ervoor kiezen om slechts een deel van het geheugen van uw cluster te gebruiken door het aantal mappers of reducers te verminderen tot het aantal containers dat u wilt gebruiken.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Stap 4: Aantal GARENcontainers berekenen

GAREN containers dicteren de hoeveelheid gelijktijdigheid beschikbaar voor de taak. Neem het totale GAREN-geheugen en deel dat door mapreduce.map.memory.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Stap 5: Mapreduce.job.maps/mapreduce.job.reduces instellen

Stel mapreduce.job.maps/mapreduce.job.reduces in tot ten minste het aantal beschikbare containers. U verder experimenteren door het aantal mappers en reducers te verhogen om te zien of u betere prestaties krijgt. Houd er rekening mee dat meer mappers extra overhead hebben, zodat het hebben van te veel mappers de prestaties kan verslechteren.

CPU-planning en CPU-isolatie zijn standaard uitgeschakeld, zodat het aantal GAREN-containers wordt beperkt door het geheugen.

## <a name="example-calculation"></a>Voorbeeldberekening

Stel dat u momenteel een cluster hebt dat bestaat uit 8 D14-knooppunten en dat u een I/O-intensieve taak wilt uitvoeren. Hier zijn de berekeningen die u moet doen:

### <a name="step-1-determine-number-of-jobs-running"></a>Stap 1: Aantal taken bepalen dat wordt uitgevoerd

Voor ons voorbeeld gaan we ervan uit dat onze taak de enige is die draait.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Stap 2: Mapreduce.map.memory/mapreduce.reduce.memory instellen

Voor ons voorbeeld voert u een I/O-intensieve taak uit en besluit u dat 3 GB geheugen voor kaarttaken voldoende is.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Stap 3: Bepaal het totale YARN-geheugen

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Stap 4: Bereken # van GAREN containers

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Stap 5: Mapreduce.job.maps/mapreduce.job.reduces instellen

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Beperkingen

**Data Lake Storage Gen1 throttling**

Als multitenant-service stelt Data Lake Storage Gen1 bandbreedtelimieten op accountniveau in. Als u deze limieten bereikt, begint u taakfouten te zien. Dit kan worden geïdentificeerd door het observeren van throttling fouten in taaklogboeken. Als je meer bandbreedte nodig hebt voor je werk, neem dan contact met ons op.

Om te controleren of u wordt gewurgd, moet u de foutopsporingsregistratie aan de clientzijde inschakelen. Zo kun je dat doen:

1. Plaats de volgende eigenschap in de log4j eigenschappen in Ambari > YARN > Config > Advanced yarn-log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Start alle knooppunten/service opnieuw om de config van kracht te laten worden.

3. Als u wordt gewurgd, ziet u de HTTP 429-foutcode in het YARN-logboekbestand. Het YARN log bestand bevindt&lt;&gt;zich in /tmp/user /yarn.log

## <a name="examples-to-run"></a>Voorbeelden om uit te voeren

Als u wilt aantonen hoe MapReduce wordt uitgevoerd op Data Lake Storage Gen1, volgt een voorbeeldcode die is uitgevoerd op een cluster met de volgende instellingen:

* 16 knooppunt D14v2
* Hadoopcluster met HDI 3.6

Voor een startpunt zijn hier enkele voorbeeldopdrachten voor het uitvoeren van MapReduce Teragen, Terasort en Teravalidate. U deze opdrachten aanpassen op basis van uw resources.

### <a name="teragen"></a>Teragen Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teraongeldig

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```
