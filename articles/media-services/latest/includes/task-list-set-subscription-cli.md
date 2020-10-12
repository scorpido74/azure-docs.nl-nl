---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88556292"
---
<!-- List and set subscriptions -->

1. U kunt een lijst met uw abonnementen opvragen met de opdracht [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Gebruik `az account set` met de abonnements-ID of -naam waarnaar u wilt overschakelen.

    ```
    az account set --subscription "My Demos"
    ```
