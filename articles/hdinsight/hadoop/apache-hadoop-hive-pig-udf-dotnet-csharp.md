---
title: C#, Apache Hive & Apache-varken op Apache Hadoop-Azure HDInsight
description: Meer informatie over het C# gebruik van door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache Pig-streaming in azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 222b91b2efefa81186d32fee7229aa0cc4f13a63
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044597"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Door C# de gebruiker gedefinieerde functies gebruiken met Apache Hive en Apache varken op Apache Hadoop in HDInsight

Meer informatie over het C# gebruik van door de gebruiker gedefinieerde functies (UDF) met Apache Hive en Apache varken op HDInsight.

> [!IMPORTANT]
> De stappen in dit document werken met HDInsight-clusters op basis van Linux en Windows. Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [versie beheer van HDInsight-onderdelen](../hdinsight-component-versioning.md)voor meer informatie.

Zowel Hive als Pig kunnen gegevens door geven aan externe toepassingen voor verwerking. Dit proces wordt ook wel _streaming_genoemd. Wanneer u een .NET-toepassing gebruikt, worden de gegevens door gegeven aan de toepassing op STDIN en de toepassing retourneert de resultaten op STDOUT. Als u wilt lezen en schrijven vanuit STDIN en STDOUT, kunt u `Console.ReadLine()` en `Console.WriteLine()` van een console toepassing gebruiken.

## <a name="prerequisites"></a>Vereisten

