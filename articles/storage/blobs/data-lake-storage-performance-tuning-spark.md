---
title: 'Prestaties afstemmen: Spark, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Documenten'
description: Richtlijnen voor azure Data Lake Storage Gen2 Spark-prestaties
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a70b8112af201a49e7eece8b689e75102ec55880
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327550"
---
# <a name="tune-performance-spark-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: Spark, HDInsight & Azure Data Lake Storage Gen2

Wanneer u de prestaties op Spark afstemt, moet u rekening houden met het aantal apps dat op uw cluster wordt uitgevoerd.  Standaard u 4 apps tegelijk uitvoeren op uw HDI-cluster (Let op: de standaardinstelling kan worden gewijzigd).  U besluiten om minder apps te gebruiken, zodat u de standaardinstellingen overschrijven en meer van het cluster voor die apps gebruiken.  

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure Data Lake Storage Gen2-account**. Zie [Snelstart: Een Azure Data Lake Storage Gen2-opslagaccount](data-lake-storage-quickstart-create-account.md)maken voor instructies over het maken van een azure data lake storage Gen2.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Zorg ervoor dat u Extern bureaublad inschakelt voor het cluster.
* **Spark-cluster uitvoeren op Data Lake Storage Gen2**.  Zie [HDInsight Spark-cluster gebruiken om gegevens te analyseren in Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-use-with-data-lake-store) voor meer informatie
* **Richtlijnen voor prestatieafstemming voor Data Lake Storage Gen2**.  Zie Richtlijnen voor [de prestaties van Data Lake Storage Gen2 Performance Tuning voor](data-lake-storage-performance-tuning-guidance.md) algemene prestatieconcepten 

## <a name="parameters"></a>Parameters

Bij het uitvoeren van Spark-taken zijn hier de belangrijkste instellingen die kunnen worden afgestemd om de prestaties op Data Lake Storage Gen2 te verhogen:

* **Num-executors** - Het aantal gelijktijdige taken dat kan worden uitgevoerd.

* **Executor-memory** - De hoeveelheid geheugen die aan elke executeur is toegewezen.

* **Executor-cores** - Het aantal cores toegewezen aan elke executeur.                     

**Num-executeors** Num-executeors stellen het maximum aantal taken in dat parallel kan worden uitgevoerd.  Het werkelijke aantal taken dat parallel kan worden uitgevoerd, wordt begrensd door het geheugen en de CPU-bronnen die beschikbaar zijn in uw cluster.

**Executor-geheugen** Dit is de hoeveelheid geheugen die wordt toegewezen aan elke executeur.  Het geheugen dat nodig is voor elke uitvoerder is afhankelijk van de taak.  Voor complexe bewerkingen moet het geheugen hoger zijn.  Voor eenvoudige bewerkingen zoals lezen en schrijven zijn de geheugenvereisten lager.  De hoeveelheid geheugen voor elke executeur kan worden bekeken in Ambari.  Navigeer in Ambari naar Spark en bekijk het tabblad Configs.  

**Executor-cores** Hiermee wordt het aantal cores ingesteld dat per executeur wordt gebruikt, dat het aantal parallelle threads bepaalt dat per executeur kan worden uitgevoerd.  Als executor-cores = 2 bijvoorbeeld worden uitgevoerd, kan elke uitvoerder 2 parallelle taken uitvoeren in de executor.  De benodigde executeur-cores zijn afhankelijk van de taak.  Voor zware taken van i/o's is geen grote hoeveelheid geheugen per taak vereist, zodat elke uitvoerder meer parallelle taken kan verwerken.

Standaard worden twee virtuele YARN-kernen gedefinieerd voor elke fysieke kern wanneer spark op HDInsight wordt uitgevoerd.  Dit getal biedt een goede balans van gelijktijdigheid en de hoeveelheid context die overschakelt van meerdere threads.  

## <a name="guidance"></a>Richtlijnen

Tijdens het uitvoeren van Spark-analytische workloads om met gegevens te werken in Data Lake Storage Gen2, raden we u aan de meest recente HDInsight-versie te gebruiken om de beste prestaties te leveren met Data Lake Storage Gen2. Wanneer uw taak meer I/O-intensief is, kunnen bepaalde parameters worden geconfigureerd om de prestaties te verbeteren.  Data Lake Storage Gen2 is een zeer schaalbaar opslagplatform dat hoge doorvoer aankan.  Als de taak voornamelijk bestaat uit lezen of schrijven, kan het verhogen van de gelijktijdigheid voor I/O van en naar Data Lake Storage Gen2 de prestaties verhogen.

Er zijn een paar algemene manieren om gelijktijdigheid voor I/O-intensieve banen te verhogen.

**Stap 1: Bepaal hoeveel apps er op uw cluster worden uitgevoerd** : u moet weten hoeveel apps er op het cluster worden uitgevoerd, waaronder het huidige.  De standaardwaarden voor elke Spark-instelling gaan ervan uit dat er 4 apps tegelijk worden uitgevoerd.  Daarom hebt u slechts 25% van het cluster beschikbaar voor elke app.  Om betere prestaties te krijgen, u de standaardinstellingen overschrijven door het aantal uitvoerders te wijzigen.  

