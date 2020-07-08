---
title: .NET gebruiken met Hadoop MapReduce op basis van Linux-Azure
description: Meer informatie over het gebruik van .NET-toepassingen voor streaming MapReduce op HDInsight op basis van Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a6e9bea5d600771a7754142a4df4c0af503ccf5d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86075481"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>.NET-oplossingen voor op Windows gebaseerde HDInsight migreren naar op Linux gebaseerde HDInsight

HDInsight-clusters op basis van Linux gebruiken [mono ( https://mono-project.com) ](https://mono-project.com) om .NET-toepassingen uit te voeren. Met mono kunt u .NET-onderdelen gebruiken, zoals MapReduce-toepassingen met HDInsight op basis van Linux. In dit document leert u hoe u .NET-oplossingen die zijn gemaakt voor op Windows gebaseerde HDInsight-clusters kunt migreren om te werken met mono op op Linux gebaseerde HDInsight.

## <a name="mono-compatibility-with-net"></a>Mono-compatibiliteit met .NET

Mono versie 4.2.1 is opgenomen in HDInsight-versie 3,6. Zie [hdinsight-onderdeel versies](hdinsight-component-versioning.md)voor meer informatie over de versie van mono die is opgenomen in hdinsight.

Zie voor meer informatie over de compatibiliteit tussen mono en .NET de [mono-compatibiliteit https://www.mono-project.com/docs/about-mono/compatibility/) (](https://www.mono-project.com/docs/about-mono/compatibility/) document.

> [!IMPORTANT]  
> Het SCP.NET-Framework is compatibel met mono. Zie [Visual Studio gebruiken voor het ontwikkelen van C#-topologieën voor Apache Storm op HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)voor meer informatie over het gebruik van SCP.net met mono.

## <a name="automated-portability-analysis"></a>Automatische portabiliteit-analyse

De [.net-portabiliteit Analyzer](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) kan worden gebruikt voor het genereren van een rapport met incompatibiliteits problemen tussen uw toepassing en mono. Gebruik de volgende stappen om de Analyzer te configureren om uw toepassing te controleren op zwartwit:

1. Installeer de [.net-portabiliteit-analyse functie](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Selecteer tijdens de installatie de versie van Visual Studio die u wilt gebruiken.

2. Selecteer in Visual Studio 2015 __Analyze__  >  __instellingen voor portabiliteit Analyzer__analyseren en controleer of __4,5__ is ingeschakeld in het gedeelte __mono__ .

    ![4,5 gecontroleerd in mono-sectie voor de analyse-instellingen](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Selecteer __OK__ om de configuratie op te slaan.

3. Selecteer __analyseren__van  >  __Assembly voor analyse__analyseren. Selecteer de assembly die uw oplossing bevat en selecteer vervolgens __openen__ om te beginnen met de analyse.

4. Wanneer de analyse is voltooid, __Analyze__selecteert u  >  __analyse rapporten analyseren weer geven__. Selecteer in de resultaten van de __portabiliteit-analyse__de optie __rapport openen__ om een rapport te openen.

    ![Dialoog venster resultaten van portabiliteit Analyzer](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> Het analyseprogramma kan niet elk probleem met uw oplossing ondervangen. Een bestandspad van wordt bijvoorbeeld als `c:\temp\file.txt` OK beschouwd als mono op Windows wordt uitgevoerd. Hetzelfde pad is niet geldig op een Linux-platform.

## <a name="manual-portability-analysis"></a>Hand matige portabiliteit analyse

Voer een hand matige controle uit voor uw code met behulp van de informatie in de [Application portabiliteit ( https://www.mono-project.com/docs/getting-started/application-portability/) ](https://www.mono-project.com/docs/getting-started/application-portability/) document.

## <a name="modify-and-build"></a>Wijzigen en bouwen

U kunt Visual Studio blijven gebruiken om uw .NET-oplossingen voor HDInsight te maken. U moet er echter voor zorgen dat het project is geconfigureerd voor het gebruik van .NET Framework 4,5.

## <a name="deploy-and-test"></a>Implementeren en testen

Wanneer u uw oplossing hebt gewijzigd met de aanbevelingen van de .NET-portabiliteit Analyzer of van een hand matige analyse, moet u deze testen met HDInsight. Het testen van de oplossing op een HDInsight-cluster op basis van Linux kan subtiele problemen onthullen die moeten worden opgelost. U wordt aangeraden extra logboek registratie in uw toepassing in te scha kelen tijdens het testen.

Raadpleeg de volgende documenten voor meer informatie over het openen van Logboeken:

* [Toegang tot Apache Hadoop GARENs van toepassings logboeken op HDInsight op basis van Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Volgende stappen

* [C# gebruiken met MapReduce in HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Door de gebruiker gedefinieerde C#-functies gebruiken met Apache Hive en Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [C#-topologieën ontwikkelen voor Apache Storm op HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
