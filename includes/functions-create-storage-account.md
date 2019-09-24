---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203155"
---
## <a name="create-an-azure-storage-account"></a>Een Azure Storage-account maken

Functions gebruikt een account voor algemeen gebruik in Azure Storage om de status en andere gegevens van uw functies te onderhouden. Maak een algemeen opslagaccount in de resourcegroep die u hebt gemaakt, met behulp van de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create).

Vervang in de volgende opdracht de tijdelijke aanduiding `<storage_name>` door een wereldwijd unieke opslagaccountnaam. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
