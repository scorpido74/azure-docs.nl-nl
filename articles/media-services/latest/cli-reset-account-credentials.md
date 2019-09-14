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
ms.openlocfilehash: 09c93e2d851bea22e9d54dde35398f36335eb896
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70967578"
---
# <a name="cli-example-reset-the-account-credentials"></a>CLI-voorbeeld: De referenties voor het account opnieuw instellen

In het Azure CLI-script in dit artikel ziet u hoe u de referenties van uw account opnieuw kunt instellen en de app.config-instellingen kunt terugkrijgen.

## <a name="prerequisites"></a>Vereisten 

[Een Azure Media Services-account maken](create-account-cli-how-to.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>Voorbeeldscript

```
# Update the following variables for your own settings:
resourceGroup=amsResourceGroup
amsAccountName=amsmediaaccountname

az ams account sp reset-credentials \
  --account-name $amsAccountName \
  --resource-group $resourceGroup 
 ```

## <a name="next-steps"></a>Volgende stappen

* [AZ AMS](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [Referenties opnieuw instellen](https://docs.microsoft.com/cli/azure/ams/account/sp?view=azure-cli-latest#az-ams-account-sp-reset-credentials)
