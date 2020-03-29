---
title: C#, Apache Hive & Apache Pig op Apache Hadoop - Azure HDInsight
description: Meer informatie over het gebruik van C#-gebruikersgedefinieerde functies (UDF) met Apache Hive- en Apache Pig-streaming in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949386"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Gebruik C# door de gebruiker gedefinieerde functies met Apache Hive en Apache Pig op Apache Hadoop in HDInsight

Meer informatie over het gebruik van C# door gebruikers gedefinieerde functies (UDF) met [Apache Hive](https://hive.apache.org) en [Apache Pig](https://pig.apache.org) op HDInsight.

> [!IMPORTANT]
> De stappen in dit document werken met HDInsight-clusters op basis van Linux. Linux is het enige besturingssysteem dat wordt gebruikt in HDInsight-versie 3.4 of hoger. Zie [HDInsight component versioning](../hdinsight-component-versioning.md)voor meer informatie.

Zowel Hive als Pig kunnen gegevens doorgeven aan externe toepassingen voor verwerking. Dit proces staat bekend als _streaming_. Bij gebruik van een .NET-toepassing worden de gegevens doorgegeven aan de toepassing op SOAIN en de toepassing retourneert de resultaten op SOAOUT. Om te lezen en te schrijven van SODIN en SODOUT, u gebruiken `Console.ReadLine()` en `Console.WriteLine()` vanuit een console-applicatie.

## <a name="prerequisites"></a>Vereisten

