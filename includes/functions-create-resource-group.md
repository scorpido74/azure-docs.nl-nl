---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0fba49ec5a610d03ece2dce7fa5bb0d9d094ab4c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203143"
---
## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). Een Azure-resourcegroep is een logische container waarin Azure-resources, zoals functie-apps, databases en opslagaccounts worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt.  
Als u Cloud Shell niet gebruikt, meldt u zich `az login`eerst aan met.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

In het algemeen maakt u de resource groep en de resources in een [regio](https://azure.microsoft.com/global-infrastructure/regions/) bij u in de buurt. 
