---
title: Volwassen, racy, bloederige inhoud - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van inhoud voor volwassenen in afbeeldingen met behulp van de Computer Vision APi.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718519"
---
# <a name="detect-adult-content"></a>Inhoud voor volwassenen detecteren

Computer Vision kan volwassen materiaal in afbeeldingen detecteren, zodat ontwikkelaars de weergave van deze afbeeldingen in hun software kunnen beperken. Inhoudsvlaggen worden toegepast met een score tussen nul en één, zodat ontwikkelaars de resultaten kunnen interpreteren op basis van hun eigen voorkeuren.

> [!NOTE]
> Een groot deel van deze functionaliteit wordt aangeboden door de [Azure Content Moderator-service.](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) Zie dit alternatief voor oplossingen voor strengere scenario's voor het beheren van inhoud, zoals tekstmoderatie en workflows voor menselijke beoordeling.

## <a name="content-flag-definitions"></a>Definities van inhoudsvlag

Binnen de classificatie "volwassen" zijn verschillende categorieën:

- **Volwassen** beelden worden gedefinieerd als beelden die expliciet seksueel van aard zijn en vaak naaktheid en seksuele handelingen weergeven.
- **Pikante** afbeeldingen worden gedefinieerd als afbeeldingen die seksueel suggestief van aard zijn en vaak minder seksueel expliciete inhoud bevatten dan afbeeldingen die als **adult zijn**getagd.
- **Bloederige** beelden worden gedefinieerd als die die gore afbeelden.

## <a name="use-the-api"></a>De API gebruiken

U inhoud voor volwassenen detecteren met de [API Afbeelding analyseren.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Wanneer u de `Adult` waarde toevoegt aan de parameter **visualFeatures-query,** retourneert de API drie&mdash;`isAdultContent`booleaanse eigenschappen `isRacyContent`en `isGoryContent` &mdash;in de JSON-respons. De methode retourneert ook overeenkomstige eigenschappen&mdash;`adultScore` `racyScore`, en `goreScore` &mdash;die betrouwbaarheidsscores tussen nul en één voor elke respectievelijke categorie vertegenwoordigen.

- [Snelstart: een afbeelding analyseren (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Snelstart: een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
