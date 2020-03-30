---
title: 'Prestaties afstemmen: Hive, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Documenten'
description: Richtlijnen voor azure Data Lake Storage Gen2 Hive Prestaties afstemmen.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 66042568cede364c16302fbd85751de4113bbe0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327588"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Prestaties afstemmen: Hive, HDInsight & Azure Data Lake Storage Gen2

De standaardinstellingen zijn ingesteld om goede prestaties te leveren in veel verschillende use cases.  Voor I/O-intensieve query's kan Hive worden afgestemd om betere prestaties te krijgen met Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage Gen2-account**. Zie [Snelstart: Een Azure Data Lake Storage Gen2-opslagaccount maken voor](data-lake-storage-quickstart-create-account.md) instructies over het maken van een azure data lake storage Gen2-opslagaccount
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen2-account. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Running Hive op HDInsight**.  Zie Hive gebruiken [op HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive) voor meer informatie over het uitvoeren van Hive-taken op HDInsight
* **Richtlijnen voor prestatieafstemming voor Data Lake Storage Gen2**.  Zie Richtlijnen voor [de prestaties van Data Lake Storage Gen2 Performance Tuning voor](data-lake-storage-performance-tuning-guidance.md) algemene prestatieconcepten

## <a name="parameters"></a>Parameters

Dit zijn de belangrijkste instellingen om af te stemmen op de verbeterde Data Lake Storage Gen2-prestaties:

* **hive.tez.container.size** – de hoeveelheid geheugen die door elke taak wordt gebruikt

* **tez.grouping.min-size** – minimale grootte van elke mapper

* **tez.grouping.max-size** – maximale grootte van elke mapper

* **hive.exec.reducer.bytes.per.reducer** – grootte van elke reducer

**hive.tez.container.size** - De containergrootte bepaalt hoeveel geheugen beschikbaar is voor elke taak.  Dit is de belangrijkste input voor het beheersen van de gelijktijdigheid in Hive.  

**tez.grouping.min-size** – Met deze parameter u de minimale grootte van elke mapper instellen.  Als het aantal mappers dat Tez kiest kleiner is dan de waarde van deze parameter, gebruikt Tez hier de waardeset.

**tez.grouping.max-size** – Met de parameter u de maximale grootte van elke mapper instellen.  Als het aantal mappers dat Tez kiest groter is dan de waarde van deze parameter, gebruikt Tez de waarde die hier is ingesteld.

**hive.exec.reducer.bytes.per.reducer** – Deze parameter stelt de grootte van elke reducer in.  Standaard is elke reducer 256MB.  

## <a name="guidance"></a>Richtlijnen

**Stel hive.exec.reducer.bytes.per.reducer** in - De standaardwaarde werkt goed wanneer de gegevens niet zijn gecomprimeerd.  Voor gegevens die worden gecomprimeerd, moet u de grootte van de reducer verkleinen.  

**Stel hive.tez.container.size** in - In elk knooppunt wordt het geheugen opgegeven door yarn.nodemanager.resource.memory-mb en moet het standaard correct worden ingesteld op het HDI-cluster.  Zie dit [bericht](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)voor meer informatie over het instellen van het juiste geheugen in YARN.

I/O-intensieve workloads kunnen profiteren van meer parallellisme door de grootte van de Tez-container te verkleinen. Dit geeft de gebruiker meer containers die gelijktijdigheid verhoogt.  Sommige Hive-query's vereisen echter een aanzienlijke hoeveelheid geheugen (bijvoorbeeld MapJoin).  Als de taak niet genoeg geheugen heeft, krijgt u tijdens runtime een uitzondering voor het geheugen.  Als u geen geheugenuitzonderingen ontvangt, moet u het geheugen verhogen.   

Het gelijktijdige aantal taken dat wordt uitgevoerd of parallellisme wordt begrensd door het totale YARN-geheugen.  Het aantal GAREN-containers bepaalt hoeveel gelijktijdige taken kunnen worden uitgevoerd.  Om het YARN-geheugen per knooppunt te vinden, u naar Ambari gaan.  Navigeer naar YARN en bekijk het tabblad Configs.  Het YARN-geheugen wordt in dit venster weergegeven.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
De sleutel tot het verbeteren van de prestaties met Data Lake Storage Gen2 is om de gelijktijdigheid zo veel mogelijk te verhogen.  Tez berekent automatisch het aantal taken dat moet worden gemaakt, zodat u deze niet hoeft in te stellen.   

## <a name="example-calculation"></a>Voorbeeldberekening

Stel dat u een D14-cluster met 8 node hebt.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Meer informatie over Hive tuning

Hier zijn een paar blogs die zullen helpen bij het afstemmen van uw Hive query's:
* [Hive-query's optimaliseren voor Hadoop in HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Problemen met de prestaties van Hive-query's oplossen](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite talk on optimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
