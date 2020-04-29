---
title: Een Cognitive Services Text Analytics resource maken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Cognitive Services Text Analytics resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 6cd653909e26dc5e0484ca289a1d2ab47e20457f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876404"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Een Cognitive Services Text Analytics resource maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer **een resource maken**en ga vervolgens naar **AI + machine learning** > **Text Analytics**.
   Of ga naar [Text Analytics maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Voer een naam in (2-64 tekens).|
    |Abonnement|Selecteer het juiste abonnement.|
    |Locatie|Selecteer een locatie in de buurt.|
    |Prijscategorie| Voer **de**prijs categorie Standard in.|
    |Resourcegroep|Selecteer een beschik bare resource groep.|

1. Selecteer **maken**en wacht tot de resource is gemaakt. Uw browser wordt automatisch omgeleid naar de zojuist gemaakte resource pagina.
1. De geconfigureerde `endpoint` en een API-sleutel verzamelen:

    |Het tabblad resource in de portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eind punt. Deze lijkt op `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`.|
    |**Sleutels**|API-sleutel|Kopieer een van de twee sleutels. Het is een alfanumerieke teken reeks van 32 tekens zonder spaties of streepjes: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
