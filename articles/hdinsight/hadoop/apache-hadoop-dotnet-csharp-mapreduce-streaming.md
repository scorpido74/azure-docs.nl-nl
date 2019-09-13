---
title: Gebruiken C# met MapReduce in Hadoop in HDInsight-Azure
description: Meer informatie over hoe C# u kunt gebruiken om MapReduce-oplossingen te maken met Apache Hadoop in azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 7f82ad65ecc805d5a45c78e8b190dd0eee4c340c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882337"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Gebruiken C# met MapReduce streaming op Apache Hadoop in HDInsight

Meer informatie over hoe C# u kunt gebruiken om een MapReduce-oplossing te maken op HDInsight.

> [!IMPORTANT]
> Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [versie beheer van HDInsight-onderdelen](../hdinsight-component-versioning.md)voor meer informatie.

Apache Hadoop streaming is een hulp programma waarmee u MapReduce-taken kunt uitvoeren met behulp van een script of een uitvoerbaar bestand. In dit voor beeld wordt .NET gebruikt voor het implementeren van de Mapper en de reminderr voor een oplossing voor het aantal woorden.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

__HDInsight-clusters op basis van Linux__ gebruiken [https://mono-project.com) mono (](https://mono-project.com) om .NET-toepassingen uit te voeren. Mono versie 4.2.1 is opgenomen in HDInsight-versie 3,6. Zie [hdinsight-onderdeel versies](../hdinsight-component-versioning.md)voor meer informatie over de versie van mono die is opgenomen in hdinsight. 

Zie [mono-compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over de compatibiliteit van mono met .NET Framework versies.

## <a name="how-hadoop-streaming-works"></a>Hoe Hadoop streaming werkt

Het basis proces dat wordt gebruikt voor streaming in dit document, is als volgt:

1. Hadoop geeft gegevens door aan de Mapper (Mapper. exe in dit voor beeld) op STDIN.
2. De toewijzing verwerkt de gegevens en verzendt door tabs gescheiden sleutel/waarde-paren naar STDOUT.
3. De uitvoer wordt door Hadoop gelezen en vervolgens door gegeven aan de reducer (reducer. exe in dit voor beeld) op STDIN.
4. De reducer leest de door tabs gescheiden sleutel-waardeparen, verwerkt de gegevens en levert vervolgens het resultaat op als tabgescheiden gescheiden sleutel/waarde-paren op STDOUT.
5. De uitvoer wordt door Hadoop gelezen en naar de uitvoermap geschreven.

Zie [Hadoop streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)voor meer informatie over streaming.

## <a name="prerequisites"></a>Vereisten

* Een vertrouwdheid met het schrijven en C# maken van code die gericht is op .NET Framework 4,5. De stappen in dit document gebruiken Visual Studio 2017.

* Een manier om exe-bestanden te uploaden naar het cluster. De stappen in dit document gebruiken de Data Lake-Hulpprogram Ma's voor Visual Studio voor het uploaden van de bestanden naar de primaire opslag voor het cluster.

* Azure PowerShell of een SSH-client.

* Een Hadoop in HDInsight-cluster. Zie [een HDInsight-cluster maken](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een cluster.

## <a name="create-the-mapper"></a>De toewijzing maken

Maak in Visual Studio een nieuwe __console toepassing__ met de naam __Mapper__. Gebruik de volgende code voor de toepassing:

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

Nadat u de toepassing hebt gemaakt, bouwt u deze `/bin/Debug/mapper.exe` voor het maken van het bestand in de projectmap.

## <a name="create-the-reducer"></a>De verminderr maken

Maak in Visual Studio een nieuwe __console toepassing__ met de naam __reducer__. Gebruik de volgende code voor de toepassing:

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

Nadat u de toepassing hebt gemaakt, bouwt u deze `/bin/Debug/reducer.exe` voor het maken van het bestand in de projectmap.

## <a name="upload-to-storage"></a>Uploaden naar opslag

1. Open **Server Explorer**in Visual Studio.

2. Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

3. Voer desgevraagd uw Azure-abonnements referenties in en klik op **Aanmelden**.

4. Vouw het HDInsight-cluster uit dat u wilt gebruiken om deze toepassing te implementeren. Een vermelding met de tekst __(standaard opslag account)__ wordt weer gegeven.

    ![Server Explorer het opslag account voor het cluster wordt weer gegeven](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

    * Als dit item kan worden uitgevouwen, gebruikt u een __Azure Storage-account__ als standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, vouwt u de vermelding uit en dubbelklikt u vervolgens op de __(standaard container)__ .

    * Als dit item niet kan worden uitgevouwen, gebruikt u __Azure data Lake Storage__ als de standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, dubbelklikt u op de vermelding __(standaard opslag account)__ .

5. Gebruik een van de volgende methoden om de exe-bestanden te uploaden:

   * Als u een __Azure Storage-account__gebruikt, klikt u op het pictogram uploaden en bladert u naar de map **bin\debug** voor het **toewijzings** project. Selecteer ten slotte het bestand **Mapper. exe** en klik op **OK**.

        ![pictogram uploaden](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)
    
   * Als u __Azure data Lake Storage__gebruikt, klikt u met de rechter muisknop op een leeg gebied in de bestands vermelding en selecteert u vervolgens __uploaden__. Selecteer ten slotte het bestand **Mapper. exe** en klik op **openen**.

     Nadat het uploaden van __Mapper. exe__ is voltooid, herhaalt u het upload proces voor het bestand __reducer. exe__ .

## <a name="run-a-job-using-an-ssh-session"></a>Een taak uitvoeren: Een SSH-sessie gebruiken

1. Gebruik SSH om verbinding te maken met het HDInsight-cluster. Zie [SSH gebruiken met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

2. Gebruik een van de volgende opdrachten om de MapReduce-taak te starten:

   * Als __Data Lake Storage Gen2__ als standaard opslag gebruikt:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Als __Data Lake Storage gen1__ als standaard opslag gebruikt:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Als __Azure Storage__ als standaard opslag gebruikt:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     In de volgende lijst wordt beschreven wat elke para meter doet:

   * `hadoop-streaming.jar`: Het jar-bestand dat de functionaliteit van streaming MapReduce bevat.
   * `-files`: Voegt de `mapper.exe` - `reducer.exe` en-bestanden toe aan deze taak. De `abfs:///`of vóór`wasb:///` elk bestand is het pad naar de hoofdmap van de standaard opslag voor het cluster`adl:///` .
   * `-mapper`: Hiermee geeft u op welk bestand de Mapper implementeert.
   * `-reducer`: Hiermee geeft u op welk bestand de verminderr implementeert.
   * `-input`: De invoer gegevens.
   * `-output`: De uitvoermap.

3. Zodra de MapReduce-taak is voltooid, gebruikt u de volgende opdracht om de resultaten weer te geven:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    De volgende tekst is een voor beeld van de gegevens die door deze opdracht worden geretourneerd:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Een taak uitvoeren: PowerShell gebruiken

Gebruik het volgende Power shell-script om een MapReduce-taak uit te voeren en de resultaten te downloaden.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Dit script vraagt u om de naam en het wacht woord van het cluster aanmeldings account, samen met de naam van het HDInsight-cluster. Zodra de taak is voltooid, wordt de uitvoer gedownload naar een bestand met `output.txt`de naam. De volgende tekst is een voor beeld van de gegevens in `output.txt` het bestand:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Volgende stappen

Zie [MapReduce gebruiken met hdinsight](hdinsight-use-mapreduce.md)voor meer informatie over het gebruik van MapReduce met hdinsight.

Zie [een door de gebruiker gedefinieerde functie gebruiken met Apache Hive en Apache C# varken](apache-hadoop-hive-pig-udf-dotnet-csharp.md)voor meer informatie over het gebruik C# van de with-component en Pig.

Zie voor meer informatie C# over het gebruik van met Storm op hdinsight [topologieën ontwikkelen C# voor Apache Storm op hdinsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
