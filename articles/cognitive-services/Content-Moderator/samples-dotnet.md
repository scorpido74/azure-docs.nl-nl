---
title: Codevoorbeelden - Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de Content Moderator van Azure Cognitive Services in uw .NET-toepassingen via de SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: a746a4baf172bcb6728c06f2a31b460139377e46
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912919"
---
# <a name="content-moderator-net-sdk-samples"></a>Content Moderator .NET SDK-voorbeelden

De volgende lijst bevat koppelingen naar de codevoorbeelden die zijn gecompileerd met behulp van de Azure Content Moderator SDK voor .NET.

## <a name="moderation"></a>Beheer

- **Beheer van afbeeldingen** : [Een afbeelding controleren op erotische en ongepaste inhoud, tekst en gezichten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Raadpleeg de [.NET SDK quickstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Aangepaste afbeeldingen** : [Toezicht houden met aangepaste afbeeldingslijsten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Raadpleeg de [.NET SDK quickstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Er is een maximumlimiet van **5 afbeeldingslijsten** waarbij elke lijst **niet meer dan 10.000 afbeeldingen mag bevatten** .
>

- **Tekstbeheer** : [Schermtekst voor scheldwoorden en persoonlijke gegevens](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Raadpleeg de [.NET SDK quickstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Aangepaste terminologie** : [Toezicht houden met aangepaste terminologielijsten](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Raadpleeg de [.NET SDK quickstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten** .
>

- **Beheer van video's** : [Een video controleren op erotische en ongepaste inhoud en resultaten ontvangen](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Zie [snelstart](video-moderation-api.md).

## <a name="review"></a>Beoordelen

- **Taken voor afbeeldingen** : [Een beheertaak starten om beoordelingen te scannen en maken](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Zie [snelstart](moderation-jobs-quickstart-dotnet.md).
- **Beoordelingen voor afbeeldingen** : [Beoordelingen maken voor HITL (human-in-the-loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Zie [snelstart](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Beoordelingen voor video's** : [Beoordelingen maken voor HITL (human-in-the-loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Zie [Snelstart](video-reviews-quickstart-dotnet.md)
- **Beoordelingen van videotranscripties** : [Beoordelingen van videotranscripties maken voor HITL (human-in-the-loop)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Zie [snelstart](video-reviews-quickstart-dotnet.md)

Zie alle .NET-voorbeelden bij de [Voorbeelden van Content Moderator .NET op GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).