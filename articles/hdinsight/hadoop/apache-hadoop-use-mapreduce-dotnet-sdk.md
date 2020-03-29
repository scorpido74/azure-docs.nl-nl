---
title: Kaart verzendenTaken verminderen met HDInsight .NET SDK - Azure
description: Meer informatie over het indienen van mapWerk taken naar Azure HDInsight Apache Hadoop verzenden met HDInsight .NET SDK.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157046"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce-taken uitvoeren met behulp van de HDInsight .NET-SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Meer informatie over het indienen van MapReduce-taken met HDInsight .NET SDK. HDInsight clusters worden geleverd met een pot bestand met een aantal MapReduce monsters. Het potbestand `/example/jars/hadoop-mapreduce-examples.jar`is.  Een van de monsters is **wordcount**. U ontwikkelt een C#-consoletoepassing om een wordcount-taak in te dienen.  De taak `/example/data/gutenberg/davinci.txt` leest het bestand en `/example/data/davinciwordcount`leidt de resultaten uit naar .  Als u de toepassing opnieuw wilt uitvoeren, moet u de uitvoermap opschonen.

> [!NOTE]  
> De stappen in dit artikel moeten worden uitgevoerd vanuit een Windows-client. Voor informatie over het gebruik van een Linux-, OS X- of Unix-client om met Hive te werken, gebruikt u de tabkiezer boven aan het artikel.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster op HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Kaart verzendenWerk verminderen met HDInsight .NET SDK

De HDInsight .NET SDK biedt .NET-clientbibliotheken, waarmee u eenvoudiger werken met HDInsight-clusters van .NET.

1. Start Visual Studio en maak een C#-consoletoepassing.

1. Navigeer naar **NuGet** > Package**Manager-console** **NuGet Package Manager** > en voer de volgende opdracht in:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Kopieer de onderstaande code in **Program.cs**. Bewerk vervolgens de code door `existingClusterName`de `existingClusterPassword` `defaultStorageAccountName`waarden `defaultStorageAccountKey`in `defaultStorageContainerName`te stellen voor: , , , en .

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Druk op **F5** om de toepassing uit te voeren.

Als u de taak opnieuw wilt uitvoeren, moet u de `/example/data/davinciwordcount`naam van de taakuitvoermap wijzigen in het voorbeeld .

Wanneer de taak is voltooid, wordt de inhoud `part-r-00000`van het uitvoerbestand in de toepassing afgedrukt.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende manieren geleerd om een HDInsight-cluster te maken. Zie voor meer informatie de volgende artikelen:

* Zie [Apache Hive-query's uitvoeren met HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md)voor het indienen van een Hive-taak.
* Zie [Linux-gebaseerde Apache Hadoop-clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)voor het maken van HDInsight-clusters.
* Zie Clusters van [Apache Hadoop beheren in HDInsight](../hdinsight-administer-use-portal-linux.md)voor het beheren van HDInsight-clusters.
* Zie [HDInsight .NET SDK-referentie](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)voor het leren van de HDInsight .NET SDK.
* Zie [Niet-interactieve verificatie .NET HDInsight-toepassingen maken](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)voor niet-interactieve verificatie naar Azure.