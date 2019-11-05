---
title: LUIS-resource maken
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465918"
---
## <a name="create-a-luis-resource"></a>Een LUIS-resource maken

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)
1. Klik [op **Language Understanding** maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Gewenste naam (2-64 tekens)|
    |Abonnement|Selecteer het juiste abonnement|
    |Locatie|Selecteer een locatie in de buurt en beschik bare locaties|
    |Prijscategorie|`F0`-de minimale prijs categorie|
    |Resourcegroep|Een beschik bare resource groep selecteren|

1. Klik op **maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, gaat u naar de pagina resource.
1. De geconfigureerde `endpoint` en een API-sleutel verzamelen, Zie [vereiste para meters verzamelen](#gathering-required-parameters).

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
