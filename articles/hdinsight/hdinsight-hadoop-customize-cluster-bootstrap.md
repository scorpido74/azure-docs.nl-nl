---
title: Configuraties van Azure HDInsight-clusters aanpassen met Boots trap
description: Meer informatie over hoe u de configuratie van HDInsight-clusters programmatisch kunt aanpassen met behulp van .net-, Power shell-en Resource Manager-sjablonen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/19/2019
ms.openlocfilehash: 15d08b14e38f097e8e9c3e0db893efb1d6efe44d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098674"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>HDInsight-clusters aanpassen met Boots trap

Met Boots trap scripts kunt u programmatisch onderdelen installeren en configureren in azure HDInsight.

Er zijn drie benaderingen om instellingen van het configuratie bestand in te stellen wanneer uw HDInsight-cluster wordt gemaakt:

* Azure PowerShell gebruiken
* .NET SDK gebruiken
* Azure Resource Manager-sjabloon gebruiken

Met deze programmatische methoden kunt u bijvoorbeeld opties in deze bestanden configureren:

* clusterIdentity.xml
* bestand core-site. XML
* gateway. XML
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* Hive-site. XML
* mapred-site
* oozie-site.xml
* oozie-env.xml
* Storm-site. XML
* tez-site.xml
* webhcat-site.xml
* yarn-site. XML
* server. Properties (Kafka-Broker-configuratie)

Zie [HDInsight-clusters aanpassen met script Action (Linux)](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het installeren van extra onderdelen in het HDInsight-cluster tijdens de aanmaak tijd.

## <a name="prerequisites"></a>Vereisten

* Als u Power shell gebruikt, hebt u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

## <a name="use-azure-powershell"></a>Azure PowerShell gebruiken

Met de volgende Power shell-code wordt een [Apache Hive](https://hive.apache.org/) configuratie aangepast:

> [!IMPORTANT]  
> De para `Spark2Defaults` meter moet mogelijk worden gebruikt met [add-AzHDInsightConfigValue](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightconfigvalue). U kunt lege waarden door geven aan de para meter, zoals wordt weer gegeven in het code voorbeeld hieronder.

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

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

Een volledig werkend Power shell-script vindt u in [bijlage](#appendix-powershell-sample).

**De wijziging controleren:**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik in het menu links op **HDInsight-clusters**. Als u dit niet ziet, klikt u eerst op **alle services** .
3. Klik op het cluster dat u zojuist hebt gemaakt met behulp van het Power shell-script.
4. Klik op **dash board** aan de bovenkant van de Blade om de Ambari-gebruikers interface te openen.
5. Klik op **Hive** in het menu links.
6. Klik op **HiveServer2** in **samen vatting**.
7. Klik op het tabblad **configuratie** .
8. Klik op **Hive** in het menu links.
9. Klik op het tabblad **Geavanceerd**.
10. Schuif omlaag en vouw vervolgens **Geavanceerde Hive-site**uit.
11. Zoek naar **Hive. meta Store. client. socket. timeout** in de sectie.

Meer voor beelden over het aanpassen van andere configuratie bestanden:

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
Zie [Linux-gebaseerde clusters maken in HDInsight met behulp van de .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Resource Manager-sjabloon gebruiken
U kunt Boots trap gebruiken in Resource Manager-sjabloon:

```json
"configurations": {
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![Hadoop-cluster Azure Resource Manager sjabloon aanpassen](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Zie ook

* [Apache Hadoop clusters maken in HDInsight][hdinsight-provision-cluster] vindt u instructies voor het maken van een HDInsight-cluster met behulp van andere aangepaste opties.
* [Script actie scripts voor HDInsight ontwikkelen][hdinsight-write-script]
* [Apache Spark op HDInsight-clusters installeren en gebruiken][hdinsight-install-spark]
* [Installeer en gebruik Apache Giraph in HDInsight-clusters](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions-linux.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fasen tijdens het maken van het cluster"

## <a name="appendix-powershell-sample"></a>Nummer Voorbeeld van PowerShell

Met dit Power shell-script maakt u een HDInsight-cluster en past u een Hive-instelling aan. Zorg ervoor dat u waarden opgeeft `$nameToken`voor `$httpPassword`,, `$sshPassword`en.

> [!WARNING]  
> Het type `BlobStorage` opslag account kan niet worden gebruikt voor HDInsight-clusters.

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
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

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
