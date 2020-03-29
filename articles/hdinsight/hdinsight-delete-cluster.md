---
title: Een HDInsight-cluster verwijderen - Azure
description: Informatie over de verschillende manieren waarop u een Azure HDInsight-cluster verwijderen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807133"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Een HDInsight-cluster verwijderen met uw browser, PowerShell of azure cli

De facturering voor het gebruik van HDInsight-clusters begint zodra er een cluster is gemaakt en stopt als een cluster wordt verwijderd. Facturering wordt naar rato per minuut beoordeeld, dus u moet uw cluster altijd verwijderen wanneer het niet meer in gebruik is. In dit document leert u hoe u een cluster verwijdert met de [Azure-portal,](https://portal.azure.com)azure [PowerShell Az-module](https://docs.microsoft.com/powershell/azure/overview)en de [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Als u een HDInsight-cluster verwijdert, worden de Azure Storage-accounts of Gegevensmeeropslag die aan het cluster zijn gekoppeld, niet verwijderd. U gegevens die in deze services zijn opgeslagen in de toekomst opnieuw gebruiken.

## <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Navigeer in het linkermenu naar alle**Analytics** > **HDInsight-clusters** van **alle services** > en selecteer uw cluster.

3. Selecteer in de standaardweergave het pictogram **Verwijderen.** Volg de prompt om uw cluster te verwijderen.

    ![Knop CLUSTER verwijderen HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Vervang `CLUSTERNAME` de naam van uw HDInsight-cluster in de onderstaande code. Voer vanuit een PowerShell-prompt de volgende opdracht in om het cluster te verwijderen:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure-CLI

Vervang `CLUSTERNAME` de naam van uw HDInsight-cluster en `RESOURCEGROUP` de naam van uw resourcegroep in de onderstaande code.  Voer in een opdrachtprompt het volgende in om het cluster te verwijderen:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
