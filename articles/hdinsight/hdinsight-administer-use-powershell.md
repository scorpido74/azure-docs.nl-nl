---
title: Apache Hadoop-clusters beheren met PowerShell - Azure HDInsight
description: Meer informatie over het uitvoeren van administratieve taken voor de Apache Hadoop-clusters in HDInsight met Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/13/2020
ms.openlocfilehash: 104975e6424ed96d43434a588997957033c31d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560351"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop-clusters beheren in HDInsight met Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan worden gebruikt om de implementatie en het beheer van uw workloads in Azure te beheren en te automatiseren. In dit artikel leert u hoe u [Apache Hadoop-clusters](https://hadoop.apache.org/) in Azure HDInsight beheert met behulp van de Azure PowerShell Az-module. Zie de referentie van [Az.HDInsight voor](https://docs.microsoft.com/powershell/module/az.hdinsight)de lijst met de CMDLets van HDInsight.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De PowerShell [Az Module](https://docs.microsoft.com/powershell/azure/overview) geïnstalleerd.

## <a name="create-clusters"></a>Clusters maken

Zie [Clusters op basis van Linux maken in HDInsight met Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Lijstclusters

Gebruik de volgende opdracht om alle clusters in het huidige abonnement weer te geven:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Cluster weergeven

Gebruik de volgende opdracht om details van een specifiek cluster in het huidige abonnement weer te geven:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik de volgende opdracht om een cluster te verwijderen:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

U ook een cluster verwijderen door de brongroep te verwijderen die het cluster bevat. Als u een resourcegroep verwijdert, worden alle bronnen in de groep verwijderd, inclusief het standaardopslagaccount.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Clusters schalen

Met de functie clusterschaling u het aantal werkknooppunten wijzigen dat wordt gebruikt door een cluster dat wordt uitgevoerd in Azure HDInsight zonder dat u het cluster opnieuw hoeft te maken. Als u de hadoop-clustergrootte wilt wijzigen met Azure PowerShell, voert u de volgende opdracht uit vanaf een clientmachine:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Zie [Scale HDInsight clusters voor](./hdinsight-scaling-best-practices.md)meer informatie over het schalen van clusters.

## <a name="update-http-user-credentials"></a>HTTP-gebruikersreferenties bijwerken

[Set-AzHDInsightGatewayCredential](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightgatewaycredential) stelt de GATEWAY HTTP-referenties in van een Azure HDInsight-cluster.

```powershell
$clusterName = "CLUSTERNAME"
$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Set-AzHDInsightGatewayCredential -ClusterName $clusterName -HttpCredential $credential
```

## <a name="find-the-default-storage-account"></a>Het standaardopslagaccount zoeken

In het volgende PowerShell-script wordt uitgelegd hoe u de naam van het standaardopslagaccount en de bijbehorende informatie krijgen:

```powershell
#Connect-AzAccount
$clusterName = "<HDInsight Cluster Name>"

$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$storageInfo = $clusterInfo.DefaultStorageAccount.split('.')
$defaultStoreageType = $storageInfo[1]
$defaultStorageName = $storageInfo[0]

echo "Default Storage account name: $defaultStorageName"
echo "Default Storage account type: $defaultStoreageType"

if ($defaultStoreageType -eq "blob")
{
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey

    echo "Default Blob container name: $defaultBlobContainerName"
    echo "Default Storage account key: $defaultStorageAccountKey"
}
```

## <a name="find-the-resource-group"></a>De resourcegroep zoeken

In de modus Resourcebeheer behoort elk HDInsight-cluster tot een Azure-brongroep.  Ga als u de resourcegroep zoeken:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Vacatures indienen

**MapReduce-taken verzenden**

Zie [De kaart uitvoerenBekijk voorbeelden in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive-taken indienen**

Zie [Apache Hive-query's uitvoeren met PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).

**Apache Sqoop-taken indienen**

Zie [Apache Sqoop gebruiken met HDInsight](hadoop/hdinsight-use-sqoop.md).

**Apache Oozie-taken indienen**

Zie [Apache Oozie gebruiken met Apache Hadoop om een werkstroom in HDInsight te definiëren en uit te voeren.](hdinsight-use-oozie-linux-mac.md)

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob-opslag

Zie [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Zie ook

* [Az.HDInsight cmdlets](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Apache Hadoop-clusters beheren in HDInsight met behulp van de Azure-portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met behulp van een command-line interface](hdinsight-administer-use-command-line.md)
* [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop-taken programmatisch indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
