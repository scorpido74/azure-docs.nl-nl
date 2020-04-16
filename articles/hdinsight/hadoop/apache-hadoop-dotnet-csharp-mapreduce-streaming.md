---
title: C# gebruiken met MapReduce op Hadoop in HDInsight - Azure
description: Meer informatie over het gebruik van C# om MapReduce-oplossingen te maken met Apache Hadoop in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417612"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>C# gebruiken met MapReduce streaming op Apache Hadoop in HDInsight

Meer informatie over het gebruik van C# om een MapReduce-oplossing te maken op HDInsight.

Met Apache Hadoop-streaming u MapReduce-taken uitvoeren met behulp van een script of uitvoerbaar. Hier wordt .NET gebruikt om de mapper en reducer te implementeren voor een oplossing voor het tellen van woorden.

## <a name="net-on-hdinsight"></a>.NET op HDInsight

HDInsight-clusters gebruiken [Mono (https://mono-project.com) ](https://mono-project.com) om .NET-toepassingen uit te voeren. Mono versie 4.2.1 is inbegrepen bij HDInsight versie 3.6. Zie [Apache Hadoop-componenten die beschikbaar zijn met verschillende HDInsight-versies](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)voor meer informatie over de versie van Mono die bij HDInsight is meegeleverd.

Zie [Monocompatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over Mono-compatibiliteit met .NET Framework-versies.

## <a name="how-hadoop-streaming-works"></a>Hoe Hadoop streaming werkt

Het basisproces dat wordt gebruikt voor het streamen in dit document is als volgt:

1. Hadoop geeft gegevens door aan de mapper *(mapper.exe* in dit voorbeeld) op SOAIN.
2. De mapper verwerkt de gegevens en zendt tab-delimited key/value pairs uit naar SODOUT.
3. De uitvoer wordt gelezen door Hadoop, en vervolgens doorgegeven aan de reducer *(reducer.exe* in dit voorbeeld) op STDIN.
4. De reducer leest de tab-delimited key/value pairs, verwerkt de gegevens en zendt het resultaat vervolgens uit als tab-delimited key/value pairs op STDOUT.
5. De uitvoer wordt gelezen door Hadoop en naar de uitvoermap geschreven.

Zie [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)voor meer informatie over streaming.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een vertrouwdheid met het schrijven en bouwen van C# code die zich richt op .NET Framework 4.5.

* Een manier om .exe-bestanden naar het cluster te uploaden. De stappen in dit document gebruiken de Data Lake Tools voor Visual Studio om de bestanden te uploaden naar primaire opslag voor het cluster.

* Als u PowerShell gebruikt, hebt u de [Az-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

* Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Het [URI-schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) voor de primaire opslag van uw clusters. Dit schema `wasb://` is bedoeld `abfs://` voor Azure Storage, Azure `adl://` Data Lake Storage Gen2 of voor Azure Data Lake Storage Gen1. Als beveiligde overdracht is ingeschakeld voor Azure Storage of Data `wasbs://` Lake `abfss://`Storage Gen2, is de URI respectievelijk secure transfer of , respectievelijk Zie ook, [secure transfer](../../storage/common/storage-require-secure-transfer.md).

## <a name="create-the-mapper"></a>De mapper maken

Maak in Visual Studio een nieuwe .NET Framework-consoletoepassing met de naam *mapper*. Gebruik de volgende code voor de toepassing:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Nadat u de toepassing hebt gemaakt, maakt u deze om het bestand */bin/Debug/mapper.exe* in de projectmap te produceren.

## <a name="create-the-reducer"></a>De reducer maken

Maak in Visual Studio een nieuwe .NET Framework-consoletoepassing met de naam *reducer*. Gebruik de volgende code voor de toepassing:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Nadat u de toepassing hebt gemaakt, maakt u deze om het bestand */bin/Debug/reducer.exe* in de projectmap te produceren.

## <a name="upload-to-storage"></a>Uploaden naar opslag

Vervolgens moet u de *mapper-* en *reducer-toepassingen* uploaden naar HDInsight-opslag.

1. Selecteer**serververkenner** **in** > Visual Studio .

1. Klik met de rechtermuisknop op **Azure,** selecteer **Verbinding maken met Microsoft Azure-abonnement...** en voltooi het aanmeldingsproces.

1. Breid het HDInsight-cluster uit waarop u deze toepassing wilt implementeren. Er wordt een vermelding met de tekst **(standaardopslagaccount)** weergegeven.

   ![Opslagaccount, HDInsight-cluster, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Als de vermelding **(Standaardopslagaccount)** kan worden uitgebreid, gebruikt u een **Azure Storage-account** als standaardopslag voor het cluster. Als u de bestanden op de standaardopslag voor het cluster wilt weergeven, vouwt u de vermelding uit en dubbelklikt u **vervolgens op (Standaardcontainer).**

   * Als de vermelding **(Standaardopslagaccount)** niet kan worden uitgebreid, gebruikt u **Azure Data Lake Storage** als de standaardopslag voor het cluster. Als u de bestanden op de standaardopslag voor het cluster wilt weergeven, dubbelklikt u op de vermelding **(Standaardopslagaccount).**

1. Als u de .exe-bestanden wilt uploaden, gebruikt u een van de volgende methoden:

    * Als u een **Azure-opslagaccount gebruikt,** selecteert u het pictogram **Blob uploaden.**

        ![HDInsight uploadpictogram voor mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Selecteer Bladeren in het dialoogvenster **Nieuw bestand uploaden** onder **Bestandsnaam**. **Browse** Ga in het dialoogvenster **Blob uploaden** naar de map *bin\debug* voor het *mapperproject* en kies het *mapper.exe-bestand.* Selecteer tot slot **Openen** en vervolgens **OK** om de upload te voltooien.

    * Klik voor **Azure Data Lake Storage**met de rechtermuisknop op een leeg gebied in de bestandsvermelding en selecteer **Uploaden**. Selecteer ten slotte het *bestand mapper.exe* en selecteer **Openen**.

    Zodra de *mapper.exe-upload* is voltooid, herhaalt u het uploadproces voor het *reducer.exe-bestand.*

## <a name="run-a-job-using-an-ssh-session"></a>Een taak uitvoeren: een SSH-sessie gebruiken

In de volgende procedure wordt beschreven hoe u een map uitvoertReduce-taak met behulp van een SSH-sessie:

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Gebruik een van de volgende opdrachten om de taak MapReduce te starten:

   * Als de standaardopslag **Azure Storage**is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Als de standaardopslag **Data Lake Storage Gen1**is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Als de standaardopslag **Data Lake Storage Gen2**is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   In de volgende lijst wordt beschreven wat elke parameter en optie vertegenwoordigt:

   |Parameter | Beschrijving |
   |---|---|
   |hadoop-streaming.jar|Hiermee geeft u het potbestand op dat de functionaliteit voor streaming MapReduce bevat.|
   |-bestanden|Hiermee geeft u de *mapper.exe-* en *reducer.exe-bestanden* voor deze taak op. De `wasbs:///` `adl:///`, `abfs:///` of protocoldeclaratie voor elk bestand is het pad naar de hoofdmap van standaardopslag voor het cluster.|
   |-mapper|Hiermee geeft u het bestand op dat de mapper implementeert.|
   |-reducer|Hiermee geeft u het bestand op dat de reducer implementeert.|
   |-invoer|Hiermee geeft u de invoergegevens op.|
   |-output|Hiermee geeft u de uitvoermap op.|

1. Zodra de taak MapReduce is voltooid, gebruikt u de volgende opdracht om de resultaten weer te geven:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   De volgende tekst is een voorbeeld van de gegevens die door deze opdracht worden geretourneerd:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Een taak uitvoeren: PowerShell gebruiken

Gebruik het volgende PowerShell-script om een mapuittaak uit te voerenEn de resultaten te downloaden.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Dit script vraagt u om de naam en het wachtwoord van het clusteraccount, samen met de clusternaam HDInsight. Zodra de taak is voltooid, wordt de uitvoer gedownload naar een bestand met de naam *output.txt*. De volgende tekst is een voorbeeld `output.txt` van de gegevens in het bestand:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Volgende stappen

Zie [MapReduce gebruiken in Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)voor meer informatie over het gebruik van MapReduce met HDInsight.

Zie [Een C#-door gebruiker gedefinieerde functie gebruiken met Apache Hive en Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md)voor informatie over het gebruik van C# met Hive en Pig.

Zie [C# topologieën ontwikkelen voor Apache Storm op HDInsight voor](../storm/apache-storm-develop-csharp-visual-studio-topology.md)informatie over het gebruik van C# met Storm op HDInsight.
