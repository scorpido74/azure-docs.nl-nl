---
title: Apache Hadoop clusters beheren met Power shell-Azure HDInsight
description: Meer informatie over het uitvoeren van beheer taken voor de Apache Hadoop clusters in HDInsight met behulp van Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e37571b0078b4966aab9f505ddf88c2edb353197
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435630"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop clusters in HDInsight beheren door gebruik te maken van Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan worden gebruikt om de implementatie en het beheer van uw workloads in azure te beheren en te automatiseren. In dit artikel vindt u informatie over het beheren van [Apache Hadoop](https://hadoop.apache.org/) clusters in azure HDInsight met behulp van de Azure PowerShell AZ-module. Zie de [verwijzing AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight)voor de lijst met de Hdinsight Power shell-cmdlets.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De Power shell [AZ-module](https://docs.microsoft.com/powershell/azure/overview) is geïnstalleerd.

## <a name="create-clusters"></a>Clusters maken

Zie [op Linux gebaseerde clusters maken in HDInsight met behulp van Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

## <a name="list-clusters"></a>Clusters weer geven

Gebruik de volgende opdracht om alle clusters in het huidige abonnement weer te geven:

```powershell
Get-AzHDInsightCluster
```

## <a name="show-cluster"></a>Cluster weer geven

Gebruik de volgende opdracht om details weer te geven van een specifiek cluster in het huidige abonnement:

```powershell
Get-AzHDInsightCluster -ClusterName <Cluster Name>
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik de volgende opdracht om een cluster te verwijderen:

```powershell
Remove-AzHDInsightCluster -ClusterName <Cluster Name>
```

U kunt ook een cluster verwijderen door de resource groep te verwijderen die het cluster bevat. Als u een resource groep verwijdert, worden alle resources in de groep verwijderd, inclusief het standaard opslag account.

```powershell
Remove-AzResourceGroup -Name <Resource Group Name>
```

## <a name="scale-clusters"></a>Clusters schalen

Met de functie voor het schalen van clusters kunt u het aantal worker-knoop punten wijzigen dat wordt gebruikt door een cluster dat wordt uitgevoerd in azure HDInsight zonder dat u het cluster opnieuw hoeft te maken. Als u de grootte van de Hadoop-cluster wilt wijzigen met behulp van Azure PowerShell, voert u de volgende opdracht uit vanaf een client computer:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```

 Zie [HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md)voor meer informatie over het schalen van clusters.

## <a name="grantrevoke-access"></a>Toegang verlenen/intrekken

HDInsight-clusters hebben de volgende HTTP-webservices (al deze services hebben REST-eind punten):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Deze services worden standaard verleend voor toegang. U kunt de toegang intrekken/verlenen. Intrekken:

```powershell
Revoke-AzHDInsightHttpServicesAccess -ClusterName <Cluster Name>
```

Verlenen:

```powershell
$clusterName = "<HDInsight Cluster Name>"

# Credential option 1
$hadoopUserName = "admin"
$hadoopUserPassword = '<Enter the Password>'
$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

# Credential option 2
#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"

Grant-AzHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential
```

> [!NOTE]  
> Door de toegang te verlenen/in te trekken, stelt u de gebruikers naam en het wacht woord van het cluster opnieuw in.

Het verlenen en intrekken van toegang kan ook worden gedaan via de portal. Zie [Apache Hadoop clusters in HDInsight beheren met behulp van de Azure Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP-gebruikers referenties bijwerken

Dit is dezelfde procedure als HTTP-toegang verlenen/intrekken. Als de HTTP-toegang aan het cluster is verleend, moet u dit eerst intrekken.  En verleen vervolgens de toegang met nieuwe HTTP-gebruikers referenties.

## <a name="find-the-default-storage-account"></a>Het standaard opslag account zoeken

Het volgende Power shell-script laat zien hoe u de standaard naam van het opslag account en de bijbehorende gegevens kunt ophalen:

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

## <a name="find-the-resource-group"></a>De resource groep zoeken

In de Resource Manager-modus maakt elk HDInsight-cluster deel uit van een Azure-resource groep.  De resource groep zoeken:

```powershell
$clusterName = "<HDInsight Cluster Name>"

$cluster = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroupName = $cluster.ResourceGroup
```

## <a name="submit-jobs"></a>Taken verzenden

**MapReduce-taken verzenden**

Zie [de MapReduce-voor beelden uit HDInsight uitvoeren](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive-taken verzenden**

Zie [Apache Hive Query's uitvoeren met behulp van Power shell](hadoop/apache-hadoop-use-hive-powershell.md).

**Apache Sqoop-taken verzenden**

Zie [Apache Sqoop gebruiken met HDInsight](hadoop/hdinsight-use-sqoop.md).

**Apache Oozie-taken verzenden**

Zie [Apache Oozie met Apache Hadoop gebruiken om een werk stroom in HDInsight te definiëren en uit te voeren](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob Storage

Zie [gegevens uploaden naar HDInsight](hdinsight-upload-data.md).

## <a name="see-also"></a>Zie ook

* [AZ. HDInsight-cmdlets](https://docs.microsoft.com/powershell/module/az.hdinsight/?view=azps-3.1.0#hdinsight)
* [Apache Hadoop clusters in HDInsight beheren door gebruik te maken van de Azure Portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met een opdracht regel interface](hdinsight-administer-use-command-line.md)
* [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop-taken via een programma indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
