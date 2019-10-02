---
title: Volwassene, ongepaste, benchmarks-inhoud-Computer Vision
titleSuffix: Azure Cognitive Services
description: Concepten met betrekking tot het detecteren van inhoud voor volwassenen in afbeeldingen met behulp van de Computer Vision-APi.
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
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718519"
---
# <a name="detect-adult-content"></a>Inhoud voor volwassenen detecteren

Computer Vision kunnen materiaal voor volwassenen in afbeeldingen detecteren zodat ontwikkel aars de weer gave van deze installatie kopieën in hun software kunnen beperken. Inhouds vlaggen worden toegepast met een score tussen nul en één zodat ontwikkel aars de resultaten kunnen interpreteren op basis van hun eigen voor keuren.

> [!NOTE]
> Veel van deze functionaliteit wordt aangeboden door de [Azure content moderator](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) -service. Bekijk dit alternatief voor oplossingen voor meer strenge toezicht scenario's voor inhoud, zoals tekst toezicht en Human Review-werk stromen.

## <a name="content-flag-definitions"></a>Definities van inhouds markeringen

In de categorie ' volwassene ' zijn er verschillende categorieën:

- **Volwassene** installatie kopieën worden gedefinieerd als die van een expliciete seksuele aard zijn en vaak worden weer gegeven als naaktheid en seksuele daden.
- **Ongepaste** -installatie kopieën worden gedefinieerd als installatie kopieën die expliciet worden voorgesteld en bevatten vaak minder expliciete inhoud dan afbeeldingen die als **volwassene**worden gelabeld.
- **Benchmarks** -installatie kopieën worden gedefinieerd als die van Gore.

## <a name="use-the-api"></a>De API gebruiken

U kunt inhoud voor volwassenen detecteren met de API voor het [analyseren van afbeeldingen](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Wanneer u de waarde van `Adult` toevoegt aan de query parameter **visualFeatures** , retourneert de API drie Booleaanse eigenschappen @ no__t-2 @ no__t-3, `isRacyContent` en `isGoryContent` @ no__t-6IN het JSON-antwoord. De-methode retourneert ook overeenkomende eigenschappen @ no__t-0 @ no__t-1, `racyScore` en `goreScore` @ no__t-4which geven betrouwbaarheids scores weer tussen nul en één voor elke betreffende categorie.

- [Snelstart: Een installatie kopie (.NET SDK) analyseren ](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Snelstart: Een afbeelding analyseren (REST API)](./quickstarts/csharp-analyze.md)
