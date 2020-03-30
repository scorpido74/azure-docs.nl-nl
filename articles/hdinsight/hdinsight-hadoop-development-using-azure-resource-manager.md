---
title: Migreren naar Azure Resource Manager-hulpprogramma's voor HDInsight
description: Migreren naar Azure Resource Manager-ontwikkeltools voor HDInsight-clusters
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 76eb3a135f7a32a30cfa62546a644bc77cf39998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934589"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migreren naar op Azure Resource Manager gebaseerde ontwikkeltools voor HDInsight-clusters

HDInsight is het afkeuren van op Azure Service Manager (ASM)-gebaseerde tools voor HDInsight. Als u Azure PowerShell, Azure Classic CLI of de HDInsight .NET SDK hebt gebruikt om met HDInsight-clusters te werken, wordt u aangemoedigd om de Azure Resource Manager-versies van PowerShell, CLI en .NET SDK in de toekomst te gebruiken. In dit artikel vindt u tips over het migreren naar de nieuwe op Resource Manager gebaseerde benadering. Waar van toepassing, dit document wijst op de verschillen tussen de ASM en Resource Manager benaderingen voor HDInsight.

> [!IMPORTANT]  
> De ondersteuning voor het op ASM gebaseerde PowerShell, CLI en .NET SDK wordt op **1 januari 2017 stopgezet.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Azure Classic CLI migreren naar Azure Resource Manager

> [!IMPORTANT]  
> Azure CLI biedt geen ondersteuning voor het werken met HDInsight-clusters. U Azure Classic CLI nog steeds gebruiken met HDInsight, maar Azure Classic CLI is afgeschaft.

Hieronder volgen de basisopdrachten voor het werken met HDInsight via Azure CLassic CLI:

* `azure hdinsight cluster create`- creëert een nieuw HDInsight-cluster
* `azure hdinsight cluster delete`- verwijdert een bestaand HDInsight-cluster
* `azure hdinsight cluster show`- informatie over een bestaand cluster weergeven
* `azure hdinsight cluster list`- geeft hdinsight-clusters voor uw Azure-abonnement weer

Gebruik `-h` de schakelaar om de parameters en schakelaars te inspecteren die beschikbaar zijn voor elke opdracht.

### <a name="new-commands"></a>Nieuwe opdrachten
Nieuwe opdrachten die beschikbaar zijn met Azure Resource Manager zijn:

* `azure hdinsight cluster resize`- wijzigt dynamisch het aantal werknemersknooppunten in het cluster
* `azure hdinsight cluster enable-http-access`- https toegang tot het cluster (standaard ingeschakeld)
* `azure hdinsight cluster disable-http-access`- http's toegang tot het cluster uitschakelt
* `azure hdinsight script-action`- biedt opdrachten voor het maken/beheren van Scriptacties in een cluster
* `azure hdinsight config`- biedt opdrachten voor het maken van een `hdinsight cluster create` configuratiebestand dat met de opdracht kan worden gebruikt om configuratiegegevens te verstrekken.

### <a name="deprecated-commands"></a>Afgeschafte opdrachten
Als u `azure hdinsight job` de opdrachten gebruikt om taken in te dienen bij uw HDInsight-cluster, zijn deze opdrachten niet beschikbaar via de opdrachten ResourceManager. Als u taken programmatisch moet indienen bij HDInsight vanuit scripts, moet u in plaats daarvan de REST API's van HDInsight gebruiken. Zie de volgende documenten voor meer informatie over het indienen van taken met REST API's.

