---
title: De juiste VM-grootte selecteren voor uw Azure HDInsight-cluster
description: Meer informatie over het selecteren van de juiste VM-grootte voor uw HDInsight-cluster.
keywords: vm-grootten, clustergroottes, clusterconfiguratie
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682210"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>De juiste vm-grootte selecteren voor uw Azure HDInsight-cluster

In dit artikel wordt besproken hoe u de juiste VM-grootte selecteert voor de verschillende knooppunten in uw HDInsight-cluster. 

Begin met te begrijpen hoe de eigenschappen van een virtuele machine, zoals CPU-verwerking, RAM-grootte en netwerklatentie, de verwerking van uw workloads beïnvloeden. Denk vervolgens na over uw toepassing en hoe deze overeenkomt met waarvoor verschillende VM-families zijn geoptimaliseerd. Zorg ervoor dat de VM-familie die u wilt gebruiken, compatibel is met het clustertype dat u wilt implementeren. Zie [Azure HDInsight-ondersteunde knooppuntconfiguraties](hdinsight-supported-node-configuration.md)voor een lijst met alle ondersteunde en aanbevolen VM-formaten voor elk clustertype. Ten slotte u een benchmarkingproces gebruiken om een aantal voorbeeldworkloads te testen en te controleren welke SKU binnen dat gezin bij u geschikt is.

Zie [Capaciteitsplanning voor HDInsight-clusters voor](hdinsight-capacity-planning.md)meer informatie over het plannen van andere aspecten van uw cluster, zoals het selecteren van een opslagtype of clustergrootte.

## <a name="vm-properties-and-big-data-workloads"></a>VM-eigenschappen en big data-workloads

De VM-grootte en -type worden bepaald door cpu-verwerkingskracht, RAM-grootte en netwerklatentie:

- CPU: De VM-grootte bepaalt het aantal cores. Hoe meer kernen, hoe groter de mate van parallelle berekening die elk knooppunt kan bereiken. Sommige VM-typen hebben ook snellere cores.

- RAM: De VM-grootte bepaalt ook de hoeveelheid RAM beschikbaar in de VM. Voor workloads die gegevens opslaan in het geheugen voor verwerking, in plaats van het lezen vanaf de schijf, moet u ervoor zorgen dat uw werknemersknooppunten voldoende geheugen hebben om aan de gegevens te voldoen.

- Netwerk: Voor de meeste clustertypen bevinden de gegevens die door het cluster worden verwerkt, niet op lokale schijf, maar in een externe opslagservice zoals Data Lake Storage of Azure Storage. Houd rekening met de netwerkbandbreedte en -doorvoer tussen de VM van knooppunt's en de opslagservice. De netwerkbandbreedte die beschikbaar is voor een VM neemt doorgaans toe met grotere formaten. Zie overzicht [van VM-formaten](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)voor meer informatie.

## <a name="understanding-vm-optimization"></a>Informatie over VM-optimalisatie

Virtuele machinefamilies in Azure zijn geoptimaliseerd voor verschillende use cases. In de onderstaande tabel vindt u enkele van de populairste use cases en de VM-families die bij hen passen.

| Type                     | Grootten           |    Beschrijving       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Entry-level](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Cpu-prestaties en geheugenconfiguraties die het meest geschikt zijn voor instapworkloads zoals ontwikkeling en test. Ze zijn zuinig en bieden een goedkope optie om aan de slag te gaan met Azure. |
| [Algemeen doel](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Gebalanceerde CPU-geheugenverhouding. Dit is ideaal voor testen en ontwikkelen, voor kleine tot middelgrote databases, en webservers met weinig tot gemiddeld verkeer. |
| [Geoptimaliseerde rekenkracht](../virtual-machines/linux/sizes-compute.md)        | F           | Hoge CPU-geheugenverhouding. Goed voor medium traffic webservers, netwerkapparaten, batchprocessen en applicatieservers.        |
| [Geoptimaliseerd geheugen](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Hoge geheugen-cpu-verhouding. Zeer geschikt voor relationele databaseservers, middelgrote tot grote caches, en analysefuncties in het geheugen.                 |

- Zie [HDInsight Pricing](https://azure.microsoft.com/pricing/details/hdinsight/)voor informatie over de prijzen van beschikbare VM-exemplaren in door HDInsight ondersteunde regio's.

## <a name="cost-saving-vm-types-for-light-workloads"></a>Kostenbesparende VM-typen voor lichte workloads

Als u vereisten heeft voor lichte verwerking, kan de [F-serie](https://azure.microsoft.com/blog/f-series-vm-size/) een goede keuze zijn om aan de slag te gaan met HDInsight. Tegen een lagere prijs per uur levert de F-serie de beste prijs/kwaliteit-verhouding van de Azure-portfolio, gebaseerd op de ACU (Azure Compute Unit) per vCPU.

In de volgende tabel worden de clustertypen en knooppunttypen beschreven, die kunnen worden gemaakt met de VM's uit de Fsv2-serie.

| Clustertype | Versie | Worker Node | Hoofdknooppunt | Dierenverzorger Knooppunt |
|---|---|---|---|---|
| Spark | Alle | F4 en hoger | nee | nee |
| Hadoop | Alle | F4 en hoger | nee | nee |
| Kafka | Alle | F4 en hoger | nee | nee |
| HBase | Alle | F4 en hoger | nee | nee |
| LLAP LLAP | Handicap | nee | nee | nee |
| Storm | Handicap | nee | nee | nee |
| ML-service | HDI 3.6 ALLEEN | F4 en hoger | nee | nee |

Zie VM-formaten uit de [F-serie](https://azure.microsoft.com/blog/f-series-vm-size/)om de specificaties van elke F-serie SKU te bekijken.

## <a name="benchmarking"></a>Benchmarking

Benchmarking is het proces van het uitvoeren van gesimuleerde workloads op verschillende VM's om te meten hoe goed ze zullen presteren voor uw productieworkloads. 

Zie [Clustercapaciteitsplanning in Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type)voor meer informatie over benchmarking voor VM SKU's en clusterformaten.

## <a name="next-steps"></a>Volgende stappen

- [Azure HDInsight ondersteunde knooppuntconfiguraties](hdinsight-supported-node-configuration.md)
- [Grootten voor Linux Virtual Machines in Azure](../virtual-machines/linux/sizes.md)
