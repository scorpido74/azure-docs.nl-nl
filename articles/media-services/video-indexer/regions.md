---
title: Regio's waar Video Indexer beschikbaar is-Azure
titleSuffix: Azure Media Services
description: Dit artikel spreekt over Azure-regio's waarin Video Indexer beschikbaar is.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: b67c89e02091d57b4f2dc901005c2dae36deafc2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838203"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regio's waarin Video Indexer bestaat

Video Indexer-Api's bevatten een **locatie** parameter die u moet instellen op de Azure-regio waarin de aanroep moet worden gerouteerd. Dit moet een Azure-regio zijn waarin [video indexer beschikbaar is](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Locaties

De **locatie** parameter moet de naam van de Azure-regio code krijgen als waarde. Als u Video Indexer in de preview-modus gebruikt, moet u *' proef versie '* als waarde invoeren. Als u de code naam wilt ophalen van de Azure-regio waarin uw account zich bevindt en dat uw oproep moet worden doorgestuurd, kunt u de volgende regel uitvoeren in [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest):

```bash
az account list-locations
```

Wanneer u de hierboven weer gegeven regel hebt uitgevoerd, krijgt u een lijst met alle Azure-regio's. Ga naar de Azure-regio met de *DisplayName* die u zoekt en gebruik de *naam* waarde voor de **locatie** parameter.

Voor de Azure-regio vs-West 2 (hieronder weer gegeven) gebruikt u bijvoorbeeld ' westus2 ' voor de **locatie** parameter.

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

- [Taal model aanpassen met behulp van Api's](customize-language-model-with-api.md)
- [Het merk model aanpassen met behulp van Api's](customize-brands-model-with-api.md)
- [Persoonlijk model aanpassen met behulp van Api's](customize-person-model-with-api.md)
