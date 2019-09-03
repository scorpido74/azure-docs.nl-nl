---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: a2994602f857c2c8ff9f935b649a8d3e94c10dca
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2019
ms.locfileid: "68444122"
---
## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [az group create](/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources, zoals functie-apps, databases en opslagaccounts worden geïmplementeerd en beheerd.

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt.  
Als u Cloud Shell niet gebruikt, moet u zich eerst aanmelden met `az login`.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

In het algemeen maakt u de resource groep en de resources in een [regio](https://azure.microsoft.com/global-infrastructure/regions/) bij u in de buurt. 