* [Map uitvoerenWerk verminderen met Hadoop op HDInsight met behulp van cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Apache Hive-query's uitvoeren met Apache Hadoop op HDInsight met behulp van cURL](hadoop/apache-hadoop-use-hive-curl.md)


Voor informatie over andere manieren om Apache Hadoop MapReduce, Apache Hive en Apache Pig interactief uit te voeren, zie [MapReduce gebruiken met Hadoop op HDInsight](hadoop/hdinsight-use-mapreduce.md), [Apache Hive gebruiken met Apache Hadoop op HDInsight](hadoop/hdinsight-use-hive.md)en Apache Pig gebruiken met Apache [Hadoop op HDInsight](hadoop/hdinsight-use-pig.md).

### <a name="examples"></a>Voorbeelden
**Een cluster maken**

* Oude opdracht (ASM) -`azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nieuwe opdracht -`azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Een cluster verwijderen**

* Oude opdracht (ASM) -`azure hdinsight cluster delete myhdicluster`
* Nieuwe opdracht -`azure hdinsight cluster delete mycluster -g myresourcegroup`

**Lijstclusters**

* Oude opdracht (ASM) -`azure hdinsight cluster list`
* Nieuwe opdracht -`azure hdinsight cluster list`

> [!NOTE]  
> Als u de opdracht Lijst opgeeft, `-g` wordt alleen de clusters in de opgegeven resourcegroep teruggekeerd door de resourcegroep op te geven.

**Clusterinformatie weergeven**

* Oude opdracht (ASM) -`azure hdinsight cluster show myhdicluster`
* Nieuwe opdracht -`azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure PowerShell migreren naar Azure Resource Manager
De algemene informatie over Azure PowerShell in de Azure Resource Manager-modus is te vinden bij [Azure PowerShell gebruiken met Azure Resource Manager](../powershell-azure-resource-manager.md).

De Azure PowerShell Resource Manager-cmdlets kunnen naast elkaar worden geïnstalleerd met de ASM-cmdlets. De cmdlets van de twee modi kunnen worden onderscheiden door hun namen.  De resourcebeheermodus heeft *AzHDInsight* in de cmdlet-namen in vergelijking met *AzureHDInsight* in de ASM-modus.  Bijvoorbeeld *New-AzHDInsightCluster* vs. *New-AzureHDInsightCluster*. Parameters en switches kunnen nieuwsnamen bevatten en er zijn veel nieuwe parameters beschikbaar bij het gebruik van Resource Manager.  Voor meerdere cmdlets is bijvoorbeeld een nieuwe switch vereist die *-ResourceGroupName wordt genoemd.* 

Voordat u de HDInsight-cmdlets gebruiken, moet u verbinding maken met uw Azure-account en een nieuwe brongroep maken:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Omgedoopt tot cmdlets
Ga als bedoeld als een lijst van de HDInsight ASM-cmdlets in de Windows PowerShell-console:

    help *azurehdinsight*

In de volgende tabel worden de ASM-cmdlets en hun namen weergegeven in de resourcemanagermodus:

| ASM-cmdlets | Cmdlets Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-azHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-azHDInsightStorage](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-azHDInsightJobOutput](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](https://docs.microsoft.com/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-azHDInsightHiveJob](https://docs.microsoft.com/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[Nieuw-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[Nieuw-AzHDInsightClusterConfig](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[Nieuwe-azHDInsightHiveJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[Nieuwe-azHDInsightmapreduceJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[Nieuwe-azHDInsightPigJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[Nieuwe-AzHDInsightSqoopJobDefinition](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[Nieuwe-azHDInsightstreamingmapreducejobdefinitie](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](https://docs.microsoft.com/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-azHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Standaardopslag voor azHDInsight instellen](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Gebruik-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-azHDInsightJob](https://docs.microsoft.com/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nieuwe cmdLets
Hieronder volgen de nieuwe cmdlets die alleen beschikbaar zijn in de Resource Manager-modus. 

**Cmdlets met scriptactie:**

* **Get-AzHDInsightPersistedScriptAction:** Hiermee worden de aanhoudende scriptacties voor een cluster weergegeven en deze in chronologische volgorde weergegeven, of worden details voor een opgegeven aanhoudende scriptactie weergegeven. 
* **Get-AzHDInsightScriptActionHistory**: Krijgt de scriptactiegeschiedenis voor een cluster en geeft deze in omgekeerde chronologische volgorde, of krijgt details van een eerder uitgevoerde scriptactie. 
* **Remove-AzHDInsightPersistedScriptAction:** Verwijdert een aanhoudende scriptactie uit een HDInsight-cluster.
* **Set-AzHDInsightPersistedScriptAction**: Hiermee stelt u een eerder uitgevoerde scriptactie in als een aanhoudende scriptactie.
* **Submit-AzHDInsightScriptAction**: Verzendt een nieuwe scriptactie naar een Azure HDInsight-cluster. 

Zie Op Linux [gebaseerde HDInsight-clusters aanpassen met Scriptactie](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik.

**Clusteridentiteitgerelateerde cmdlets:**

* **Add-AzHDInsightClusterIdentity**: Voegt een clusteridentiteit toe aan een clusterconfiguratieobject, zodat het HDInsight-cluster toegang heeft tot Azure Data Lake Storage. Zie [Een HDInsight-cluster maken met Data Lake Storage met Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Voorbeelden
**Cluster maken**

Oude opdracht (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Nieuwe opdracht:

    New-AzHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Cluster verwijderen**

Oude opdracht (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Nieuwe opdracht:

    Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Lijstcluster**

Oude opdracht (ASM):

    Get-AzureHDInsightCluster

Nieuwe opdracht:

    Get-AzHDInsightCluster 

**Cluster weergeven**

Oude opdracht (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Nieuwe opdracht:

    Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Andere monsters
* [HDInsight-clusters maken](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Apache Hive-taken indienen](hadoop/apache-hadoop-use-hive-powershell.md)
* [Apache Sqoop-vacatures indienen](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migreren naar de nieuwe HDInsight .NET SDK
De OP Azure Service Management gebaseerde [(ASM) HDInsight .NET SDK](https://msdn.microsoft.com/library/azure/mt416619.aspx) is nu afgeschaft. U wordt aangemoedigd om de [HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight)op basis van Azure Resource Management te gebruiken. De volgende ASM-gebaseerde HDInsight-pakketten worden afgeschaft.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

In deze sectie vindt u meer informatie over het uitvoeren van bepaalde taken met behulp van de SDK op basis van Resource Manager.

| Procedures... met behulp van de OP Resource Manager gebaseerde HDInsight SDK | Koppelingen |
| --- | --- |
| Azure HDInsight-SDK voor .NET|Zie [Azure HDInsight SDK voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet) |
| Toepassingen interactief verifiëren met Azure Active Directory met .NET SDK |Zie [Apache Hive-query's uitvoeren met .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). Het codefragment in dit artikel maakt gebruik van de interactieve verificatiebenadering. |
| Toepassingen niet interactief verifiëren met Azure Active Directory met .NET SDK |Zie [Niet-interactieve toepassingen maken voor HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Een Apache Hive-taak verzenden met .NET SDK |Zie [Apache Hive-vacatures verzenden](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Een Apache Sqoop-taak indienen met .NET SDK |Zie [Apache Sqoop-taken verzenden](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| HDInsight-clusters weergeven met .NET SDK |[Zie Lijst HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| HDInsight-clusters schalen met .NET SDK |Zie [Scale HDInsight clusters](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Toegang tot HDInsight-clusters verlenen/intrekken met .NET SDK |Zie [De toegang tot HDInsight-clusters verlenen/intrekken](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| HTTP-gebruikersreferenties voor HDInsight-clusters bijwerken met .NET SDK |Zie [HTTP-gebruikersreferenties bijwerken voor HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Het standaardopslagaccount voor HDInsight-clusters zoeken met .NET SDK |Zie [Het standaardopslagaccount voor HDInsight-clusters zoeken](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| HDInsight-clusters verwijderen met .NET SDK |Zie [HDInsight-clusters verwijderen](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Voorbeelden
Hieronder volgen enkele voorbeelden over hoe een bewerking wordt uitgevoerd met behulp van de OP ASM gebaseerde SDK en het gelijkwaardige codefragment voor de SDK op basis van Resource Manager.

**Een cluster-CRUD-client maken**

* Oude opdracht (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Nieuwe opdracht (Service principal authorization)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Nieuwe opdracht (gebruikersautorisatie)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Een cluster maken**

* Oude opdracht (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Nieuwe opdracht
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**HTTP-toegang inschakelen**

* Oude opdracht (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Nieuwe opdracht
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Een cluster verwijderen**

* Oude opdracht (ASM)
  
        client.DeleteCluster(dnsName);
* Nieuwe opdracht
  
        client.Clusters.Delete(resourceGroup, dnsname);

