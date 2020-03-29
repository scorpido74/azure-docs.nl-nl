---
title: Een tekstanalysebron voor Cognitive Services maken
titleSuffix: Azure Cognitive Services
description: Meer informatie over het maken van een bron voor text analytics voor Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: d3b65174f3d161e0b8780a890f297d9d1c812b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383468"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Een tekstanalysebron voor Cognitive Services maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Een resource maken**en ga naar **AI + Machine Learning** > **Text Analytics**.
   Of ga naar [Tekstanalyse maken.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Name|Voer een naam in (2-64 tekens).|
    |Abonnement|Selecteer het juiste abonnement.|
    |Locatie|Selecteer een locatie in de buurt.|
    |Prijscategorie| Voer **S**in , de standaardprijslaag.|
    |Resourcegroep|Selecteer een beschikbare resourcegroep.|

1. Selecteer **Maken**en wacht tot de resource is gemaakt. Uw browser wordt automatisch omgeleid naar de nieuw gemaakte resourcepagina.
1. Verzamel de `endpoint` geconfigureerde en een API-sleutel:

    |Tabblad Resource in portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eindpunt. Het lijkt `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0`op .|
    |**Sleutels**|API-sleutel|Kopieer een van de twee sleutels. Het is een alfanumerieke tekenreeks van 32 tekens zonder `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx` spaties of streepjes: <>.|
