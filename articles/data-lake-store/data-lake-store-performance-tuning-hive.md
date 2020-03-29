---
title: Richtlijnen voor prestatieafstemming azure Data Lake Storage Gen1 Hive | Microsoft Documenten
description: Richtlijnen voor prestatieafstemming azure Data Lake Storage Gen1 Hive
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61437273"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Richtlijnen voor prestatieafstemming voor Hive op HDInsight en Azure Data Lake Storage Gen1

De standaardinstellingen zijn ingesteld om goede prestaties te leveren in veel verschillende use cases.  Voor I/O-intensieve query's kan Hive worden afgestemd om betere prestaties te krijgen met Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Data Lake Storage Gen1-account**. Zie [Aan de slag met Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage Gen1-account. Zie [Een HDInsight-cluster maken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat u Extern bureaublad inschakelt voor het cluster.
* **Running Hive op HDInsight**.  Zie Hive gebruiken [op HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive) voor meer informatie over het uitvoeren van Hive-taken op HDInsight
* **Richtlijnen voor prestatieafstemming voor Data Lake Storage Gen1**.  Zie Richtlijnen voor [de prestaties van Data Lake Storage Gen1 Performance Tuning voor](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) algemene prestatieconcepten

## <a name="parameters"></a>Parameters

Dit zijn de belangrijkste instellingen om af te stemmen op de verbeterde Data Lake Storage Gen1-prestaties:

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
De sleutel tot het verbeteren van de prestaties met Data Lake Storage Gen1 is om de gelijktijdigheid zo veel mogelijk te verhogen.  Tez berekent automatisch het aantal taken dat moet worden gemaakt, zodat u deze niet hoeft in te stellen.   

## <a name="example-calculation"></a>Voorbeeldberekening

Stel dat u een D14-cluster met 8 node hebt.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Beperkingen

**Data Lake Storage Gen1 throttling** 

Als u de bandbreedtelimieten van Data Lake Storage Gen1 bereikt, ziet u taakfouten. Dit kan worden geïdentificeerd door het observeren van throttling fouten in taaklogboeken.  U de parallellisme verminderen door de grootte van tez-containers te vergroten.  Als je meer gelijktijdigheid nodig hebt voor je werk, neem dan contact met ons op.

Om te controleren of u wordt gewurgd, moet u de foutopsporingsregistratie aan de clientzijde inschakelen. Zo kun je dat doen:

1. Zet de volgende eigenschap in de log4j eigenschappen in Hive config. Dit kan vanuit de Ambari-weergave: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Start alle knooppunten/service opnieuw voor de config om van kracht te worden.

2. Als u wordt gewurgd, ziet u de HTTP 429-foutcode in het hive-logboekbestand. Het hive log bestand bevindt&lt;&gt;zich in /tmp/ user /hive.log

## <a name="further-information-on-hive-tuning"></a>Meer informatie over Hive tuning

Hier zijn een paar blogs die zullen helpen bij het afstemmen van uw Hive query's:
* [Hive-query's optimaliseren voor Hadoop in HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Problemen met de prestaties van Hive-query's oplossen](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite talk on optimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
