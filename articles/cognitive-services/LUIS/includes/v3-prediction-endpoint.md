---
title: V3-voorspellingseindpunt ophalen
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128158"
---
1. Kopieer de **Voorbeeldquery** aan de onderkant van de pagina in het LUIS-portaal, in de sectie **Beheren** (menu rechtsboven) op de pagina **Azure-resources** (linkermenu) op het tabblad **Voorspellingsresources**. De URL bevat uw app-ID, -sleutel en sitenaam. De URL voor het V3-voorspellingseindpunt heeft de volgende vorm: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="voorbeeldquery in de sectie Voorspellingsresources" lightbox="../media/prediction-resources-example-query.png":::
    
    Plak de URL in een nieuw browsertabblad. Als u de URL niet ziet, hebt u geen voorspellingsresource en moet u er een maken. 

    

    

