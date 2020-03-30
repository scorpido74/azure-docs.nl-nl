---
title: Apache Hadoop-clusters maken met Azure CLI - Azure HDInsight
description: Meer informatie over het maken van Azure HDInsight-clusters met behulp van het Azure CLI met meerdere platforms.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199038"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>HDInsight-clusters maken met de Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

De stappen in deze document-walk-through het maken van een HDInsight 3.6-cluster met behulp van de Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Azure CLI. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor stappen als u de Azure CLI niet hebt geïnstalleerd.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Een cluster maken

1. Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **Probeer deze** in de rechterbovenhoek van het codeblok. Voer anders de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Omgevingsvariabelen instellen. Het gebruik van variabelen in dit artikel is gebaseerd op Bash. Kleine variaties zullen nodig zijn voor andere omgevingen. Zie [az-hdinsight-create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) voor een volledige lijst van mogelijke parameters voor clustercreatie.

    |Parameter | Beschrijving |
    |---|---|
    |`--workernode-count`| Het aantal werknemersknooppunten in het cluster. In dit artikel `clusterSizeInNodes` wordt de `--workernode-count`variabele gebruikt als de waarde die wordt doorgegeven aan . |
    |`--version`| De HDInsight-clusterversie. In dit artikel `clusterVersion` wordt de `--version`variabele gebruikt als de waarde die wordt doorgegeven aan . Zie ook: [Ondersteunde HDInsight-versies](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Type HDInsight cluster, zoals: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  In dit artikel `clusterType` wordt de `--type`variabele gebruikt als de waarde die wordt doorgegeven aan . Zie ook: [Clustertypen en configuratie](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|De versies van verschillende Hadoop componenten, in ruimte-gescheiden versies in 'component=versie' formaat. In dit artikel `componentVersion` wordt de `--component-version`variabele gebruikt als de waarde die wordt doorgegeven aan . Zie ook: [Hadoop componenten](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Vervang `RESOURCEGROUPNAME` `LOCATION`, `CLUSTERNAME` `STORAGEACCOUNTNAME`, `PASSWORD` , en met de gewenste waarden. Wijzig waarden voor de andere variabelen naar wens. Voer vervolgens de CLI-opdrachten in.

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

3. [Maak de resourcegroep](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) door onderstaande opdracht in te voeren:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Voor een lijst met geldige `az account list-locations` locaties gebruikt u de opdracht en `name` gebruikt u een van de locaties van de waarde.

4. [Maak een Azure Storage-account](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) door onderstaande opdracht in te voeren:

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

5. [Haal de primaire sleutel uit het Azure Storage-account](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) en sla deze op in een variabele door onderstaande opdracht in te voeren:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Maak een Azure Storage-container](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) door onderstaande opdracht in te voeren:

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
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight clusters zijn er in verschillende soorten, die overeenkomen met de werkbelasting of technologie waarvoor het cluster is afgestemd. Er is geen ondersteunde methode om een cluster te maken dat meerdere typen combineert, zoals Storm en HBase op één cluster.

    Het kan enkele minuten duren voordat het proces voor het maken van het cluster is voltooid. Meestal rond de 15.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u het artikel hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs als het niet in gebruik is. Aangezien de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

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

Nu u een HDInsight-cluster hebt gemaakt met azure cli, gebruikt u het volgende om te leren hoe u met uw cluster werken:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase clusters

* [Aan de slag met Apache HBase op HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-toepassingen ontwikkelen voor Apache HBase op HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Ontwikkel Java-topologieën voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-componenten gebruiken in Apache Storm op HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en monitoren met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