**Stap 2: Stel executor-geheugen in** – Het eerste wat u moet instellen is het executor-geheugen.  Het geheugen zal afhankelijk zijn van de taak die u gaat uitvoeren.  U de gelijktijdigheid verhogen door minder geheugen per executeur toe te wijzen.  Als u uit het geheugen uitzonderingen ziet wanneer u uw taak uitvoert, moet u de waarde voor deze parameter verhogen.  Een alternatief is om meer geheugen te krijgen met behulp van een cluster dat hogere hoeveelheden geheugen heeft of het vergroten van de grootte van uw cluster.  Meer geheugen zal het mogelijk maken meer uitvoerders te gebruiken, wat betekent meer gelijktijdigheid.

**Stap 3: Stel executor-cores in** – Voor I/O-intensieve workloads die geen complexe bewerkingen hebben, is het goed om te beginnen met een groot aantal executor-cores om het aantal parallelle taken per executeur te verhogen.  Het instellen van executor-cores op 4 is een goed begin.   

    executor-cores = 4
Het verhogen van het aantal executor-cores geeft je meer parallellisme, zodat je experimenteren met verschillende executor-cores.  Voor taken met complexere bewerkingen moet u het aantal cores per uitvoerder verminderen.  Als executor-cores hoger is ingesteld dan 4, kan garbage collection inefficiënt worden en de prestaties verslechteren.

**Stap 4: Bepaal de hoeveelheid GAREN-geheugen in het cluster** – Deze informatie is beschikbaar in Ambari.  Navigeer naar YARN en bekijk het tabblad Configs.  Het YARN-geheugen wordt in dit venster weergegeven.  
Opmerking terwijl u zich in het venster bevindt, u ook de standaardgrootte van de GAREN-container zien.  De grootte van de YARN-container is hetzelfde als de parameter geheugen per executeur.

    Total YARN memory = nodes * YARN memory per node
**Stap 5: Num-executors berekenen**

**Geheugenbeperking berekenen** - De parameter num-executors wordt beperkt door het geheugen of door CPU.  De geheugenbeperking wordt bepaald door de hoeveelheid beschikbaar GAREN-geheugen voor uw toepassing.  U moet nemen totale YARN geheugen en delen dat door executor-geheugen.  De beperking moet worden gedeschaald voor het aantal apps, zodat we delen door het aantal apps.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
**CPU-beperking berekenen** - De CPU-beperking wordt berekend als de totale virtuele kernen gedeeld door het aantal cores per executor.  Er zijn 2 virtuele kernen voor elke fysieke kern.  Net als bij de geheugenbeperking moeten we delen door het aantal apps.

    virtual cores = (nodes in cluster * # of physical cores in node * 2)
    CPU constraint = (total virtual cores / # of cores per executor) / # of apps
**Num-executors instellen** : de parameter num-executors wordt bepaald door het minimum van de geheugenbeperking en de CPU-beperking te nemen. 

    num-executors = Min (total virtual Cores / # of cores per executor, available YARN memory / executor-memory)   
Het instellen van een hoger aantal num-executors verhoogt niet noodzakelijkerwijs de prestaties.  U moet er rekening mee houden dat het toevoegen van meer uitvoerders extra overhead toevoegt voor elke extra executor, die de prestaties mogelijk kan verslechteren.  Num-executeors wordt begrensd door de clusterbronnen.    

## <a name="example-calculation"></a>Voorbeeldberekening

Stel dat u momenteel een cluster hebt dat bestaat uit 8 D4v2-knooppunten waarop 2 apps worden uitgevoerd, waaronder de cluster die u gaat uitvoeren.  

**Stap 1: Bepaal hoeveel apps er op je cluster worden uitgevoerd:** je weet dat je twee apps op je cluster hebt, waaronder de apps die je gaat uitvoeren.  

**Stap 2: Stel executor-geheugen** in – voor dit voorbeeld bepalen we dat 6 GB executorgeheugen voldoende is voor een I/O-intensieve taak.  

    executor-memory = 6GB
**Stap 3: Stel executor-cores in** – Aangezien dit een I/O-intensieve taak is, kunnen we het aantal cores voor elke executeur instellen op 4.  Het instellen van kernen per uitvoerder op groter dan 4 kan problemen veroorzaken bij het ophalen van afval.  

    executor-cores = 4
**Stap 4: Bepaal de hoeveelheid GAREN-geheugen in het cluster** - We navigeren naar Ambari om erachter te komen dat elke D4v2 25 GB GAREN-geheugen heeft.  Aangezien er 8 knooppunten zijn, wordt het beschikbare YARN-geheugen vermenigvuldigd met 8.

    Total YARN memory = nodes * YARN memory* per node
    Total YARN memory = 8 nodes * 25GB = 200GB
**Stap 5: Num-executors berekenen** : de parameter num-executors wordt bepaald door het minimum van de geheugenbeperking en de CPU-beperking te nemen gedeeld door het aantal apps dat op Spark wordt uitgevoerd.    

**Geheugenbeperking berekenen** : de geheugenbeperking wordt berekend als het totale YARN-geheugen gedeeld door het geheugen per uitvoerder.

    Memory constraint = (total YARN memory / executor memory) / # of apps   
    Memory constraint = (200GB / 6GB) / 2   
    Memory constraint = 16 (rounded)
**CPU-beperking berekenen** - De CPU-beperking wordt berekend als de totale garenkernen gedeeld door het aantal cores per executor.
    
    YARN cores = nodes in cluster * # of cores per node * 2   
    YARN cores = 8 nodes * 8 cores per D14 * 2 = 128
    CPU constraint = (total YARN cores / # of cores per executor) / # of apps
    CPU constraint = (128 / 4) / 2
    CPU constraint = 16
**Num-executors instellen**

    num-executors = Min (memory constraint, CPU constraint)
    num-executors = Min (16, 16)
    num-executors = 16    

