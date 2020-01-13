---
title: Een Azure Data Explorer-cluster en-data base maken met behulp van Power shell
description: Meer informatie over het maken van een Azure Data Explorer-cluster en-data base met behulp van Power shell
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: b855fde88173fe9a14a964ba1f9fd07aa74d85eb
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911984"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-powershell"></a>Een Azure Data Explorer-cluster en-data base maken met behulp van Power shell

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sjabloon](create-cluster-database-resource-manager.md)  

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. In dit artikel maakt u een cluster en een Data Base met behulp van Power shell. U kunt Power shell-cmdlets en-scripts uitvoeren op Windows, Linux of in [Azure Cloud shell](../cloud-shell/overview.md) met [AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.4.0#kusto) om Azure Data Explorer-clusters en-data bases te maken en te configureren.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de Azure CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0.4 of hoger. Voer `az --version` uit om uw versie te controleren. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-parameters"></a>Para meters configureren

De volgende stappen zijn niet vereist als u opdrachten in Azure Cloud Shell uitvoert. Als u de CLI lokaal uitvoert, volgt u stap 1 & 2 om u aan te melden bij Azure en uw huidige abonnement in te stellen:

1. Voer de volgende opdracht uit om u aan te melden bij Azure:

    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Stel het abonnement in waarvoor u het cluster wilt maken:

    ```azurepowershell-interactive
     Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ```
1. Wanneer u Azure CLI lokaal uitvoert of in de Azure Cloud Shell, moet u de module AZ. Kusto op uw apparaat installeren:
    
    ```azurepowershell-interactive
     Install-Module -Name Az.Kusto  
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Het Azure Data Explorer-cluster maken

1. Maak uw cluster met behulp van de volgende opdracht:

    ```azurepowershell-interactive
     New-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster -Location 'Central US' -Sku D13_v2 -Capacity 10
    ```

   |**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
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

   |**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
   |---|---|---|
   | ClusterName | *mykustocluster* | De naam van het cluster waar de database wordt gemaakt.|
   | Name | *mykustodatabase* | De naam van uw database.|
   | ResourceGroupName | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |
   | SoftDeletePeriod | *3650:00:00:00* | Hoe lang gegevens worden opgeslagen om query's erop te kunnen uitvoeren. |
   | HotCachePeriod | *3650:00:00:00* | Hoe lang gegevens worden opgeslagen in de cache. |

1. Voer de volgende opdracht uit om de database te bekijken die u hebt gemaakt:

    ```azurepowershell-interactive
    Get-AzKustoDatabase -ClusterName mykustocluster -ResourceGroupName testrg -Name mykustodatabase
    ```

U hebt nu een cluster en een database.

## <a name="clean-up-resources"></a>Resources opschonen

* Als u van plan bent om onze andere artikelen te volgen, moet u de resources die u hebt gemaakt, blijven gebruiken.
* Als u resources wilt opschonen, moet u het cluster verwijderen. Wanneer u een cluster verwijdert, worden alle databases hierin ook verwijderd. Gebruik de volgende opdracht om uw cluster te verwijderen:

    ```azurepowershell-interactive
    Remove-AzKustoCluster -ResourceGroupName testrg -Name mykustocluster
    ```

## <a name="next-steps"></a>Volgende stappen

* [Aanvullende opdracht AZ. Kusto](/powershell/module/az.kusto/?view=azps-1.7.0#kusto)
* [Gegevens opnemen met behulp van de Azure Data Explorer .NET Standard SDK (preview)](net-standard-ingest-data.md)
