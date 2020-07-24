---
title: Azure CLI-vereisten voor Azure HPC-cache
description: Setup-stappen voordat u Azure CLI kunt gebruiken om een Azure HPC-cache te maken of te wijzigen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 7d0844e699c012d987c23a75e2b0874005cf535a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87097387"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Azure CLI instellen voor Azure HPC-cache

Volg deze stappen om uw omgeving voor te bereiden voordat u Azure CLI gebruikt voor het maken of beheren van een HPC-cache van Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-azure-cli"></a>Azure CLI installeren

Voor de Azure HPC-cache is versie 2,7 of hoger van de Azure-CLI vereist. Voer `az --version` uit om de versie en afhankelijke bibliotheken te vinden die zijn geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u CLI wilt installeren of upgraden.

```azurecli-interactive
az --version
```

### <a name="install-the-azure-hpc-cache-extension"></a>De Azure HPC-cache-extensie installeren

De functies van de Azure HPC-cache zijn geen onderdeel van de hoofd code basis. Daarom moet u ook de extensie verwijzing installeren. Volg de onderstaande instructies.

1. Aanmelden

   Meld u aan met de opdracht [AZ login](/cli/azure/reference-index#az-login) als u een lokaal geïnstalleerde versie van de CLI gebruikt.

    ```azurecli
    az login
    ```

    Volg de weergegeven stappen in uw terminal om het verificatieproces te voltooien.

   > [!TIP]
   > Als u meerdere abonnementen hebt, moet u er een kiezen. Selecteer deze optie wanneer u een Cloud Shell-sessie in azure Portal start of volg de instructies in aan de [slag met Azure cli](/cli/azure/get-started-with-azure-cli#sign-in) om uw abonnement in te stellen via de opdracht regel.

2. De Azure CLI-extensie installeren

   De functies van de Azure HPC-cache zijn opgenomen als een Azure CLI-extensie, maar maakt geen deel uit van het kern CLI-pakket. U moet de uitbreidings verwijzing installeren voordat u deze kunt gebruiken.

   Met Azure CLI-extensies krijgt u toegang tot de opdrachten voor experimentele en voorlopige versies. Zie [extensies gebruiken met Azure cli](/cli/azure/azure-cli-extensions-overview)voor meer informatie over uitbrei dingen, zoals bijwerken en verwijderen.

   Installeer de extensie voor Azure HPC-cache door de volgende opdracht uit te voeren:

    ```azurecli-interactive
    az extension add --name hpc-cache
   ```

## <a name="set-default-resource-group-optional"></a>Standaard resource groep instellen (optioneel)

Voor de meeste HPC-cache opdrachten moet u de resource groep van de cache door geven. U kunt de standaard resource groep instellen met behulp van [AZ configure](/cli/azure/reference-index#az-configure).

## <a name="next-steps"></a>Volgende stappen

Nadat u de Azure CLI-extensie hebt geïnstalleerd en u zich hebt aangemeld, kunt u Azure CLI gebruiken voor het maken en beheren van Azure HPC-cache systemen.

* [Een HPC-cache van Azure maken](hpc-cache-create.md)
* [Documentatie voor Azure CLI HPC-cache](/cli/azure/ext/hpc-cache/hpc-cache)
