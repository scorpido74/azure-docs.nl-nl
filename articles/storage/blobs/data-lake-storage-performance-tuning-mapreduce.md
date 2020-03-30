---
title: 'Prestaties afstemmen: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Documenten'
description: Azure Data Lake Storage Gen2 MapReduce Prestatietuning Richtlijnen
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327928"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Begrijp de factoren waarmee u rekening moet houden wanneer u de prestaties van Map Reduce-taken afstemt. Dit artikel behandelt een reeks richtlijnen voor prestatieafstemming.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure Data Lake Storage Gen2-account**. Zie [Snelstart: Een Azure Data Lake Storage Gen2-opslagaccount](data-lake-storage-quickstart-create-account.md)maken voor instructies over het maken van een azure data lake storage Gen2.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **MapReduce gebruiken op HDInsight**.  Zie [MapReduce gebruiken in Hadoop op HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce) voor meer informatie
* **Richtlijnen voor prestatieafstemming voor Data Lake Storage Gen2**.  Zie Richtlijnen voor [de prestaties van Data Lake Storage Gen2 Performance Tuning voor](data-lake-storage-performance-tuning-guidance.md) algemene prestatieconcepten

## <a name="parameters"></a>Parameters

Bij het uitvoeren van MapReduce-taken zijn dit de parameters die u configureren om de prestaties op Data Lake Storage Gen2 te verhogen:

* **Mapreduce.map.memory.mb** – De hoeveelheid geheugen die aan elke mapper moet worden toegewezen
* **Mapreduce.job.maps** – Het aantal kaarttaken per taak
* **Mapreduce.reduce.memory.mb** – De hoeveelheid geheugen die aan elke reducer moet worden toegewezen
* **Mapreduce.job.reduces** – Het aantal taken per taak verminderen

**Mapreduce.map.memory / Mapreduce.reduce.memory** Dit nummer moet worden aangepast op basis van hoeveel geheugen nodig is voor de kaart en/of de taak te verminderen.  De standaardwaarden van mapreduce.map.memory en mapreduce.reduce.memory kunnen in Ambari worden bekeken via de garenconfiguratie.  Navigeer in Ambari naar YARN en bekijk het tabblad Configs.  Het YARN-geheugen wordt weergegeven.  

**Mapreduce.job.maps / Mapreduce.job.reduces Mapreduce.job.** Dit bepaalt het maximum aantal mappers of reducers dat moet worden gemaakt.  Het aantal splitsingen bepaalt hoeveel mappers er worden gemaakt voor de mapReduce-taak.  Daarom u minder mappers krijgen dan u had gevraagd als er minder splitsingen zijn dan het aantal gevraagde mappers.       

## <a name="guidance"></a>Richtlijnen

> [!NOTE]
> De richtlijnen in dit document gaan ervan uit dat uw toepassing de enige toepassing is die op uw cluster wordt uitgevoerd.

**Stap 1: Aantal taken bepalen dat wordt uitgevoerd**

MapReduce gebruikt standaard het hele cluster voor uw taak.  U minder van het cluster gebruiken door minder mappers te gebruiken dan er beschikbare containers zijn.        

**Stap 2: Mapreduce.map.memory/mapreduce.reduce.memory instellen**

De grootte van het geheugen voor kaart- en takenvermindering is afhankelijk van uw specifieke taak.  U de geheugengrootte verkleinen als u de gelijktijdigheid wilt verhogen.  Het aantal gelijktijdig lopende taken is afhankelijk van het aantal containers.  Door de hoeveelheid geheugen per mapper of reducer te verminderen, kunnen meer containers worden gemaakt, waardoor meer mappers of reducers gelijktijdig kunnen worden uitgevoerd.  Het verminderen van de hoeveelheid geheugen te veel kan leiden tot sommige processen opraken van het geheugen.  Als u een heapfout krijgt bij het uitvoeren van uw taak, moet u het geheugen per mapper of reducer verhogen.  U moet er rekening mee houden dat het toevoegen van meer containers extra overhead toevoegt voor elke extra container, wat de prestaties mogelijk kan verslechteren.  Een ander alternatief is om meer geheugen te krijgen door een cluster te gebruiken met hogere hoeveelheden geheugen of het aantal knooppunten in uw cluster te verhogen.  Meer geheugen zal het mogelijk maken meer containers te gebruiken, wat betekent dat meer gelijktijdigheid.  

**Stap 3: Bepaal totaal GAREN-geheugen**

Om mapreduce.job.maps/mapreduce.job.reduces af te stemmen, moet u rekening houden met de hoeveelheid totaal GAREN-geheugen dat beschikbaar is voor gebruik.  Deze informatie is beschikbaar in Ambari.  Navigeer naar YARN en bekijk het tabblad Configs.  Het YARN-geheugen wordt in dit venster weergegeven.  U moet het YARN-geheugen vermenigvuldigen met het aantal knooppunten in uw cluster om het totale YARN-geheugen te krijgen.

    Total YARN memory = nodes * YARN memory per node

Als u een leeg cluster gebruikt, kan het geheugen het totale GAREN-geheugen voor uw cluster zijn.  Als andere toepassingen geheugen gebruiken, u ervoor kiezen om slechts een deel van het geheugen van uw cluster te gebruiken door het aantal mappers of reducers te verminderen tot het aantal containers dat u wilt gebruiken.  

**Stap 4: Aantal GARENcontainers berekenen**

GAREN containers dicteren de hoeveelheid gelijktijdigheid beschikbaar voor de taak.  Neem het totale GAREN-geheugen en deel dat door mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Stap 5: Mapreduce.job.maps/mapreduce.job.reduces instellen**

Stel mapreduce.job.maps/mapreduce.job.reduces in tot ten minste het aantal beschikbare containers.  U verder experimenteren door het aantal mappers en reducers te verhogen om te zien of u betere prestaties krijgt.  Houd er rekening mee dat meer mappers extra overhead hebben, zodat het hebben van te veel mappers de prestaties kan verslechteren.  

CPU-planning en CPU-isolatie zijn standaard uitgeschakeld, zodat het aantal GAREN-containers wordt beperkt door het geheugen.

## <a name="example-calculation"></a>Voorbeeldberekening

Laten we aannemen dat we een cluster hebben bestaande uit 8 D14-knooppunten en dat we een I/O-intensieve taak willen uitvoeren.  Hier zijn de berekeningen die u moet doen:

**Stap 1: Aantal taken bepalen dat wordt uitgevoerd**

Laten we in dit voorbeeld aannemen dat onze taak de enige taak is die wordt uitgevoerd.  

**Stap 2: Mapreduce.map.memory/mapreduce.reduce.memory instellen**

In dit voorbeeld hebben we een I/O-intensieve taak en besluiten we dat 3 GB geheugen voor kaarttaken voldoende is.

    mapreduce.map.memory = 3GB

**Stap 3: Bepaal totaal GAREN-geheugen**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Stap 4: Bereken # van GAREN containers**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Stap 5: Mapreduce.job.maps/mapreduce.job.reduces instellen**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Voorbeelden om uit te voeren

Als u wilt laten zien hoe MapReduce wordt uitgevoerd op Data Lake Storage Gen2, vindt u hieronder een voorbeeldcode die is uitgevoerd op een cluster met de volgende instellingen:

* 16 knooppunt D14v2
* Hadoopcluster met HDI 3.6

Voor een startpunt zijn hier enkele voorbeeldopdrachten voor het uitvoeren van MapReduce Teragen, Terasort en Teravalidate.  U deze opdrachten aanpassen op basis van uw resources.

**Teragen Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teraongeldig**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
