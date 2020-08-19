---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602570"
---
<!-- Create a resource group -->

Gebruik de volgende opdracht om een resource groep te maken. Selecteer de geografische regio die wordt gebruikt om de media-en meta gegevens records voor uw Media Services-account op te slaan. Deze regio wordt gebruikt om uw media te verwerken en te streamen.

```azurecli
az group create --name amsResourceGroup --location westus2
```
