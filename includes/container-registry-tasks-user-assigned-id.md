---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77112306"
---
### <a name="create-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit maken

Maak een identiteit met de naam *myACRTasksId* in uw abonnement met behulp van de opdracht [AZ-identiteit maken.][az-identity-create] U dezelfde resourcegroep gebruiken die u eerder hebt gebruikt om een containerregister of een ander register te maken.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Als u de door de gebruiker toegewezen identiteit wilt configureren in de volgende stappen, gebruikt u de opdracht [AZ-identiteitweergeven][az-identity-show] om de bron-id, de hoofd-id en de client-id van de identiteit in variabelen op te slaan.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show