* Een vertrouwdheid met het schrijven en C# maken van code die gericht is op .NET Framework 4,5.

    * Gebruik de gewenste IDE. We raden [Visual studio](https://www.visualstudio.com/vs) 2015, 2017 of [Visual Studio code](https://code.visualstudio.com/)aan. De stappen in dit document gebruiken Visual Studio 2017.

* Een manier om exe-bestanden naar het cluster te uploaden en Pig-en Hive-taken uit te voeren. U wordt aangeraden de Data Lake-Hulpprogram Ma's voor Visual Studio, Azure PowerShell en Azure Classic CLI uit te voeren. In de stappen in dit document worden de Data Lake-Hulpprogram Ma's voor Visual Studio gebruikt om de bestanden te uploaden en de voorbeeld query van de Hive uit te voeren.

    Voor informatie over andere manieren om Hive-query's en Pig-taken uit te voeren, raadpleegt u de volgende documenten:

    * [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)

    * [Apache Pig gebruiken met HDInsight](hdinsight-use-pig.md)

* Een Hadoop in HDInsight-cluster. Zie [een HDInsight-cluster maken](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een cluster.

## <a name="net-on-hdinsight"></a>.NET in HDInsight

* __Op Linux gebaseerde HDInsight-__ clusters met [mono (https://mono-project.com)](https://mono-project.com) om .NET-toepassingen uit te voeren. Mono versie 4.2.1 is opgenomen in HDInsight-versie 3,6.

    Zie [mono-compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over de compatibiliteit van mono met .NET Framework versies.

* __HDInsight-clusters op basis van Windows__ gebruiken de Microsoft .NET CLR om .NET-toepassingen uit te voeren.

Zie [hdinsight-onderdeel versies](../hdinsight-component-versioning.md)voor meer informatie over de versie van .NET Framework en mono, opgenomen in hdinsight-versies.

## <a name="create-the-c-projects"></a>De C\#-projecten maken

### <a name="apache-hive-udf"></a>Apache Hive UDF

1. Open Visual Studio en maak een oplossing. Selecteer voor het project type **console-app (.NET Framework)** en geef het nieuwe project de naam **HiveCSharp**.

    > [!IMPORTANT]
    > Selecteer __.NET Framework 4,5__ als u gebruikmaakt van een HDInsight-cluster op basis van Linux. Zie [mono-compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over de compatibiliteit van mono met .NET Framework versies.

2. Vervang de inhoud van **Program.cs** door de volgende code:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Maak het project.

### <a name="apache-pig-udf"></a>Apache-Pig UDF

1. Open Visual Studio en maak een oplossing. Voor het project type selecteert u **console toepassing**en geeft u de naam van het nieuwe project **PigUDF**.

2. Vervang de inhoud van het **Program.cs** -bestand door de volgende code:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Met deze code worden de regels geparseerd die zijn verzonden van Pig en worden de regels die beginnen met `java.lang.Exception`opnieuw ingedeeld.

3. Sla **Program.cs**op en bouw het project.

## <a name="upload-to-storage"></a>Uploaden naar opslag

1. Open **Server Explorer**in Visual Studio.

2. Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

3. Voer desgevraagd uw Azure-abonnements referenties in en klik op **Aanmelden**.

4. Vouw het HDInsight-cluster uit dat u wilt gebruiken om deze toepassing te implementeren. Een vermelding met de tekst __(standaard opslag account)__ wordt weer gegeven.

    ![Server Explorer het opslag account voor het cluster wordt weer gegeven](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Als dit item kan worden uitgevouwen, gebruikt u een __Azure Storage-account__ als standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, vouwt u de vermelding uit en dubbelklikt u vervolgens op de __(standaard container)__ .

    * Als dit item niet kan worden uitgevouwen, gebruikt u __Azure data Lake Storage__ als de standaard opslag voor het cluster. Als u de bestanden op de standaard opslag voor het cluster wilt weer geven, dubbelklikt u op de vermelding __(standaard opslag account)__ .

6. Gebruik een van de volgende methoden om de exe-bestanden te uploaden:

   * Als u een __Azure Storage-account__gebruikt, klikt u op het pictogram uploaden en bladert u naar de map **Bin\debug** voor het **HiveCSharp** -project. Ten slotte selecteert u het bestand **HiveCSharp. exe** en klikt u op **OK**.

       ![Pictogram voor het uploaden van HDInsight voor nieuw project](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)
    
   * Als u __Azure data Lake Storage__gebruikt, klikt u met de rechter muisknop op een leeg gebied in de bestands vermelding en selecteert u vervolgens __uploaden__. Ten slotte selecteert u het bestand **HiveCSharp. exe** en klikt u op **openen**.

     Wanneer het uploaden van __HiveCSharp. exe__ is voltooid, herhaalt u het upload proces voor het bestand __PigUDF. exe__ .

## <a name="run-an-apache-hive-query"></a>Een Apache Hive query uitvoeren

1. Open **Server Explorer**in Visual Studio.

2. Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

3. Klik met de rechter muisknop op het cluster waarop u de **HiveCSharp** -toepassing hebt geïmplementeerd en selecteer vervolgens **een Hive-query schrijven**.

4. Gebruik de volgende tekst voor de Hive-query:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Verwijder de opmerking bij de `add file`-instructie die overeenkomt met het type standaard opslag dat voor uw cluster wordt gebruikt.

    Met deze query worden de velden `clientid`, `devicemake`en `devicemodel` geselecteerd van `hivesampletable`en worden de velden door gegeven aan de toepassing HiveCSharp. exe. De query verwacht dat de toepassing drie velden retourneert, die worden opgeslagen als `clientid`, `phoneLabel`en `phoneHash`. De query verwacht ook HiveCSharp. exe te vinden in de hoofdmap van de standaard-opslag container.

5. Klik op **verzenden** om de taak naar het HDInsight-cluster te verzenden. Het venster **samen vatting van Hive-taak** wordt geopend.

6. Klik op **vernieuwen** om de samen vatting te vernieuwen totdat de **taak status** is gewijzigd in **voltooid**. Als u de taak uitvoer wilt weer geven, klikt u op **taak uitvoer**.

## <a name="run-an-apache-pig-job"></a>Een Apache Pig-taak uitvoeren

1. Gebruik SSH om verbinding te maken met uw HDInsight-cluster. Bijvoorbeeld `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Zie [SSH-WithHDInsight gebruiken](../hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

2. Gebruik een van de volgende opdrachten om de Pig-opdracht regel te starten:

        pig

    > [!IMPORTANT]
    > Als u gebruikmaakt van een Windows-cluster, gebruikt u in plaats daarvan de volgende opdrachten:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Er wordt een `grunt>` prompt weer gegeven.

3. Voer het volgende in om een Pig-taak uit te voeren die gebruikmaakt van de .NET Framework-toepassing:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    De instructie `DEFINE` maakt een alias van `streamer` voor de toepassingen pigudf. exe en `CACHE` laadt deze vanuit de standaard opslag voor het cluster. Later wordt `streamer` met de operator `STREAM` gebruikt voor het verwerken van de enkelvoudige regels in het logboek en het retour neren van de gegevens als een reeks kolommen.

    > [!NOTE]
    > De naam van de toepassing die wordt gebruikt voor streaming moet tussen het \`-teken (apostroffen) staan wanneer de alias en ' (enkele aanhalings tekens) worden gebruikt in combi natie met `SHIP`.

4. Nadat de laatste regel is ingevoerd, wordt de taak gestart. Het resultaat van de uitvoer is vergelijkbaar met de volgende tekst:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u een .NET Framework-toepassing kunt gebruiken uit Hive en Pig op HDInsight. Als u wilt weten hoe u python met hive en Pig kunt gebruiken, raadpleegt u [python gebruiken met Apache Hive en Apache varken in HDInsight](python-udf-hdinsight.md).

Raadpleeg de volgende documenten voor andere manieren om Pig en Hive te gebruiken, en voor meer informatie over het gebruik van MapReduce:

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hdinsight-use-pig.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)
