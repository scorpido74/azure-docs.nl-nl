---
title: Apache Hive gebruiken met PowerShell in HDInsight - Azure
description: PowerShell gebruiken om Apache Hive-query's uit te voeren in Apache Hadoop in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: deaa934b257fab74830d75e308a283e7608dc590
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552590"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive-query's uitvoeren met PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dit document bevat een voorbeeld van het gebruik van Azure PowerShell om Apache Hive-query's uit te voeren in een Apache Hadoop op HDInsight-cluster.

> [!NOTE]  
> Dit document geeft geen gedetailleerde beschrijving van wat de HiveQL-instructies die in de voorbeelden worden gebruikt, doen. Zie [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)voor informatie over de HiveQL die in dit voorbeeld wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* De PowerShell [Az Module](https://docs.microsoft.com/powershell/azure/overview) geïnstalleerd.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u op afstand Hive-query's op HDInsight uitvoeren. Intern voeren de cmdlets REST-gesprekken met [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) op het HDInsight-cluster.

De volgende cmdlets worden gebruikt bij het uitvoeren van Hive-query's in een extern HDInsight-cluster:

* `Connect-AzAccount`: Verifieert Azure PowerShell naar uw Azure-abonnement.
* `New-AzHDInsightHiveJobDefinition`: Hiermee maakt u een *taakdefinitie* met behulp van de opgegeven HiveQL-instructies.
* `Start-AzHDInsightJob`: Hiermee wordt de taakdefinitie naar HDInsight gesstuurt en wordt de taak gestart. Een *taakobject* wordt geretourneerd.
* `Wait-AzHDInsightJob`: Gebruikt het taakobject om de status van de taak te controleren. Het wacht tot de taak is voltooid of de wachttijd is overschreden.
* `Get-AzHDInsightJobOutput`: Wordt gebruikt om de uitvoer van de taak op te halen.
* `Invoke-AzHDInsightHiveJob`: Wordt gebruikt om HiveQL-instructies uit te voeren. Deze cmdlet blokkeert de query wordt voltooid en retourneert vervolgens de resultaten.
* `Use-AzHDInsightCluster`: Hiermee stelt u het `Invoke-AzHDInsightHiveJob` huidige cluster in dat voor de opdracht moet worden gebruikt.

De volgende stappen laten zien hoe u deze cmdlets gebruikt om een taak uit te voeren in uw HDInsight-cluster:

1. Sla met behulp van een `hivejob.ps1`editor de volgende code op als .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Open een nieuwe **Azure PowerShell-opdrachtprompt.** Wijzig mappen in de `hivejob.ps1` locatie van het bestand en gebruik de volgende opdracht om het script uit te voeren:

        .\hivejob.ps1

    Wanneer het script wordt uitgevoerd, wordt u gevraagd de clusternaam en de https/clusterbeheerders-accountreferenties in te voeren. U ook worden gevraagd zich aan te melden bij uw Azure-abonnement.

3. Wanneer de taak is voltooid, retourneert deze informatie die vergelijkbaar is met de volgende tekst:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Zoals eerder vermeld, `Invoke-Hive` kan worden gebruikt om een query uit te voeren en te wachten op het antwoord. Gebruik het volgende script om te zien hoe Invoke-Hive werkt:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    De uitvoer ziet eruit als de volgende tekst:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Voor langere HiveQL-query's u de cmdlet- of HiveQL-scriptbestanden van Azure PowerShell **Here-Strings** gebruiken. In het volgende fragment `Invoke-Hive` ziet u hoe u de cmdlet gebruikt om een HiveQL-scriptbestand uit te voeren. Het HiveQL-scriptbestand moet worden geüpload naar wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Zie <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings gebruiken</a>voor meer informatie over **Here-Strings**.

## <a name="troubleshooting"></a>Problemen oplossen

Als er geen informatie wordt geretourneerd wanneer de taak is voltooid, bekijkt u de foutlogboeken. Als u foutgegevens voor deze taak wilt weergeven, voegt u het volgende toe aan het einde van het `hivejob.ps1` bestand, slaat u deze op en voert u deze opnieuw uit.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Deze cmdlet retourneert de informatie die tijdens taakverwerking naar STDERR is geschreven.

## <a name="summary"></a>Samenvatting

Zoals u zien, biedt Azure PowerShell een eenvoudige manier om Hive-query's uit te voeren in een HDInsight-cluster, de taakstatus te controleren en de uitvoer op te halen.

## <a name="next-steps"></a>Volgende stappen

Voor algemene informatie over Hive in HDInsight:

* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren waarop u met Hadoop werken aan HDInsight:

* [MapGebruikenReduce met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
