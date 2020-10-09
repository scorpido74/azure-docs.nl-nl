---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2647c604bebc2885f5c8516eb997792b65751e88
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80876071"
---
## <a name="create-an-computer-vision-resource"></a>Een Computer Vision resource maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Klik [op **Computer Vision** resource maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) .
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0` -de minimale prijs categorie|
    |Resource Group|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina resource.
1. Verzamelen geconfigureerd `{ENDPOINT_URI}` en `{API_KEY}` , Zie [vereiste para meters](../computer-vision-how-to-install-containers.md#gathering-required-parameters) voor details verzamelen.
