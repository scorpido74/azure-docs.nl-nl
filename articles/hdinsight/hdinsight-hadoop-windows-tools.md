---
title: Een Windows-pc gebruiken met Hadoop op HDInsight - Azure
description: Werk vanaf een Windows-pc in Hadoop op HDInsight. Clusters beheren en query's met PowerShell-, Visual Studio- en Linux-hulpprogramma's. Ontwikkel big data oplossingen met .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933941"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Werken in het Apache Hadoop-ecosysteem op HDInsight vanaf een Windows-pc

Meer informatie over ontwikkelings- en beheeropties op de Windows-pc voor het werken in het Apache Hadoop-ecosysteem op HDInsight.

HDInsight is gebaseerd op Apache Hadoop en Hadoop componenten, open-source technologieën ontwikkeld op Linux. HDInsight versie 3.4 en hoger gebruikt de Ubuntu Linux distributie als onderliggend os voor het cluster. U echter werken met HDInsight vanuit een Windows-client of Windows-ontwikkelomgeving.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>PowerShell gebruiken voor implementatie- en beheertaken

Azure PowerShell is een scriptomgeving die u gebruiken om implementatie- en beheertaken in HDInsight vanuit Windows te beheren en te automatiseren.

Voorbeelden van taken die u uitvoeren met PowerShell:

* [Clusters maken met PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Apache Hive-query's uitvoeren met PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Clusters beheren met PowerShell](hdinsight-administer-use-powershell.md).

Volg stappen om [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) te installeren en configureren om de nieuwste versie te krijgen.

## <a name="utilities-you-can-run-in-a-browser"></a>Hulpprogramma's die u uitvoeren in een browser

De volgende hulpprogramma's hebben een web-gebruikersinterface die wordt uitgevoerd in een browser:
* **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** is een interactieve, command-line shell die wordt uitgevoerd in uw browser en vanuit de Azure-portal.

* **[Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** is een beheer- en bewakingshulpprogramma dat beschikbaar is in de Azure-portal en die kan worden gebruikt om verschillende soorten taken te beheren, zoals:
    * [Apache Ambari gebruiken met de API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Apache Hive View in Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Apache Tez View in Apache Ambari](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) Tools voor Visual Studio

Gebruik Data Lake Tools voor Visual Studio om Storm topologieën te implementeren en te beheren. Data Lake Tools installeert ook de SCP.NET SDK, waarmee je C# Storm topologieën ontwikkelen met Visual Studio.

Voordat u naar de volgende voorbeelden gaat, [installeert en probeert u Data Lake Tools voor Visual Studio.](hadoop/apache-hadoop-visual-studio-tools-get-started.md)

Voorbeelden van taken die u uitvoeren met Visual Studio en Data Lake Tools voor Visual Studio:
* [Stormtopologieën implementeren en beheren vanuit Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Ontwikkel C# topologieën voor Storm met behulp van Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). De bits bevatten voorbeeldsjablonen voor Storm-topologieën waarmee u verbinding maken met databases, zoals Azure Cosmos DB en SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio en de .NET SDK

U Visual Studio met de .NET SDK gebruiken om clusters te beheren en big data-toepassingen te ontwikkelen. U andere IdEs gebruiken voor de volgende taken, maar voorbeelden worden weergegeven in Visual Studio.

Voorbeelden van taken die u uitvoeren met de .NET SDK in Visual Studio:
* [Azure HDInsight SDK voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Apache Hive-query's uitvoeren met de .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Gebruik C# door de gebruiker gedefinieerde functies met Apache Hive en Apache Pig streaming op Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA en Eclipse IDE voor Spark clusters

Zowel [Intellij IDEA](https://www.jetbrains.com/idea/download) en de [Eclipse IDE](https://www.eclipse.org/downloads/) kan worden gebruikt om:
* Ontwikkel en dien een Scala Spark-toepassing in op een HDInsight Spark-cluster.
* Toegang tot Spark-clusterbronnen.
* Ontwikkelen en lokaal een Scala Spark-toepassing uitvoeren.

Deze artikelen laten zien hoe:
* Intellij IDEA: [Maak Apache Spark-toepassingen met behulp van de Azure Toolkit voor Intellij-plug-in en de Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* Eclipse IDE of Scala IDE voor Eclipse: [Maak Apache Spark-toepassingen en de Azure Toolkit voor Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notitieblokken op Spark voor gegevenswetenschappers

Apache Spark-clusters in HDInsight omvatten Apache Zeppelin-notebooks en kernels die kunnen worden gebruikt met Jupyter-laptops.

* [Meer informatie over het gebruik van kernels op Apache Spark-clusters met Jupyter-laptops om Spark-toepassingen te testen](spark/apache-spark-zeppelin-notebook.md)
* [Meer informatie over het gebruik van Apache Zeppelin-notitieblokken op Apache Spark-clusters om Spark-taken uit te voeren](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Linux-gebaseerde tools en technologieën uitvoeren op Windows

Als u een situatie tegenkomt waarin u een tool of technologie moet gebruiken die alleen beschikbaar is op Linux, overweeg dan de volgende opties:

* **Bash op Ubuntu op Windows 10** biedt een Linux-subsysteem op Windows. Met Bash u linux-hulpprogramma's rechtstreeks uitvoeren zonder dat u een speciale Linux-installatie hoeft te onderhouden. Zie [Windows Subsystem for Linux Installation Guide voor Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) voor installatiestappen.  Andere [Unix schelpen](https://www.gnu.org/software/bash/) zal ook werken.
* **Docker voor Windows** biedt toegang tot veel Linux-gebaseerde tools en kan rechtstreeks vanuit Windows worden uitgevoerd. U Docker bijvoorbeeld gebruiken om de Beeline-client voor Hive rechtstreeks vanuit Windows uit te voeren. U Docker ook gebruiken om een lokaal Jupyter-notitieblok uit te voeren en op afstand verbinding te maken met Spark op HDInsight. [Aan de slag met Docker voor Windows](https://docs.docker.com/docker-for-windows/)
* **[Met MobaXTerm](https://mobaxterm.mobatek.net/)** u grafisch door het clusterbestandssysteem bladeren via een SSH-verbinding.

## <a name="cross-platform-tools"></a>Tools voor meerdere platforms

De Azure-opdrachtregelinterface (CLI) is de platformoverschrijdende opdrachtregelervaring voor het beheren van Azure-resources.  Zie [Azure Command-Line Interface (CLI) voor](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als u nieuw bent in het werken in Linux-gebaseerde clusters, raadpleegt u de volgende artikelen:
* [Apache Hadoop, Apache Kafka, Apache Spark of andere clusters instellen](hdinsight-hadoop-provision-linux-clusters.md)
* [Tips voor HDInsight clusters over Linux](hdinsight-hadoop-linux-information.md)
