---
title: Azure HDInsight-clusters beheren met Azure CLI
description: Meer informatie over het gebruik van de Azure CLI om Azure HDInsight-clusters te beheren. Clustertypen zijn Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query en ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272771"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure HDInsight-clusters beheren met Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Meer informatie over het gebruik van [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) voor het beheren van Azure HDInsight-clusters. De Azure-opdrachtregelinterface (CLI) is de platformoverschrijdende opdrachtregelervaring voor het beheren van Azure-resources.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI. Zie [De Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli) voor stappen als u de Azure CLI niet hebt geïnstalleerd.

* Een Apache Hadoop cluster op HDInsight. Zie [Aan de slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **Probeer deze** in de rechterbovenhoek van het codeblok. Voer anders de onderstaande opdracht in:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Lijstclusters

Gebruik [de lijst van AZ hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) om clusters op te sommen. Bewerk de onderstaande opdrachten `RESOURCE_GROUP_NAME` door te vervangen door de naam van uw resourcegroep en voer vervolgens de opdrachten in:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Cluster weergeven

Gebruik [de AZ HDInsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) om informatie weer te geven voor een bepaald cluster. Bewerk de opdracht hieronder `RESOURCE_GROUP_NAME`door `CLUSTER_NAME` de relevante informatie te vervangen en met de relevante informatie, voer vervolgens de opdracht in:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik [az hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) om een bepaald cluster te verwijderen. Bewerk de opdracht hieronder `RESOURCE_GROUP_NAME`door `CLUSTER_NAME` de relevante informatie te vervangen en met de relevante informatie, voer vervolgens de opdracht in:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

U ook een cluster verwijderen door de brongroep die het cluster bevat te verwijderen. Let op, hiermee worden alle bronnen in de groep verwijderd, inclusief het standaardopslagaccount.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Clusters schalen

Gebruik [het formaat van AZ hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) om het formaat van het opgegeven HDInsight-cluster te wijzigen in de opgegeven grootte. Bewerk de opdracht hieronder `RESOURCE_GROUP_NAME`door `CLUSTER_NAME` de relevante informatie te vervangen en te vervangen. Vervang `WORKERNODE_COUNT` door het gewenste aantal werkknooppunten voor uw cluster. Zie [Scale HDInsight clusters voor](./hdinsight-scaling-best-practices.md)meer informatie over het schalen van clusters. Voer de opdracht in:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u verschillende HDInsight-clusterbeheertaken uitvoeren. Zie voor meer informatie de volgende artikelen:

* [Apache Hadoop-clusters beheren in HDInsight met behulp van de Azure-portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Aan de slag met de Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