* Een vertrouwdheid met het schrijven en bouwen van C# code die zich richt op .NET Framework 4.5.

    Gebruik wat IDE je wilt. We raden [Visual Studio](https://www.visualstudio.com/vs) of Visual [Studio Code](https://code.visualstudio.com/)aan. De stappen in dit document maken gebruik van Visual Studio 2019.

* Een manier om .exe-bestanden naar het cluster te uploaden en varkens- en kasten uit te voeren. We raden [Data Lake Tools aan voor Visual Studio,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md)Azure [PowerShell](/powershell/azure)en [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). De stappen in dit document gebruiken de Data Lake Tools voor Visual Studio om de bestanden te uploaden en de voorbeeldHive-query uit te voeren.

    Zie Wat is Apache Hive en [HiveQL op Azure HDInsight voor](hdinsight-use-hive.md)informatie over andere manieren om Hive-query's uit te voeren.

* Een Hadoop op HDInsight cluster. Zie [Clusters van HDInsight maken](../hdinsight-hadoop-provision-linux-clusters.md)voor meer informatie over het maken van een cluster.

## <a name="net-on-hdinsight"></a>.NET op HDInsight

*HdInsight-clusters op basis van Linux* gebruiken Mono [(omhttps://mono-project.com) ](https://mono-project.com) .NET-toepassingen uit te voeren. Mono versie 4.2.1 is inbegrepen bij HDInsight versie 3.6.

Zie [Monocompatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor meer informatie over Mono-compatibiliteit met .NET Framework-versies.

Zie [HDInsight-componentversies](../hdinsight-component-versioning.md)voor meer informatie over de versie van het .NET Framework en Mono dat bij HDInsight-versies is meegeleverd.

## <a name="create-the-c-projects"></a>De C-projecten\# maken

In de volgende secties wordt beschreven hoe u een C#-project maakt in Visual Studio voor een Apache Hive UDF en een Apache Pig UDF.

### <a name="apache-hive-udf"></a>Apache Hive UDF

Ga als lid van het c-project voor een Apache Hive UDF:

1. Start Visual Studio.

2. Selecteer **Een nieuw project maken**.

3. Kies **in het venster Een nieuw project maken** de sjabloon Console App **(.NET Framework)** (de C#-versie). Selecteer **vervolgens Volgende**.

4. Voer in het venster **Uw nieuwe project configureren** een **projectnaam** van *HiveCSharp*in en navigeer naar of maak een **locatie** om het nieuwe project op te slaan. Selecteer vervolgens **Maken**.

5. Vervang in de Visual Studio IDE de inhoud van *Program.cs* door de volgende code:

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

6. Selecteer op de menubalk **Build** > **Build Solution** om het project te bouwen.

7. Sluit de oplossing.

### <a name="apache-pig-udf"></a>Apache Varken UDF

Ga als lid van het c-project voor een Apache Hive UDF:

1. Open Visual Studio.

2. Selecteer **in** het venster Start de optie **Een nieuw project maken**.

3. Kies **in het venster Een nieuw project maken** de sjabloon Console App **(.NET Framework)** (de C#-versie). Selecteer **vervolgens Volgende**.

4. Voer in het venster **Uw nieuwe project configureren** een **projectnaam** van *PigUDF*in en ga naar of maak een **locatie** om het nieuwe project op te slaan. Selecteer vervolgens **Maken**.

5. Vervang in de Visual Studio IDE de inhoud van *Program.cs* door de volgende code:

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

    Deze code ontleedt de lijnen die vanuit `java.lang.Exception`Varken worden verzonden en maakt regels opnieuw die beginnen met .

6. Kies**build-oplossing** **bouwen** > om het project te bouwen op de menubalk.

7. Laat de oplossing open.

## <a name="upload-to-storage"></a>Uploaden naar opslag

Upload vervolgens de Hive- en Pig UDF-toepassingen naar opslag op een HDInsight-cluster.

1. Navigeer in Visual Studio naar Server Explorer **weergeven.** > **Server Explorer**

1. Klik vanuit **Server Explorer**met de rechtermuisknop op **Azure,** selecteer **Verbinding maken met Microsoft Azure-abonnement**en voltooi het aanmeldingsproces.

1. Breid het HDInsight-cluster uit waarop u deze toepassing wilt implementeren. Er wordt een vermelding met de tekst **(standaardopslagaccount)** weergegeven.

    ![Standaardopslagaccount, HDInsight-cluster, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Als dit item kan worden uitgebreid, gebruikt u een **Azure Storage-account** als standaardopslag voor het cluster. Als u de bestanden op de standaardopslag voor het cluster wilt weergeven, vouwt u de vermelding uit en dubbelklikt u vervolgens op de **(standaardcontainer).**

    * Als dit item niet kan worden uitgebreid, gebruikt u **Azure Data Lake Storage** als de standaardopslag voor het cluster. Als u de bestanden op de standaardopslag voor het cluster wilt weergeven, dubbelklikt u op de vermelding **(Standaardopslagaccount).**

1. Als u de .exe-bestanden wilt uploaden, gebruikt u een van de volgende methoden:

    * Als u een **Azure-opslagaccount gebruikt,** selecteert u het pictogram **Blob uploaden.**

        ![HDInsight uploadpictogram voor nieuw project](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Selecteer Bladeren in het dialoogvenster **Nieuw bestand uploaden** onder **Bestandsnaam**. **Browse** Ga in het dialoogvenster **Blob uploaden** naar de map *bin\debug* voor het *HiveCSharp-project* en kies het bestand *HiveCSharp.exe.* Selecteer tot slot **Openen** en vervolgens **OK** om de upload te voltooien.

    * Als u **Azure Data Lake Storage**gebruikt, klikt u met de rechtermuisknop op een leeg gebied in de bestandsvermelding en selecteert u **Uploaden**. Kies tot slot het bestand *HiveCSharp.exe* en selecteer **Openen**.

    Zodra de *Upload van HiveCSharp.exe* is voltooid, herhaalt u het uploadproces voor het *bestand PigUDF.exe.*

## <a name="run-an-apache-hive-query"></a>Een Apache Hive-query uitvoeren

Nu u een Hive-query uitvoeren die uw Hive UDF-toepassing gebruikt.

1. Navigeer in Visual Studio naar Server Explorer **weergeven.** > **Server Explorer**

2. Vouw **Azure** uit en vouw vervolgens **HDInsight** uit.

3. Klik met de rechtermuisknop op het cluster waarop u de *HiveCSharp-toepassing* hebt geïmplementeerd en selecteer **vervolgens Een bijenkorfquery schrijven**.

4. Gebruik de volgende tekst voor de Hive-query:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
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
    > Geef geen `add file` commentaar op de instructie die overeenkomt met het type standaardopslag dat voor uw cluster wordt gebruikt.

    Met deze query `clientid` `devicemake`selecteert `devicemodel` u `hivesampletable`de , en velden uit , en vervolgens worden de velden doorgegeven aan de toepassing *HiveCSharp.exe.* De query verwacht dat de toepassing drie `clientid`velden `phoneLabel`retourneert, die zijn opgeslagen als , en `phoneHash`. De query verwacht ook *HiveCSharp.exe* te vinden in de wortel van de standaardopslagcontainer.

5. Schakel de standaard **interactieve** optie **in Batch**en selecteer **Vervolgens Verzenden** om de taak in te dienen bij het HDInsight-cluster. Het venster **Overzicht van de hive-taak** wordt geopend.

6. Selecteer **Vernieuwen** om het overzicht te vernieuwen totdat **de taakstatus** is gewijzigd in **Voltooid**. Als u de taakuitvoer wilt weergeven, selecteert u **Taakuitvoer**.

## <a name="run-an-apache-pig-job"></a>Een Apache Pig-taak uitvoeren

U ook een Pig-taak uitvoeren die uw Pig UDF-toepassing gebruikt.

1. Gebruik SSH om verbinding te maken met uw HDInsight-cluster. (Voer bijvoorbeeld de `ssh sshuser@<clustername>-ssh.azurehdinsight.net`opdracht uit .) Zie [SSH withHDInsight gebruiken](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie.

2. Gebruik de volgende opdracht om de opdrachtregel Varken te starten:

    ```shell
    pig
    ```

    Er `grunt>` wordt een prompt weergegeven.

3. Voer het volgende in om een varkenstaak uit te voeren die de toepassing .NET Framework gebruikt:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    Met `DEFINE` de instructie `streamer` wordt een alias gemaakt van de `CACHE` *toepassing PigUDF.exe* en wordt deze opgehaald vanuit de standaardopslag voor het cluster. Later `streamer` wordt met `STREAM` de operator gebruikt om de `LOG` afzonderlijke regels in de afzonderlijke regels te verwerken en de gegevens als een reeks kolommen te retourneren.

    > [!NOTE]
    > De toepassingsnaam die wordt gebruikt voor \` streaming moet worden omgeven door het (backtick) teken wanneer `SHIP`aliased, en door de ' (single quote) karakter bij gebruik met .

4. Na het invoeren van de laatste regel, moet de taak beginnen. Het retourneert de uitvoer die vergelijkbaar is met de volgende tekst:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Gebruik `exit` om varken te verlaten.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u een .NET Framework-toepassing van Hive en Pig op HDInsight gebruiken. Als u wilt leren hoe python te gebruiken met Hive en Pig, zie [Gebruik Python met Apache Hive en Apache Pig in HDInsight](python-udf-hdinsight.md).

Zie de volgende artikelen voor andere manieren om Hive te gebruiken en om meer te weten te komen over het gebruik van MapReduce:

* [Apache Hive gebruiken met HDInsight](hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hdinsight-use-mapreduce.md)
* [Pig Latin Basics](https://pig.apache.org/docs/latest/basic.html)