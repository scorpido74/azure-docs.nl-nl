---
title: Een Azure Data Explorer-cluster maken & DB met Powershell
description: Meer informatie over het maken van een Azure Data Explorer-cluster en -database met PowerShell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 690c3e281e65f54f240c70f7a6e5038f54102c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560589"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Een Azure Data Explorer-cluster en -database maken met PowerShell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sjabloon](create-cluster-database-resource-manager.md)  

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. In dit artikel maakt u een cluster en een database met Powershell. U PowerShell-cmdlets en -scripts uitvoeren op Windows, Linux of in [Azure Cloud Shell](../cloud-shell/overview.md) met [Az.Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) om Azure Data Explorer-clusters en databases te maken en te configureren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de Azure CLI lokaal te installeren en te gebruiken, vereist dit artikel de Azure CLI-versie 2.0.4 of hoger. Voer `az --version` uit om uw versie te controleren. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Parameters configureren

De volgende stappen zijn niet vereist als u opdrachten in Azure Cloud Shell uitvoert. Als u de CLI lokaal uitvoert, voert u stappen 1 & 2 uit om u aan te melden bij Azure en uw huidige abonnement in te stellen:

1. Voer de volgende opdracht uit om u aan te melden bij Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Stel het abonnement in waar u wilt dat uw cluster wordt gemaakt:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Wanneer u Azure CLI lokaal of in de Azure Cloud Shell uitvoert, moet u de Az.Kusto-module op uw apparaat installeren:

    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Het Azure Data Explorer-cluster maken

1. Maak uw cluster met behulp van de volgende opdracht:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
   |---|---|---|
   | Name | *mykustocluster* | De gewenste naam van uw cluster.|
   | Sku | *D13_v2* | De SKU die wordt gebruikt voor uw cluster. |
   | ResourceGroupName | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |

    Er zijn aanvullende optionele parameters die u kunt gebruiken, zoals de capaciteit van het cluster.

1. Voer de volgende opdracht uit om te controleren of het cluster is gemaakt:

    ```azurepowershell-interactive
    Get-AzKustoCluster -Name mykustocluster -ResourceGroupName testrg
    ```

Als het resultaat `provisioningState` met waarde `Succeeded` bevat, is het maken van het cluster geslaagd.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>De database in het Azure Data Explorer-cluster maken

1. Maak uw database met behulp van de volgende opdracht:

    ```azurepowershell-interactive
    New-AzKustoDatabase -ResourceGroupName testrg -ClusterName mykustocluster -Name mykustodatabase -SoftDeletePeriod 3650:00:00:00 -HotCachePeriod 3650:00:00:00
    ```

   |**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
   |---|---|---|
   | Clusternaam | *mykustocluster* | De naam van het cluster waar de database wordt gemaakt.|
   | Name | *mykustodatabase* | De naam van uw database.|
   | ResourceGroupName | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |
   | SoftDeletePeriod SoftDeletePeriod SoftDeletePeriod SoftDelete | *3650:00:00:00* | Hoe lang gegevens worden opgeslagen om query's erop te kunnen uitvoeren. |
   | HotCachePeriode | *3650:00:00:00* | Hoe lang gegevens worden opgeslagen in de cache. |

1. Voer de volgende opdracht uit om de database te bekijken die u hebt gemaakt:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

U hebt nu een cluster en een database.

## <a name="clean-up-resources"></a>Resources opschonen

* Als u van plan bent onze andere artikelen te volgen, behoudt u de resources die u hebt gemaakt.
* Als u resources wilt opschonen, moet u het cluster verwijderen. Wanneer u een cluster verwijdert, worden alle databases hierin ook verwijderd. Gebruik de volgende opdracht om uw cluster te verwijderen:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Volgende stappen

* [Aanvullende opdrachten van Az.Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Gegevens opnemen met de Azure Data Explorer .NET Standard SDK (Preview)](net-standard-ingest-data.md)
