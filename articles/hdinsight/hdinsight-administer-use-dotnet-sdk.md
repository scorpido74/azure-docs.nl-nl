---
title: Apache Hadoop-clusters beheren in HDInsight met .NET SDK - Azure
description: Meer informatie over het uitvoeren van administratieve taken voor de Apache Hadoop-clusters in HDInsight met HDInsight .NET SDK.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 36a77d49b507d3d0158d1b4b492d0141350de50f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240639"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Apache Hadoop-clusters in HDInsight beheren met behulp van de .NET-SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Meer informatie over het beheren van HDInsight-clusters met behulp van [HDInsight.NET SDK.](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)

**Vereisten**

Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Verbinding maken met Azure HDInsight

Je hebt de volgende NuGet-pakketten nodig:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

In het volgende codevoorbeeld ziet u hoe u verbinding maken met Azure voordat u HDInsight-clusters onder uw Azure-abonnement beheren.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

U ziet een prompt wanneer u dit programma uitvoert.  Zie [Niet-interactieve verificatie](hdinsight-create-non-interactive-authentication-dotnet-applications.md)maken als u de prompt niet wilt zien.


## <a name="list-clusters"></a>Lijstclusters

In het volgende codefragment worden clusters en enkele eigenschappen weergegeven:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik het volgende codefragment om een cluster synchroon of asynchroon te verwijderen: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Clusters schalen

Met de functie clusterschaling u het aantal werkknooppunten wijzigen dat wordt gebruikt door een cluster dat wordt uitgevoerd in Azure HDInsight zonder dat u het cluster opnieuw hoeft te maken.

> [!NOTE]  
> Alleen clusters met HDInsight-versie 3.1.3 of hoger worden ondersteund. Als u niet zeker bent van de versie van uw cluster, u de pagina Eigenschappen controleren.  Zie [Clusters weergeven en weergeven](hdinsight-administer-use-portal-linux.md#showClusters).

De impact van het wijzigen van het aantal gegevensknooppunten voor elk type cluster dat wordt ondersteund door HDInsight:

* Apache Hadoop
  
    U het aantal werknemersknooppunten in een Hadoop-cluster dat wordt uitgevoerd naadloos verhogen zonder dat dit gevolgen heeft voor lopende of lopende taken. Nieuwe taken kunnen ook worden ingediend terwijl de bewerking in volle gang is. Fouten in een schaalbewerking worden op een elegante manier verwerkt, zodat het cluster altijd in een functionele status wordt achtergelaten.
  
    Wanneer een Hadoop-cluster wordt verkleind door het aantal gegevensknooppunten te verminderen, worden sommige services in het cluster opnieuw gestart. Hierdoor mislukken alle lopende en in behandeling zijnde taken bij het voltooien van de schaalbewerking. U de taken echter opnieuw indienen zodra de bewerking is voltooid.
* Apache HBase
  
    U knooppunten naadloos toevoegen of verwijderen aan uw HBase-cluster terwijl deze wordt uitgevoerd. Regionale servers worden automatisch in evenwicht gebracht binnen een paar minuten na het voltooien van de schalingsbewerking. U de regionale servers echter ook handmatig in evenwicht brengen door in te loggen op de headnode van het cluster en de volgende opdrachten uit te voeren vanuit een opdrachtpromptvenster:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    U gegevensknooppunten naadloos toevoegen of verwijderen aan uw Storm-cluster terwijl deze wordt uitgevoerd. Maar na een succesvolle voltooiing van de schalingbewerking, moet u de topologie opnieuw in evenwicht brengen.
  
    Het opnieuw in evenwicht brengen kan op twee manieren worden bereikt:
  
  * Gebruikersinterface van stormweb
  * Cli (Command Line Interface)
    
    Raadpleeg de [Apache Storm documentatie](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) voor meer details.
    
    De gebruikersinterface van het Storm-web is beschikbaar op het HDInsight-cluster:
    
    ![HDInsight Storm schaal herbalans](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Hier is een voorbeeld van hoe u de opdracht CLI gebruikt om de stormtopologie opnieuw in evenwicht te brengen:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

In het volgende codefragment ziet u hoe u het formaat van een cluster synchroon of asynchroon wijzigen:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Toegang verlenen/intrekken

HDInsight-clusters hebben de volgende HTTP-webservices (al deze services hebben RESTful-eindpunten):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Standaard worden deze services verleend voor toegang. U de toegang intrekken/verlenen. Intrekken:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Toekenning van:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Door de toegang toe te kennen/in te trekken, reset u de gebruikersnaam en het wachtwoord van het cluster opnieuw.

Dit kan ook via de Portal. Zie [Apache Hadoop-clusters beheren in HDInsight met behulp van de Azure-portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP-gebruikersreferenties bijwerken

Het is dezelfde procedure als HTTP-toegang verlenen/intrekken.  Als het cluster de HTTP-toegang heeft gekregen, moet u het cluster eerst intrekken.  En geef de toegang vervolgens met nieuwe HTTP-gebruikersreferenties.

## <a name="find-the-default-storage-account"></a>Het standaardopslagaccount zoeken

In het volgende codefragment wordt uitgelegd hoe u de standaardnaam van het opslagaccount en de standaardopslagaccountsleutel voor een cluster krijgen.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Vacatures indienen

**MapReduce-taken verzenden**

Zie [Map uitvoeren Verlaag voorbeelden in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive-taken indienen** 

Zie [Apache Hive-query's uitvoeren met .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Apache Sqoop-taken indienen**

Zie [Apache Sqoop gebruiken met HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Apache Oozie-taken indienen**

Zie [Apache Oozie gebruiken met Hadoop om een werkstroom in HDInsight te definiëren en uit te voeren.](hdinsight-use-oozie-linux-mac.md)

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob-opslag

Zie [Gegevens uploaden naar HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zie ook

* [HDInsight .NET SDK-referentiedocumentatie](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)
* [Apache Hadoop-clusters beheren in HDInsight met behulp van de Azure-portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met behulp van een command-line interface][hdinsight-admin-cli]
* [HDInsight-clusters maken][hdinsight-provision]
* [Gegevens uploaden naar HDInsight][hdinsight-upload-data]
* [Aan de slag met Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
