---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "68444137"
---
## <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Functions gebruikt een account voor algemeen gebruik in Azure Storage om de status en andere gegevens van uw functies te onderhouden. Maak een algemeen opslagaccount in de resourcegroep die u hebt gemaakt, met behulp van de opdracht [az storage account create](/cli/azure/storage/account).

Vervang in de volgende opdracht de tijdelijke aanduiding `<storage_name>` door een wereldwijd unieke opslagaccountnaam. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
