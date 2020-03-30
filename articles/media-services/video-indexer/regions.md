---
title: Regio's waarin Video Indexer beschikbaar is - Azure
titleSuffix: Azure Media Services
description: In dit artikel wordt gesproken over Azure-regio's waarin Azure Media Services Video Indexer beschikbaar is.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: c91b38fcbfb9b517651adead010408425e519a82
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382746"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regio's waarin Video-indexer bestaat

Video Indexer API's bevatten een **locatieparameter** die u moet instellen op het Azure-gebied waarnaar de aanroep moet worden doorgestuurd. Dit moet een [Azure-gebied zijn waarin Video Indexer beschikbaar is.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

## <a name="locations"></a>Locaties

De **locatieparameter** moet de codenaam azure-regio als waarde krijgen. Als u Video Indexer in de voorbeeldmodus gebruikt, moet u *'proefversie'* als waarde plaatsen. Als u anders de codenaam wilt krijgen van het Azure-gebied waarin uw account zich bevindt en naar uw oproep moet worden doorgestuurd, u de volgende regel uitvoeren in [Azure CLI:](/cli/azure)

```azurecli-interactive
az account list-locations
```

Zodra u de bovenstaande regel uitvoert, krijgt u een lijst met alle Azure-regio's. Navigeer naar het Azure-gebied met de *weergaveNaam die* u zoekt en gebruik de *naamwaarde* voor de **locatieparameter.**

Voor de Azure-regio West US 2 (hieronder weergegeven) gebruikt u bijvoorbeeld 'westus2' voor de **locatieparameter.**

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Volgende stappen

- [Taalmodel aanpassen met API's](customize-language-model-with-api.md)
- [Merkenmodel aanpassen met API's](customize-brands-model-with-api.md)
- [Persoonsmodel aanpassen met API's](customize-person-model-with-api.md)
