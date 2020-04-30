---
title: V3-Voorspellings eindpunt ophalen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 23e7f88b23ac7af7470183877eeb1be89e79779c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80287744"
---
1. Klik in de LUIS-Portal in het gedeelte **beheren** (rechtsboven) op de pagina **Azure-resources** (linkermenu) op het tabblad **Voorspellings bronnen** en kopieer de **voorbeeld query** onder aan de pagina.

    Plak de URL in een nieuw browser tabblad.

    De URL heeft uw app-ID, sleutel en sleuf naam. De URL voor de V3-Voorspellings eindpunt ziet er als volgt uit:

    `https://REGION.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

