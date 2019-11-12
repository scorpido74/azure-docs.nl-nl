---
title: Gebruiken C# met MapReduce in Hadoop in HDInsight-Azure
description: Meer informatie over hoe C# u kunt gebruiken om MapReduce-oplossingen te maken met Apache Hadoop in azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: hrasheed
ms.openlocfilehash: 1cdf029d296bd6ff11b6531cd47dc6a7fd3163c3
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930252"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Gebruiken C# met MapReduce streaming op Apache Hadoop in HDInsight

Meer informatie over hoe C# u kunt gebruiken om een MapReduce-oplossing te maken op HDInsight.

> [!IMPORTANT]
> Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [Apache Hadoop onderdelen op HDInsight](../hdinsight-component-versioning.md)voor meer informatie.

Apache Hadoop streaming is een hulp programma waarmee u MapReduce-taken kunt uitvoeren met behulp van een script of een uitvoerbaar bestand. In dit voor beeld wordt .NET gebruikt voor het implementeren van de Mapper en de reminderr voor een oplossing voor het aantal woorden.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

*HDInsight-clusters op basis van Linux* gebruiken [mono (https://mono-project.com)](https://mono-project.com) om .NET-toepassingen uit te voeren. Mono versie 4.2.1 is opgenomen in HDInsight-versie 3,6. Zie [Apache Hadoop-onderdelen die beschikbaar zijn in verschillende versies van hdinsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)voor meer informatie over de versie van mono die is opgenomen in hdinsight. 

Zie [mono-compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over de compatibiliteit van mono met .NET Framework versies.

## <a name="how-hadoop-streaming-works"></a>Hoe Hadoop streaming werkt

Het basis proces dat wordt gebruikt voor streaming in dit document, is als volgt:

1. Hadoop geeft gegevens door aan de Mapper (*Mapper. exe* in dit voor beeld) op stdin.
2. De toewijzing verwerkt de gegevens en verzendt door tabs gescheiden sleutel/waarde-paren naar STDOUT.
3. De uitvoer wordt door Hadoop gelezen en vervolgens door gegeven aan de reducer (*reducer. exe* in dit voor beeld) op stdin.
4. De reducer leest de door tabs gescheiden sleutel-waardeparen, verwerkt de gegevens en levert vervolgens het resultaat op als tabgescheiden gescheiden sleutel/waarde-paren op STDOUT.
5. De uitvoer wordt door Hadoop gelezen en naar de uitvoermap geschreven.

Zie [Hadoop streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)voor meer informatie over streaming.

## <a name="prerequisites"></a>Vereisten

* Visual Studio.

* Een vertrouwdheid met het schrijven en C# maken van code die gericht is op .NET Framework 4,5.

* Een manier om exe-bestanden te uploaden naar het cluster. De stappen in dit document gebruiken de Data Lake-Hulpprogram Ma's voor Visual Studio voor het uploaden van de bestanden naar de primaire opslag voor het cluster.

* Azure PowerShell of een SSH-client (Secure Shell).

* Een Hadoop in HDInsight-cluster. Zie [een HDInsight-cluster maken](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een cluster.

## <a name="create-the-mapper"></a>De toewijzing maken

Maak in Visual Studio een nieuwe .NET Framework-console toepassing met de naam *Mapper*. Gebruik de volgende code voor de toepassing:

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

Nadat u de toepassing hebt gemaakt, bouwt u deze voor het maken van het */bin/debug/Mapper.exe* -bestand in de projectmap.

## <a name="create-the-reducer"></a>De verminderr maken

Maak in Visual Studio een nieuwe .NET Framework-console toepassing met de naam *reducer*. Gebruik de volgende code voor de toepassing:

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

Nadat u de toepassing hebt gemaakt, bouwt u deze voor het maken van het */bin/debug/reducer.exe* -bestand in de projectmap.

## <a name="upload-to-storage"></a>Uploaden naar opslag

Vervolgens moet u de toewijzing van de *Mapper* en het *verkorten* van toepassingen uploaden naar HDInsight-opslag.

1. Kies in Visual Studio > **Server Explorer** **weer geven** .

2. Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

3. Voer desgevraagd uw Azure-abonnements referenties in en selecteer **Aanmelden**.

4. Vouw het HDInsight-cluster uit dat u wilt gebruiken om deze toepassing te implementeren. Een vermelding met de tekst **(standaard opslag account)** wordt weer gegeven.

   ![Opslag account, HDInsight-cluster, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Als de vermelding **(standaard opslag account)** kan worden uitgevouwen, gebruikt u een **Azure Storage-account** als standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, vouwt u de vermelding uit en dubbelklikt u vervolgens op **(standaard container)** .

   * Als de vermelding **(standaard opslag account)** niet kan worden uitgevouwen, gebruikt u **Azure data Lake Storage** als de standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, dubbelklikt u op de vermelding **(standaard opslag account)** .

5. Gebruik een van de volgende methoden om de exe-bestanden te uploaden:

    * Als u een Azure Storage- **account**gebruikt, selecteert u het pictogram **BLOB uploaden** . 

        ![Pictogram voor het uploaden van HDInsight voor Mapper, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Selecteer in het dialoog venster **nieuw bestand uploaden** onder **Bestands naam**de optie **Bladeren**. Ga in het dialoog venster **BLOB uploaden** naar de map *bin\debug* voor het *toewijzings* project en kies vervolgens het bestand *Mapper. exe* . Selecteer ten slotte **openen** en klik vervolgens op **OK** om het uploaden te volt ooien. 

    * Voor **Azure data Lake Storage**klikt u met de rechter muisknop op een leeg gebied in de vermelding bestand en selecteert u vervolgens **uploaden**. Selecteer ten slotte het bestand *Mapper. exe* en selecteer vervolgens **openen**.

    Nadat het uploaden van *Mapper. exe* is voltooid, herhaalt u het upload proces voor het bestand *reducer. exe* .

## <a name="run-a-job-using-an-ssh-session"></a>Een taak uitvoeren: een SSH-sessie gebruiken

In de volgende procedure wordt beschreven hoe u een MapReduce-taak kunt uitvoeren met behulp van een SSH-sessie:

1. Gebruik SSH om verbinding te maken met het HDInsight-cluster. (Voer bijvoorbeeld de opdracht `ssh sshuser@<clustername>-ssh.azurehdinsight.net`.) Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

2. Gebruik een van de volgende opdrachten om de MapReduce-taak te starten:

   * Als de standaard opslag **Azure Storage**is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasb:///mapper.exe,wasb:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Als de standaard opslag **Data Lake Storage gen1**is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Als de standaard opslag **Data Lake Storage Gen2**is:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   In de volgende lijst wordt beschreven wat elke para meter en optie vertegenwoordigt:

   * *Hadoop-streaming. jar*: Hiermee geeft u het jar-bestand met de streaming MapReduce-functionaliteit.
   * `-files`: Hiermee geeft u de bestanden *Mapper. exe* en *reducer. exe* op voor deze taak. De declaratie van het `wasb:///`-, `adl:///`-of `abfs:///`-protocol voor elk bestand is het pad naar de hoofdmap van de standaard opslag voor het cluster.
   * `-mapper`: Hiermee geeft u het bestand op dat de Mapper implementeert.
   * `-reducer`: Hiermee geeft u het bestand op waarmee de verminderr wordt geïmplementeerd.
   * `-input`: Hiermee geeft u de invoer gegevens op.
   * `-output`: Hiermee geeft u de uitvoermap op.

3. Zodra de MapReduce-taak is voltooid, gebruikt u de volgende opdracht om de resultaten weer te geven:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   De volgende tekst is een voor beeld van de gegevens die door deze opdracht worden geretourneerd:

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

## <a name="run-a-job-using-powershell"></a>Een taak uitvoeren: Power shell gebruiken

Gebruik het volgende Power shell-script om een MapReduce-taak uit te voeren en de resultaten te downloaden.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Dit script vraagt u om de naam en het wacht woord van het cluster aanmeldings account, samen met de naam van het HDInsight-cluster. Zodra de taak is voltooid, wordt de uitvoer gedownload naar een bestand met de naam *output. txt*. De volgende tekst is een voor beeld van de gegevens in het `output.txt`-bestand:

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

Zie [MapReduce gebruiken in Apache Hadoop op hdinsight](hdinsight-use-mapreduce.md)voor meer informatie over het gebruik van MapReduce met hdinsight.

Zie [een door de gebruiker gedefinieerde functie gebruiken met Apache Hive en Apache C# varken](apache-hadoop-hive-pig-udf-dotnet-csharp.md)voor meer informatie over het gebruik C# van de with-component en Pig.

Zie voor meer informatie C# over het gebruik van met Storm op hdinsight [topologieën ontwikkelen C# voor Apache Storm op hdinsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
