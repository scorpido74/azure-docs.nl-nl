---
title: Apache Hive gebruiken met Power shell in HDInsight-Azure
description: Power shell gebruiken voor het uitvoeren van Apache Hive query's in Apache Hadoop in azure HDInsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 89fa7976b922ba0e40e97b72de5d4eb9a02f0dfd
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736065"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive query's uitvoeren met Power shell
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dit document bevat een voor beeld van het gebruik van Azure PowerShell in de Azure-resource groeps modus om Hive-query's uit te voeren in een Apache Hadoop op HDInsight-cluster.

> [!NOTE]  
> Dit document biedt geen gedetailleerde beschrijving van de HiveQL-instructies die worden gebruikt in de voor beelden. Zie [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)voor meer informatie over de HiveQL die in dit voor beeld wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Een op Linux gebaseerde Apache Hadoop op HDInsight-cluster versie 3,4 of hoger.

* Een client met Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u Hive-Query's op HDInsight op afstand kunt uitvoeren. Intern maken de cmdlets REST-aanroepen naar [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) op het HDInsight-cluster.

De volgende cmdlets worden gebruikt bij het uitvoeren van Hive-query's in een extern HDInsight-cluster:

* `Connect-AzAccount`: Verifieert Azure PowerShell aan uw Azure-abonnement.
* `New-AzHDInsightHiveJobDefinition`: Hiermee maakt u een *taak definitie* met behulp van de opgegeven HiveQL-instructies.
* `Start-AzHDInsightJob`: De taak definitie wordt verzonden naar HDInsight en de taak wordt gestart. Er wordt een *taak* object geretourneerd.
* `Wait-AzHDInsightJob`: Gebruikt het taak object om de status van de taak te controleren. Er wordt gewacht tot de taak is voltooid of de wacht tijd is overschreden.
* `Get-AzHDInsightJobOutput`: Wordt gebruikt om de uitvoer van de taak op te halen.
* `Invoke-AzHDInsightHiveJob`: Wordt gebruikt om HiveQL-instructies uit te voeren. Deze cmdlet blokkeert de query en retourneert vervolgens de resultaten.
* `Use-AzHDInsightCluster`: Hiermee stelt u in dat het huidige cluster `Invoke-AzHDInsightHiveJob` moet worden gebruikt voor de opdracht.

De volgende stappen laten zien hoe u deze cmdlets kunt gebruiken om een taak uit te voeren in uw HDInsight-cluster:

1. Gebruik een editor om de volgende code op te `hivejob.ps1`slaan als.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Open een nieuwe **Azure PowerShell** opdracht prompt. Wijzig de mappen in de locatie van `hivejob.ps1` het bestand en gebruik vervolgens de volgende opdracht om het script uit te voeren:

        .\hivejob.ps1

    Wanneer het script wordt uitgevoerd, wordt u gevraagd om de cluster naam en de referenties van het HTTPS/Cluster-beheer account in te voeren. U wordt mogelijk ook gevraagd om u aan te melden bij uw Azure-abonnement.

3. Wanneer de taak is voltooid, wordt er informatie weer gegeven die vergelijkbaar is met de volgende tekst:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Zoals eerder vermeld, `Invoke-Hive` kan worden gebruikt om een query uit te voeren en te wachten op de reactie. Gebruik het volgende script om te zien hoe invoke-Hive werkt:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    De uitvoer ziet er als volgt uit:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]  
   > Voor langere HiveQL query's kunt u de script bestanden Azure PowerShell **hier-strings** of HiveQL gebruiken. Het volgende code fragment laat zien hoe u `Invoke-Hive` de cmdlet kunt gebruiken om een HiveQL-script bestand uit te voeren. Het HiveQL-script bestand moet worden geüpload naar wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Zie <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">hier Windows Power shell gebruiken-teken reeksen</a>voor meer informatie over **hier-teken reeksen**.

## <a name="troubleshooting"></a>Problemen oplossen

Als er geen informatie wordt geretourneerd wanneer de taak is voltooid, raadpleegt u de fouten Logboeken. Als u de fout gegevens voor deze taak wilt weer geven, voegt u het volgende `hivejob.ps1` toe aan het einde van het bestand, slaat u het op en voert u het vervolgens opnieuw uit.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Met deze cmdlet wordt de informatie geretourneerd die wordt geschreven naar STDERR tijdens taak verwerking.

## <a name="summary"></a>Samenvatting

Zoals u kunt zien, biedt Azure PowerShell een eenvoudige manier om Hive-query's uit te voeren in een HDInsight-cluster, de taak status te controleren en de uitvoer op te halen.

## <a name="next-steps"></a>Volgende stappen

Voor algemene informatie over Hive in HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [Apache Pig gebruiken met Apache Hadoop op HDInsight](hdinsight-use-pig.md)
* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
