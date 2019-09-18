---
title: Apache Hadoop clusters in HDInsight beheren met Power shell-Azure
description: Meer informatie over het uitvoeren van beheer taken voor de Apache Hadoop clusters in HDInsight met behulp van Azure PowerShell.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: tyfox
ms.openlocfilehash: 751f064df271aeb0899a00aea8b1ff09e8b8bdf4
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077050"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Apache Hadoop clusters in HDInsight beheren door gebruik te maken van Azure PowerShell
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell kan worden gebruikt om de implementatie en het beheer van uw workloads in azure te beheren en te automatiseren. In dit artikel vindt u informatie over het beheren van [Apache Hadoop](https://hadoop.apache.org/) clusters in azure HDInsight met behulp van de Azure PowerShell AZ-module. Zie de [verwijzing AZ. HDInsight](https://docs.microsoft.com/powershell/module/az.hdinsight)voor de lijst met de Hdinsight Power shell-cmdlets.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* De Power shell [AZ-module](https://docs.microsoft.com/powershell/azure/overview) is geïnstalleerd.

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
Met de functie voor het schalen van clusters kunt u het aantal worker-knoop punten wijzigen dat wordt gebruikt door een cluster dat wordt uitgevoerd in azure HDInsight zonder dat u het cluster opnieuw hoeft te maken.

De gevolgen van het wijzigen van het aantal gegevens knooppunten voor elk type cluster dat wordt ondersteund door HDInsight:

* Apache Hadoop

    U kunt het aantal worker-knoop punten in een Hadoop-cluster dat wordt uitgevoerd, probleemloos verhogen zonder dat dit van invloed is op wachtende of actieve taken. Nieuwe taken kunnen ook worden verzonden terwijl de bewerking wordt uitgevoerd. Fouten in een schaal bewerking worden op de juiste wijze afgehandeld, zodat het cluster altijd in een functionele status blijft.

    Wanneer een Hadoop-cluster omlaag wordt geschaald door het aantal gegevens knooppunten te verminderen, worden enkele van de services in het cluster opnieuw gestart. Als Services opnieuw worden gestart, mislukken alle actieve en in behandeling zijnde taken bij het volt ooien van de schaal bewerking. U kunt de taken echter opnieuw verzenden nadat de bewerking is voltooid.
* Apache HBase

    U kunt tijdens het uitvoeren naadloos knoop punten toevoegen aan of verwijderen uit uw HBase-cluster. Regionale servers worden automatisch gebalanceerd binnen enkele minuten van het volt ooien van de schaal bewerking. U kunt de regionale servers echter ook hand matig balanceren door u aan te melden bij de hoofd knooppunt van het cluster en vervolgens de volgende opdrachten uit te voeren vanuit een opdracht prompt venster:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

* Apache Storm

    U kunt eenvoudig gegevens knooppunten toevoegen aan of verwijderen uit uw Storm-cluster tijdens de uitvoering. Maar nadat de schaal bewerking is voltooid, moet u de topologie opnieuw verdelen.

    Herverdeling kan op twee manieren worden uitgevoerd:

  * Storm-webinterface
  * Opdracht regel interface (CLI)-hulp programma

    Raadpleeg de [documentatie van Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) voor meer informatie.

    De gebruikers interface van Storm is beschikbaar op het HDInsight-cluster:

    ![Herverdeling van HDInsight Storm-schaal](./media/hdinsight-administer-use-powershell/portal-scale-cluster.png)

    Hier volgt een voor beeld van het gebruik van de CLI-opdracht voor het opnieuw verdelen van de Storm-topologie:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Als u de grootte van de Hadoop-cluster wilt wijzigen met behulp van Azure PowerShell, voert u de volgende opdracht uit vanaf een client computer:

```powershell
Set-AzHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
```


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
$hadoopUserPassword = "<Enter the Password>"
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
Het is dezelfde procedure als HTTP-toegang verlenen/intrekken. Als de HTTP-toegang aan het cluster is verleend, moet u dit eerst intrekken.  En verleen vervolgens de toegang met nieuwe HTTP-gebruikers referenties.

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

* [Referentie documentatie voor HDInsight-cmdlets](https://msdn.microsoft.com/library/azure/dn479228.aspx)
* [Apache Hadoop clusters in HDInsight beheren door gebruik te maken van de Azure Portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met een opdracht regel interface](hdinsight-administer-use-command-line.md)
* [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Hadoop-taken via een programma indienen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
