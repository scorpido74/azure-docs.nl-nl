---
title: Apache Hadoop clusters maken met behulp van Azure CLI-Azure HDInsight
description: Meer informatie over het maken van Azure HDInsight-clusters met behulp van de platformoverschrijdende Azure CLI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: c26c0b16331ae01f7505e44cef3fe91b3282750b
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70809859"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-clusters maken met behulp van Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Met de stappen in dit document wordt uitgelegd hoe u een HDInsight 3,6-cluster maakt met behulp van de Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Azure CLI. Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [Azure cli installeren voor de](https://docs.microsoft.com/cli/azure/install-azure-cli) stappen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Een cluster maken

1. Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van het code blok. Anders voert u de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Omgevings variabelen instellen. Het gebruik van variabelen in dit artikel is gebaseerd op bash. Er zijn kleine variaties nodig voor andere omgevingen. Zie [AZ-hdinsight-Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) voor een volledige lijst met mogelijke para meters voor het maken van een cluster.

    |Parameter | Description |
    |---|---|
    |`--size`| Het aantal worker-knoop punten in het cluster. In dit artikel wordt de `clusterSizeInNodes` variabele gebruikt als de waarde `--size`die wordt door gegeven aan. |
    |`--version`| De versie van het HDInsight-cluster. In dit artikel wordt de `clusterVersion` variabele gebruikt als de waarde `--version`die wordt door gegeven aan. Zie ook: [Ondersteunde HDInsight-versies](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Type HDInsight-cluster, zoals Hadoop, interactivehive, hbase, Kafka, Storm, Spark, Rserver, mlservices.  In dit artikel wordt de `clusterType` variabele gebruikt als de waarde `--type`die wordt door gegeven aan. Zie ook: [Cluster typen en-configuratie](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|De versies van verschillende Hadoop-onderdelen, in versies die zijn gescheiden in de indeling ' onderdeel = versie '. In dit artikel wordt de `componentVersion` variabele gebruikt als de waarde `--component-version`die wordt door gegeven aan. Zie ook: [Hadoop-onderdelen](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Vervang `RESOURCEGROUPNAME` ,`LOCATION` ,,`PASSWORD` en door de gewenste waarden. `CLUSTERNAME` `STORAGEACCOUNTNAME` Wijzig de waarden voor de andere variabelen naar wens. Voer vervolgens de CLI-opdrachten in.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Maak de resource groep](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) door de volgende opdracht in te voeren:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Gebruik de `az account list-locations` opdracht voor een lijst met geldige locaties en gebruik vervolgens een van de locaties van de `name` waarde.

4. [Maak een Azure Storage-account](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extraheer de primaire sleutel uit het Azure-opslag account](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) en sla deze op in een variabele door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Maak een Azure storage-container](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) door de volgende opdracht in te voeren:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Maak het HDInsight-cluster](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) door de volgende opdracht in te voeren:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight-clusters worden geleverd in verschillende typen, die overeenkomen met de werk belasting of technologie waarvoor het cluster is afgestemd. Er is geen ondersteunde methode voor het maken van een cluster dat meerdere typen combineert, zoals Storm en HBase op één cluster.

    Het kan enkele minuten duren voordat het proces voor het maken van het cluster is voltooid. Meestal ongeveer 15.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Voer alle of enkele van de volgende opdrachten in om resources te verwijderen:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

Nu u een HDInsight-cluster hebt gemaakt met behulp van de Azure CLI, gebruikt u de volgende informatie om te leren werken met uw cluster:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Java-topologieën ontwikkelen voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-onderdelen gebruiken in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en bewaken met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
