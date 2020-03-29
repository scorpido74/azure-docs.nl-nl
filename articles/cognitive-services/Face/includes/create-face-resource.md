---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 3886777b283af35e84683480a59097584b537fea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522735"
---
## <a name="create-an-face-resource"></a>Een Face-bron maken

1. Aanmelden bij de [Azure-portal](https://portal.azure.com)
1. Klik [op **Face-bron** maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Name|Gewenste naam (2-64 tekens)|
    |Abonnement|Passend abonnement selecteren|
    |Locatie|Selecteer een nabijgelegen en beschikbare locatie|
    |Prijscategorie|`F0`- de minimale prijscategorie|
    |Resourcegroep|Een beschikbare resourcegroep selecteren|

1. Klik **op Maken** en wacht tot de resource is gemaakt. Nadat het is gemaakt, navigeert u naar de resourcepagina
1. Configured `endpoint` en een API-sleutel verzamelen:

    |Tabblad Resource in Portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eindpunt. Het lijkt op`https://face.cognitiveservices.azure.com/face/v1.0`|
    |**Sleutels**|API-sleutel|Kopieer 1 van de twee toetsen. Het is een tekenreeks met 32 alfanumerieke tekens `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`zonder spaties of streepjes.|
