---
title: Problemen met Hive oplossen met Azure HDInsight
description: Antwoorden op veelgestelde vragen over het werken met Apache Hive en Azure HDInsight.
keywords: Azure HDInsight, Hive, FAQ, troubleshooting guide, veelgestelde vragen
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 02247adb9852a72b386feb2ef0924b0f1b3d6277
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895238"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Problemen met Apache Hive oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste vragen en hun resoluties bij het werken met Apache Hive payloads in Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>Hoe exporteer ik een Hive-metastore en importeer ik deze op een ander cluster?

### <a name="resolution-steps"></a>Oplossingen

1. Maak verbinding met het HDInsight-cluster via een Secure Shell -client (SSH). Zie [Aanvullende lectuur](#additional-reading-end)voor meer informatie.

2. Voer de volgende opdracht uit op het HDInsight-cluster van waaruit u de metastore wilt exporteren:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Met deze opdracht genereert u een bestand met de naam allatables.sql.

3. Kopieer het bestand alltables.sql naar het nieuwe HDInsight-cluster en voer de volgende opdracht uit:

    ```apache
    hive -f alltables.sql
    ```

De code in de oplossingsstappen gaat ervan uit dat gegevenspaden op het nieuwe cluster dezelfde zijn als de gegevenspaden op het oude cluster. Als de gegevenspaden verschillend zijn, u `alltables.sql` het gegenereerde bestand handmatig bewerken om eventuele wijzigingen weer te geven.

### <a name="additional-reading"></a>Aanvullende lezing

- [Verbinding maken met een HDInsight-cluster met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>Hoe kan ik Hive-logboeken op een cluster lokaliseren?

### <a name="resolution-steps"></a>Oplossingen

1. Maak verbinding met het HDInsight-cluster via SSH. Zie **Aanvullende lectuur**voor meer informatie.

2. Als u hive-clientlogboeken wilt bekijken, gebruikt u de volgende opdracht:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Als u Hive-metastorelogboeken wilt bekijken, gebruikt u de volgende opdracht:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Als u Hive-serverlogboeken wilt weergeven, gebruikt u de volgende opdracht:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Aanvullende lezing

- [Verbinding maken met een HDInsight-cluster met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>Hoe start ik de Hive shell met specifieke configuraties op een cluster?

### <a name="resolution-steps"></a>Oplossingen

1. Geef een configuratiesleutelwaardepaar op wanneer u de hive-shell start. Zie [Aanvullende lectuur](#additional-reading-end)voor meer informatie.

   ```apache
   hive -hiveconf a=b
   ```

2. Als u alle effectieve configuraties op hive shell wilt weergeven, gebruikt u de volgende opdracht:

   ```apache
   hive> set;
   ```

   Gebruik bijvoorbeeld de volgende opdracht om Hive shell te starten met foutopsporingslogboekregistratie ingeschakeld op de console:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Aanvullende lezing

- [Eigenschappen van hive-configuratie](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-apache-tez-dag-data-on-a-cluster-critical-path"></a><a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>Hoe analyseer ik Apache Tez DAG-gegevens op een clusterkritisch pad?

### <a name="resolution-steps"></a>Oplossingen

1. Als u een Apache Tez-gerichte acyclische grafiek (DAG) wilt analyseren op een clusterkritieke grafiek, maakt u verbinding met het HDInsight-cluster met behulp van SSH. Zie [Aanvullende lectuur](#additional-reading-end)voor meer informatie.

2. Voer de volgende opdracht uit op een opdrachtprompt:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Als u andere analysers wilt weergeven die kunnen worden gebruikt om Tez DAG te analyseren, gebruikt u de volgende opdracht:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   U moet een voorbeeldprogramma als eerste argument opgeven.

   Geldige programmanamen zijn:
    - **ContainerReuseAnalyzer**: Print container hergebruik details in een DAG
    - **CriticalPath**: Vind het kritieke pad van een DAG
    - **LocalityAnalyzer**: Plaatsdetails afdrukken in een DAG
    - **ShuffleTimeAnalyzer**: Analyseer de shuffle tijd details in een DAG
    - **SkewAnalyzer**: Analyseer de schuine details in een DAG
    - **SlowNodeAnalyzer**: Print knooppunt details in een DAG
    - **SlowTaskIdentifier**: Langzame taakdetails afdrukken in een DAG
    - **SlowestVertexAnalyzer**: Print langzaamste hoekpunt details in een DAG
    - **SpillAnalyzer**: Print spill details in een DAG
    - **TaskConcurrencyAnalyzer**: Druk de details van de taakgelijktijdigie in een DAG af
    - **VertexLevelCriticalPathAnalyzer**: Vind het kritieke pad op hoekpuntniveau in een DAG

### <a name="additional-reading"></a>Aanvullende lezing

- [Verbinding maken met een HDInsight-cluster met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>Hoe download ik Tez DAG-gegevens uit een cluster?

#### <a name="resolution-steps"></a>Oplossingen

Er zijn twee manieren om de Tez DAG-gegevens te verzamelen:

- Vanaf de opdrachtregel:

    Maak verbinding met het HDInsight-cluster via SSH. Voer bij de opdrachtprompt de volgende opdracht uit:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Gebruik de weergave Ambari Tez:

  1. Ga naar Ambari.
  2. Ga naar de Tez-weergave (onder het pictogram tegels in de rechterbovenhoek).
  3. Selecteer de DAG die u wilt weergeven.
  4. Selecteer **Gegevens downloaden**.

### <a name="additional-reading"></a><a name="additional-reading-end"></a>Aanvullende lezing

[Verbinding maken met een HDInsight-cluster met Behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

- Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
