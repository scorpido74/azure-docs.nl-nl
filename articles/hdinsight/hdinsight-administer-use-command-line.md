---
title: Azure HDInsight-clusters beheren met Azure CLI
description: Meer informatie over het gebruik van de Azure CLI voor het beheren van Azure HDInsight-clusters. Cluster typen zijn onder andere Apache Hadoop, Spark, HBase, Storm, Kafka, interactieve Query's en ML-Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84699359"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Azure HDInsight-clusters beheren met Azure CLI

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Meer informatie over het gebruik van [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) voor het beheren van Azure HDInsight-clusters. De Azure-opdrachtregelinterface (CLI) is de platformoverschrijdende opdrachtregelervaring voor het beheren van Azure-resources.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Azure CLI. Als u de Azure CLI nog niet hebt geïnstalleerd, raadpleegt u [Azure cli installeren voor de](https://docs.microsoft.com/cli/azure/install-azure-cli) stappen.

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Meld u aan bij uw Azure-abonnement. Als u Azure Cloud Shell wilt gebruiken, selecteert u **deze** in de rechter bovenhoek van het code blok. Anders voert u de onderstaande opdracht in:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Clusters weer geven

Gebruik [AZ hdinsight List](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) om clusters weer te geven. Bewerk de onderstaande opdrachten door `RESOURCE_GROUP_NAME` de naam van de resource groep te vervangen en voer de volgende opdrachten in:

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

## <a name="show-cluster"></a>Cluster weer geven

Gebruik [AZ hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) om informatie weer te geven voor een opgegeven cluster. Bewerk de onderstaande opdracht door te vervangen `RESOURCE_GROUP_NAME` , en `CLUSTER_NAME` Voer vervolgens de volgende opdracht uit:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik [AZ hdinsight delete](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) om een opgegeven cluster te verwijderen. Bewerk de onderstaande opdracht door te vervangen `RESOURCE_GROUP_NAME` , en `CLUSTER_NAME` Voer vervolgens de volgende opdracht uit:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

U kunt ook een cluster verwijderen door de resource groep te verwijderen die het cluster bevat. Opmerking: Hiermee verwijdert u alle resources in de groep, met inbegrip van het standaard opslag account.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Clusters schalen

Gebruik [AZ hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) om het formaat van het opgegeven hdinsight-cluster te wijzigen in de opgegeven grootte. Bewerk de onderstaande opdracht door te vervangen `RESOURCE_GROUP_NAME` en door `CLUSTER_NAME` de relevante informatie. Vervang door `WORKERNODE_COUNT` het gewenste aantal worker-knoop punten voor uw cluster. Zie [HDInsight-clusters schalen](./hdinsight-scaling-best-practices.md)voor meer informatie over het schalen van clusters. Voer de opdracht in:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u verschillende beheer taken voor HDInsight-clusters kunt uitvoeren. Raadpleeg de volgende artikelen voor meer informatie:

* [Apache Hadoop clusters in HDInsight beheren door gebruik te maken van de Azure Portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met behulp van Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Aan de slag met Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Aan de slag met de Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
