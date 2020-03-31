---
title: MapReduce en PowerShell gebruiken met Apache Hadoop - Azure HDInsight
description: Meer informatie over het gebruik van PowerShell om map te gebruikenWerk werk te beperken met Apache Hadoop op HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: b3c1abb7bff54e3e2d294b073b867c6c0e06f482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75830068"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Kaart uitvoerenWerk verminderen met Apache Hadoop op HDInsight met PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

In dit document vindt u een voorbeeld van het gebruik van Azure PowerShell om een mapreduce-taak uit te voeren in een Hadoop op het HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* De PowerShell [Az Module](https://docs.microsoft.com/powershell/azure/overview) geïnstalleerd.

## <a name="run-a-mapreduce-job"></a>Een taak Kaart verminderen uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u MapReduce-taken op HDInsight op afstand uitvoeren. Intern voert PowerShell REST-gesprekken naar [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (voorheen Templeton genoemd) die op het HDInsight-cluster draait.

De volgende cmdlets worden gebruikt bij het uitvoeren van MapReduce-taken in een extern HDInsight-cluster.

|Cmdlet | Beschrijving |
|---|---|
|Connect-AzAccount|Verifieert Azure PowerShell naar uw Azure-abonnement.|
|Nieuwe-azHDInsightmapreduceJobDefinition|Hiermee maakt u een nieuwe *taakdefinitie* met de opgegeven mapReduce-informatie.|
|Start-AzHDInsightJob|Hiermee wordt de taakdefinitie naar HDInsight gesstuurt en wordt de taak gestart. Een *taakobject* wordt geretourneerd.|
|Wait-azHDInsightJob|Hiermee gebruikt u het taakobject om de status van de taak te controleren. Het wacht tot de taak is voltooid of de wachttijd is overschreden.|
|Get-azHDInsightJobOutput|Wordt gebruikt om de uitvoer van de taak op te halen.|

De volgende stappen laten zien hoe u deze cmdlets gebruiken om een taak uit te voeren in uw HDInsight-cluster.

1. Sla met behulp van een editor de volgende code op als **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Open een nieuwe **Azure PowerShell-opdrachtprompt.** Wijzig mappen in de locatie van het **mapreducejob.ps1-bestand** en gebruik vervolgens de volgende opdracht om het script uit te voeren:

        .\mapreducejob.ps1

    Wanneer u het script uitvoert, wordt u gevraagd om de naam van het HDInsight-cluster en de clusteraanmelding. U wordt mogelijk ook gevraagd om te verifiëren voor uw Azure-abonnement.

3. Wanneer de taak is voltooid, ontvangt u uitvoer die vergelijkbaar is met de volgende tekst:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Deze uitvoer geeft aan dat de taak is voltooid.

    > [!NOTE]  
    > Zie [Probleemoplossing](#troubleshooting)als de **ExitCode** een andere waarde dan 0 is.

    In dit voorbeeld worden de gedownloade bestanden ook opgeslagen in een **bestand output.txt** in de map waarvan u het script uitvoert.

### <a name="view-output"></a>Uitvoer weergeven

Als u de woorden en tellingen wilt zien die door de taak zijn geproduceerd, opent u het bestand **output.txt** in een teksteditor.

> [!NOTE]  
> De uitvoerbestanden van een mapReduce-taak zijn onveranderlijk. Dus als u dit voorbeeld opnieuw uitvoert, moet u de naam van het uitvoerbestand wijzigen.

## <a name="troubleshooting"></a>Problemen oplossen

Als er geen informatie wordt geretourneerd wanneer de taak is voltooid, u fouten voor de taak weergeven. Als u foutgegevens voor deze taak wilt weergeven, voegt u de volgende opdracht toe aan het einde van het **mapreducejob.ps1-bestand.** Sla vervolgens het bestand op en voer het script opnieuw uit.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Deze cmdlet retourneert de informatie die is geschreven naar STDERR terwijl de taak wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zoals u zien, biedt Azure PowerShell een eenvoudige manier om MapReduce-taken uit te voeren op een HDInsight-cluster, de taakstatus te controleren en de uitvoer op te halen. Voor informatie over andere manieren waarop u met Hadoop werken aan HDInsight:

* [MapgebruikenReduce op HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Gebruik Apache Hive met Apache Hadoop op HDInsight](hdinsight-use-hive.md)
