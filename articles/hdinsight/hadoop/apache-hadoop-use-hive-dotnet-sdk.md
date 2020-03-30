---
title: Apache Hive-query's uitvoeren met HDInsight .NET SDK - Azure
description: Meer informatie over het indienen van Apache Hadoop-taken bij Azure HDInsight Apache Hadoop met HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: a9d71c8aebb9cc4a0adbd461aead6e2612bd13bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552488"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Apache Hive-query's uitvoeren met HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het indienen van Apache Hive-query's met HDInsight .NET SDK. U schrijft een C#-programma om een Hive-query in te dienen voor het aanbieden van Hive-tabellen en de resultaten weer te geven.

> [!NOTE]  
> De stappen in dit artikel moeten worden uitgevoerd vanuit een Windows-client. Voor informatie over het gebruik van een Linux-, OS X- of Unix-client om met Hive te werken, gebruikt u de tabkiezer boven aan het artikel.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u de volgende objecten hebben:

* Een Apache Hadoop cluster in HDInsight. Zie [Aan de slag met Linux-gebaseerde Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Vanaf 15 september 2017 ondersteunt de HDInsight .NET SDK alleen terugkerende Hive-queryresultaten van Azure Storage-accounts. Als u dit voorbeeld gebruikt met een HDInsight-cluster dat Azure Data Lake Storage als primaire opslag gebruikt, u zoekresultaten niet ophalen met de .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 en daarna. Ten minste workload **.NET desktop ontwikkeling** moet worden geïnstalleerd.

## <a name="run-a-hive-query"></a>Een Bijenkorfquery uitvoeren

De HDInsight .NET SDK biedt .NET-clientbibliotheken, waardoor het eenvoudiger wordt om met HDInsight-clusters van .NET te werken.

1. Maak een C#-consoletoepassing in Visual Studio.

1. Voer in de Nuget Package Manager-console de volgende opdracht uit:

        Install-Package Microsoft.Azure.Management.HDInsight.Job

1. Bewerk de onderstaande code om de waarden `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`voor variabelen te initialiseren: . Gebruik vervolgens de herziene code als de volledige inhoud van **Program.cs** in Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Druk op **F5** om de toepassing uit te voeren.

De uitvoer van de aanvraag moet vergelijkbaar zijn met:

![HDInsight Hadoop Hive job output](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Apache Hive-query's indienen met HDInsight .NET SDK. Zie voor meer informatie de volgende artikelen:

* [Aan de slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK-referentie](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Apache Sqoop gebruiken met HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Toepassingen zonder interactieve verificatie voor .NET HDInsight maken](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
