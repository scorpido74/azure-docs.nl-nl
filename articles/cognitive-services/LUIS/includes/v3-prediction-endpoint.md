---
title: V3-Voorspellings eindpunt ophalen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/05/2020
ms.author: diberry
ms.openlocfilehash: 18f5422202cf972d49349cc04b845c623cabffa3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589121"
---
1. Klik in de LUIS-Portal in het gedeelte **beheren** (rechtsboven) op de pagina Azure- **resources** (linkermenu) op het tabblad **Voorspellings bronnen** en kopieer de **voorbeeld query** onder aan de pagina.

    Plak de URL in een nieuw browser tabblad.

    De URL heeft uw app-ID, sleutel en sleuf naam. De URL voor de V3-Voorspellings eindpunt ziet er als volgt uit:

    `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

