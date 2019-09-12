---
title: Een HDInsight-cluster verwijderen-Azure
description: Informatie over de verschillende manieren waarop u een Azure HDInsight-cluster kunt verwijderen
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 08bfdcab0b7fbb8e533cb8d2d6a74d315ad4074c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885227"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure CLI

De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. De facturering wordt pro-rato per minuut berekend, dus u moet altijd uw cluster verwijderen wanneer het niet meer wordt gebruikt. In dit document vindt u informatie over het verwijderen van een cluster met behulp van de [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ-module](https://docs.microsoft.com/powershell/azure/overview)en de [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Als u een HDInsight-cluster verwijdert, worden de Azure Storage accounts of Data Lake Storage die aan het cluster zijn gekoppeld, niet verwijderd. U kunt in de toekomst gegevens die in deze services zijn opgeslagen, opnieuw gebruiken.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Ga in het menu links naar **alle services** > **Analytics** > **HDInsight-clusters** en selecteer uw cluster.

3. Selecteer in de standaard weergave het pictogram **verwijderen** . Volg de prompt om uw cluster te verwijderen.
   
    ![pictogram verwijderen](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell-az-module"></a>Azure PowerShell AZ-module

Vervang `CLUSTERNAME` door de naam van uw HDInsight-cluster in de onderstaande code. Voer de volgende opdracht uit vanaf een Power shell-prompt om het cluster te verwijderen:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure-CLI

Vervang `CLUSTERNAME` door de naam van uw HDInsight-cluster en `RESOURCEGROUP` met de naam van uw resource groep in de onderstaande code.  Voer vanaf een opdracht prompt het volgende in om het cluster te verwijderen:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
