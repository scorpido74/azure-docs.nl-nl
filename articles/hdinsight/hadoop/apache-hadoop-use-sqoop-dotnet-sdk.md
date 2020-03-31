---
title: Apache Sqoop-taken uitvoeren met .NET en HDInsight - Azure
description: Meer informatie over het gebruik van de HDInsight .NET SDK om Apache Sqoop import en export uit te voeren tussen een Apache Hadoop-cluster en een Azure SQL Database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157062"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Apache Sqoop-taken uitvoeren met .NET SDK voor Apache Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Meer informatie over het gebruik van de Azure HDInsight .NET SDK om Apache Sqoop-taken in HDInsight uit te voeren voor import en export tussen een HDInsight-cluster en een Azure SQL Database- of SQL Server-database.

## <a name="prerequisites"></a>Vereisten

* Voltooiing van [de testomgeving instellen](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) van [Apache Sqoop gebruiken met Hadoop in HDInsight.](./hdinsight-use-sqoop.md)

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Vertrouwdheid met Sqoop. Zie [Sqoop-gebruikershandleiding](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)voor meer informatie.

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Sqoop gebruiken op HDInsight-clusters met de .NET SDK

De HDInsight .NET SDK biedt .NET-clientbibliotheken, zodat het eenvoudiger is om met HDInsight-clusters van .NET te werken. In deze sectie maakt u een C#-consoletoepassing om de `hivesampletable` tabel naar de Azure SQL Database-tabel te exporteren die u hebt gemaakt op basis van de vereisten.

## <a name="set-up"></a>Instellen

1. Start Visual Studio en maak een C#-consoletoepassing.

1. Navigeer naar **NuGet** > **Package Manager** > Package**Manager Console** en voer de volgende opdracht uit:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop export

Van Hive naar SQL Server.  In dit voorbeeld worden `hivesampletable` gegevens `mobiledata` uit de tabel Hive geëxporteerd naar de tabel in SQL Database.

1. Gebruik de volgende code in het Program.cs bestand. Bewerk de code om `ExistingClusterName`de `ExistingClusterPassword`waarden in te stellen voor , en .

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Als u het programma wilt uitvoeren, selecteert u de **F5-toets.**

## <a name="sqoop-import"></a>Sqoop importeren

Van SQL Server naar Azure Storage. Dit voorbeeld is afhankelijk van de bovenstaande export.  In dit voorbeeld `mobiledata` worden gegevens uit `wasb:///tutorials/usesqoop/importeddata` de tabel in SQL Database geïmporteerd naar de map op het standaardopslagaccount van het cluster.

1. Vervang de bovenstaande `//sqoop start //sqoop end` code in het blok door de volgende code:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Als u het programma wilt uitvoeren, selecteert u de **F5-toets.**

## <a name="limitations"></a>Beperkingen

Linux-gebaseerde HDInsight presenteert de volgende beperkingen:

* Bulkexport: de Sqoop-connector die wordt gebruikt om gegevens te exporteren naar Microsoft SQL Server of Azure SQL Database, biedt momenteel geen ondersteuning voor bulkinserts.

* Batching: Door `-batch` de schakelaar te gebruiken, voert Sqoop meerdere inserts uit in plaats van de invoegbewerkingen te batcheren.

## <a name="next-steps"></a>Volgende stappen

Nu heb je geleerd hoe je Sqoop moet gebruiken. Voor meer informatie zie:

* [Apache Oozie gebruiken met HDInsight](../hdinsight-use-oozie-linux-mac.md): Gebruik Sqoop-actie in een Oozie-workflow.
* [Gegevens uploaden naar HDInsight:](../hdinsight-upload-data.md)zoek andere methoden voor het uploaden van gegevens naar HDInsight- of Azure Blob-opslag.
