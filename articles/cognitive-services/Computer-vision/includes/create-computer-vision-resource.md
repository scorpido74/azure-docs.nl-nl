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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73499113"
---
## <a name="create-an-computer-vision-resource"></a>Een Computer Vision-bron maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Klik [op **Computervisie maken.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Name|Gewenste naam (2-64 tekens)|
    |Abonnement|Passend abonnement selecteren|
    |Locatie|Selecteer een nabijgelegen en beschikbare locatie|
    |Prijscategorie|`F0`- de minimale prijscategorie|
    |Resourcegroep|Een beschikbare resourcegroep selecteren|

1. Klik **op Maken** en wacht tot de resource is gemaakt. Nadat deze is gemaakt, navigeert u naar de resourcepagina.
1. Verzamel geconfigureerd `{ENDPOINT_URI}` `{API_KEY}`en zie [het verzamelen van vereiste parameters](../computer-vision-how-to-install-containers.md#gathering-required-parameters) voor meer informatie.
