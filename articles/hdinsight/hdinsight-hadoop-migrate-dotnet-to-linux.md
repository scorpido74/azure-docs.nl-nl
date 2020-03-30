---
title: Gebruik .NET met Hadoop MapReduce op Linux-gebaseerde HDInsight - Azure
description: Meer informatie over het gebruik van .NET-toepassingen voor het streamen van MapReduce op Linux-gebaseerde HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: 4b402975a151d26e8f335c07930274c156ac52fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272368"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migreer .NET-oplossingen voor HdInsight op Basis van Windows naar HDInsight op Linux

HdInsight-clusters op basis van Linux gebruiken [Mono (omhttps://mono-project.com) ](https://mono-project.com) .NET-toepassingen uit te voeren. Met Mono u .NET-componenten zoals MapReduce-toepassingen gebruiken met HDInsight op Linux. In dit document leert u hoe u .NET-oplossingen migreert die zijn gemaakt voor HDInsight-clusters in Windows om met Mono te werken op HDInsight op Linux.

## <a name="mono-compatibility-with-net"></a>Monocompatibiliteit met .NET

Mono versie 4.2.1 is inbegrepen bij HDInsight versie 3.6. Zie [HDInsight-componentversies](hdinsight-component-versioning.md)voor meer informatie over de versie van Mono die bij HDInsight is meegeleverd.

Zie de [monocompatibiliteithttps://www.mono-project.com/docs/about-mono/compatibility/) (document.](https://www.mono-project.com/docs/about-mono/compatibility/)

> [!IMPORTANT]  
> Het SCP.NET framework is compatibel met Mono. Zie Visual Studio gebruiken om [C# topologieën voor Apache Storm op HDInsight te ontwikkelen voor](storm/apache-storm-develop-csharp-visual-studio-topology.md)meer informatie over het gebruik van SCP.NET met Mono.

## <a name="automated-portability-analysis"></a>Geautomatiseerde portabiliteitsanalyse

De [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) kan worden gebruikt om een rapport te genereren van onverenigbaarheden tussen uw toepassing en Mono. Gebruik de volgende stappen om de analyzer te configureren om uw toepassing te controleren op monoportabiliteit:

1. Installeer de [.NET Portability Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Selecteer tijdens de installatie de versie van Visual Studio die u wilt gebruiken.

2. Selecteer in Visual Studio 2015 __de__ > optie__Instellingen voor draagbaarheidsanalyse analyseren__en controleer of __4.5__ is gecontroleerd in de sectie __Mono.__

    ![4.5 gecontroleerd in monosectie voor de instellingen van de analysator](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecteer __OK__ om de configuratie op te slaan.

3. Selecteer __De__ > __portabiliteit van de assemblage analyseren__. Selecteer de verzameling die uw oplossing bevat en selecteer __Openen__ om te beginnen met de analyse.

4. Zodra de analyse is voltooid, selecteert u__Analyseanalyserapporten__ __analyseren__ > . Selecteer Rapport __openen__ om een rapport te openen in __Resultaten van portabiliteitsanalyse.__

    ![Dialoogvenster Resultaten van de analyse van de draagbaarheid](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> De analyzer kan niet elk probleem met uw oplossing vangen. Een bestandspad van `c:\temp\file.txt` wordt bijvoorbeeld als OK beschouwd als Mono op Windows wordt uitgevoerd. Hetzelfde pad is niet geldig op een Linux-platform.

## <a name="manual-portability-analysis"></a>Handmatige portabiliteitsanalyse

Voer een handmatige controle van uw code uit met behulp van de informatie in de [toepassingsportabiliteithttps://www.mono-project.com/docs/getting-started/application-portability/) (document.](https://www.mono-project.com/docs/getting-started/application-portability/)

## <a name="modify-and-build"></a>Wijzigen en bouwen

U Visual Studio blijven gebruiken om uw .NET-oplossingen voor HDInsight te bouwen. U moet er echter voor zorgen dat het project is geconfigureerd voor het gebruik van .NET Framework 4.5.

## <a name="deploy-and-test"></a>Implementeren en testen

Zodra u uw oplossing hebt gewijzigd met behulp van de aanbevelingen van de .NET Portability Analyzer of uit een handmatige analyse, moet u deze testen met HDInsight. Het testen van de oplossing op een Linux-gebaseerde HDInsight-cluster kan subtiele problemen onthullen die moeten worden gecorrigeerd. We raden u aan extra registratie in uw toepassing in te schakelen tijdens het testen ervan.

Zie de volgende documenten voor meer informatie over het openen van logboeken:

* [Toegang Apache Hadoop YARN applicatie logs op Linux-gebaseerde HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Volgende stappen

* [C#gebruiken met MapReduce op HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Gebruik C# door de gebruiker gedefinieerde functies met Apache Hive en Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Ontwikkel C# topologieën voor Apache Storm op HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
