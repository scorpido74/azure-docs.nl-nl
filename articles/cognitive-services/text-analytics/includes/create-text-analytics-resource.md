---
title: Een Cognitive Services Text Analytics-resource maken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een Cognitive Services Text Analytics resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779594"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Een Cognitive Services Text Analytics-resource maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Een resource maken** en ga vervolgens naar **AI en Machine Learning** > **Text Analytics**.
   Of ga naar [Text Analytics maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Voer een naam in (2-64 tekens).|
    |Abonnement|Selecteer het juiste abonnement.|
    |Locatie|Selecteer een locatie in de buurt.|
    |Prijscategorie| Voer **S** in, de standaardprijscategorie.|
    |Resourcegroep|Selecteer een beschikbare resourcegroep.|

1. Selecteer **Maken** en wacht tot de resource is gemaakt. Uw browser wordt automatisch omgeleid naar de zojuist gemaakte resourcepagina.
1. De geconfigureerde `endpoint` en een API-sleutel verzamelen:

    |Het tabblad resource in de portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eind punt. Deze lijkt op `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` .|
    |**Sleutels**|API-sleutel|Kopieer een van de twee sleutels. Het is een alfanumerieke teken reeks van 32 tekens zonder spaties of streepjes: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
