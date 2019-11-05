---
title: Ondersteuning voor containers
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499113"
---
## <a name="create-an-computer-vision-resource"></a>Een Computer Vision resource maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Klik [op **Computer Vision** resource maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) .
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0`-de minimale prijs categorie|
    |Resourcegroep|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina resource.
1. De geconfigureerde `{ENDPOINT_URI}` en `{API_KEY}`verzamelen, Zie [vereiste para meters verzamelen](../computer-vision-how-to-install-containers.md#gathering-required-parameters) voor meer informatie.
