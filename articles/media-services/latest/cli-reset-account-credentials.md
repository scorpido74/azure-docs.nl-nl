---
title: Azure CLI-voorbeeldscript - De referenties van uw account opnieuw instellen | Microsoft Docs
description: Gebruik het Azure CLI-script om de referenties van uw account opnieuw in te stellen en de app.config-instellingen terug te krijgen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/20/2019
ms.author: juliako
ms.openlocfilehash: 0a1a5bf9f192a7128da8843b79b60be0175cec23
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092130"
---
# <a name="azure-cli-example-reset-the-account-credentials"></a>Voorbeeld van Azure CLI: De referenties voor het account opnieuw instellen

In het Azure CLI-script in dit artikel ziet u hoe u de referenties van uw account opnieuw kunt instellen en de app.config-instellingen kunt terugkrijgen.

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Voorbeeldscript

```azurecli-interactive
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup
 ```

## <a name="next-steps"></a>Volgende stappen

* [az ams](/cli/azure/ams)
* [Referenties opnieuw instellen](/cli/azure/ams/account/sp#az-ams-account-sp-reset-credentials)
