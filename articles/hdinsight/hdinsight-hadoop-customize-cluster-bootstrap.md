---
title: Azure HDInsight-clusterconfiguraties aanpassen met start-ups
description: Meer informatie over het programmatisch aanpassen van hdInsight-clusterconfiguratie met behulp van sjablonen .Net, PowerShell en Resource Manager.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e641340ac04415ee4a20cda2bc09bbdbef9802a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272524"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>HDInsight-clusters aanpassen met Bootstrap

Met Bootstrap-scripts u onderdelen in Azure HDInsight programmatisch installeren en configureren.

Er zijn drie benaderingen om configuratiebestandsinstellingen in te stellen wanneer uw HDInsight-cluster wordt gemaakt:

* Azure PowerShell gebruiken
* .NET SDK gebruiken
* Sjabloon Azure Resource Manager gebruiken

Met deze programmatische methoden u bijvoorbeeld opties in deze bestanden configureren:

* clusterIdentiteit.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* stormsite.xml
* tez-site.xml
* webhcat-site.xml
* garen-site.xml
* server.properties (kafka-broker configuratie)

Zie [HDInsight-clusters aanpassen met Scriptactie (Linux)](hdinsight-hadoop-customize-cluster-linux.md)voor informatie over het installeren van extra componenten op het HDInsight-cluster tijdens de creatietijd.

## <a name="prerequisites"></a>Vereisten

* Als u PowerShell gebruikt, hebt u de [Az-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Met de volgende PowerShell-code wordt een [Apache Hive-configuratie](https://hive.apache.org/) aangepast:

> [!IMPORTANT]  
> De `Spark2Defaults` parameter moet mogelijk worden gebruikt met [Add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). U lege waarden doorgeven aan de parameter zoals weergegeven in het onderstaande codevoorbeeld.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90s" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config
```

Een volledig werkend PowerShell-script is te vinden in [bijlage.](#appendix-powershell-sample)

**Ga als volgt te werk om de wijziging te verifiëren:**

1. Navigeer `https://CLUSTERNAME.azurehdinsight.net/` naar `CLUSTERNAME` waar de naam van uw cluster is.
1. Navigeer in het linkermenu naar **Hive** > **Configs** > **Advanced.**
1. Geavanceerde **hive-site**uitbreiden.
1. Zoek **hive.metastore.client.socket.time-out** en bevestig dat de waarde **90 is.**

Nog meer voorbeelden over het aanpassen van andere configuratiebestanden:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```

## <a name="use-net-sdk"></a>.NET SDK gebruiken

Zie [Azure HDInsight SDK voor .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).

## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken

U bootstrap gebruiken in de sjabloon Resourcemanager:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop past sjabloon clusterbootstrap Azure Resource Manager aan](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Zie ook

* [Apache Hadoop-clusters maken in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) biedt instructies voor het maken van een HDInsight-cluster met behulp van andere aangepaste opties.
* [Scriptactiescripts ontwikkelen voor HDInsight](hdinsight-hadoop-script-actions-linux.md)
* [Apache Spark installeren en gebruiken op HDInsight-clusters](spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Installeer en gebruik Apache Giraph op HDInsight clusters.](hdinsight-hadoop-giraph-install.md)

## <a name="appendix-powershell-sample"></a>Aanhangsel: PowerShell-monster

Dit PowerShell-script maakt een HDInsight-cluster en past een Hive-instelling aan. Zorg ervoor dat `$nameToken`u `$httpPassword`waarden `$sshPassword`invoert voor , en .

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>"
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = '<ENTER A PASSWORD>'

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = '<ENTER A PASSWORD>'
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US"
#endregion


####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1

# Note: Storage account kind BlobStorage cannot be used as primary storage.

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value

$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey

New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90s"}

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValue `
        -HiveSite $hiveConfigValues `
        -Spark2Defaults @{}

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster `
    -ClusterName $hdinsightClusterName

#endregion
```
