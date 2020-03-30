---
title: Problemen met HDFS in Azure HDInsight oplossen
description: Antwoorden op veelgestelde vragen over het werken met HDFS en Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 6b0a81a2f3af10a1e5ad60c6c33357a6e906ee47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895253"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Problemen met Apache Hive HDFS oplossen met behulp van Azure HDInsight

Meer informatie over de belangrijkste problemen en hun oplossingen bij het werken met Hadoop Distributed File System (HDFS) payloads in Apache Ambari. Zie de [HDFS-opdrachtenhandleiding](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) en de [Shell-gids voor bestandssystemen](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)voor een volledige lijst met opdrachten.

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Hoe krijg ik toegang tot de lokale HDFS vanuit een cluster?

### <a name="issue"></a>Probleem

Toegang tot de lokale HDFS vanuit de opdrachtregel en toepassingscode in plaats van Azure Blob-opslag of Azure Data Lake Storage te gebruiken vanuit het HDInsight-cluster.

### <a name="resolution-steps"></a>Oplossingen

1. Gebruik bij de `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` opdrachtprompt letterlijk, zoals in de volgende opdracht:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Gebruik de URI `hdfs://mycluster/` letterlijk vanaf de broncode, zoals in de volgende voorbeeldtoepassing:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Voer het gecompileerde .jar-bestand `java-unit-tests-1.0.jar`(bijvoorbeeld een bestand met de naam) uit op het HDInsight-cluster met de volgende opdracht:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="du"></a>Du

De [-du](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) opdracht geeft formaten van bestanden en mappen in de opgegeven directory of de lengte van een bestand in het geval het is gewoon een bestand.

De `-s` optie produceert een geaggregeerde samenvatting van bestandslengtes die worden weergegeven.  
De `-h` optie maakt de bestandsformaten op.

Voorbeeld:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>Rm

Met de opdracht [-rm](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) worden bestanden verwijderd die als argumenten zijn opgegeven.

Voorbeeld:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
