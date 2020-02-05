---
title: 'Snelstartgids: Apache Spark clusters met Azure CLI-Azure HDInsight'
description: In deze Quick start ziet u hoe u Azure CLI gebruikt om een Apache Spark cluster in azure HDInsight te maken.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: ac937519ca3fa9381fe4970f2e4336fe4a0a9227
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989124"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Snelstartgids: Apache Spark cluster maken in azure HDInsight met behulp van Azure CLI

In deze Quick Start leert u hoe u een Apache Spark cluster maakt in azure HDInsight met behulp van Azure CLI. Apache Spark maakt het mogelijk om snelle gegevensanalyses en clusterberekeningen uit te voeren met behulp van verwerking in het geheugen. De [Azure-opdracht regel interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) is de platformoverschrijdende opdracht regel ervaring van micro soft voor het beheer van Azure-resources.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Azure CLI. Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [Azure cli installeren voor de](https://docs.microsoft.com/cli/azure/install-azure-cli) stappen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Een Apache Spark-cluster maken

1. Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van het code blok. Anders voert u de onderstaande opdracht in:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Omgevings variabelen instellen. Het gebruik van variabelen in deze Quick start is gebaseerd op bash. Er zijn kleine variaties nodig voor andere omgevingen. Vervang RESOURCEGROUPNAME, LOCATION, CLUSTERNAME, STORAGEACCOUNTNAME en PASSWORD in het onderstaande code fragment door de gewenste waarden. Voer vervolgens de CLI-opdrachten in om de omgevings variabelen in te stellen.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Maak de resource groep door de volgende opdracht in te voeren:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Maak een Azure Storage-account door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extraheer de primaire sleutel uit het Azure-opslag account en sla deze op in een variabele door de onderstaande opdracht in te voeren:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Maak een Azure storage-container door de volgende opdracht in te voeren:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Maak het Apache Spark-cluster door de volgende opdracht in te voeren:

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

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig kunt verwijderen wanneer deze niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs wanneer het niet in gebruik is. Omdat de kosten voor het cluster veel keren meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet worden gebruikt.

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

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een Apache Spark cluster maakt in azure HDInsight met behulp van Azure CLI.  Ga naar de volgende zelfstudie voor informatie over het gebruik van een HDInsight Spark-cluster om interactieve query's uit te voeren op voorbeeldgegevens.

> [!div class="nextstepaction"]
> [Interactieve query's uitvoeren in Apache Spark](./apache-spark-load-data-run-query.md)
