---
title: V3-voorspellingseindpunt ophalen
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: a607ad71915606d6046b4c71291a49b8641bdcc9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544981"
---
1. Kopieer de **Voorbeeldquery** aan de onderkant van de pagina in het LUIS-portaal, in de sectie **Beheren** (menu rechtsboven) op de pagina **Azure-resources** (linkermenu) op het tabblad **Voorspellingsresources**.

    Plak de URL in een nieuw browsertabblad.

    De URL bevat uw app-ID, -sleutel en sitenaam. De URL voor het V3-voorspellingseindpunt ziet er als volgt uit:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

