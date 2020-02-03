---
title: Gebruikers utterance bekijken-LUIS
titleSuffix: Azure Cognitive Services
description: Met actief leren, uw uitingen controle-eindpunt voor de juiste intentie en entiteit. LUIS kiest eindpunt uitingen is het niet zeker weet.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710535"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Concepten voor het inschakelen van actief leren aan de hand van de eindpunt-uitingen
Actief leren is een van drie strategieën voor het verbeteren van nauwkeurigheid en het gemakkelijkst te implementeren. Met actief leren, uw uitingen controle-eindpunt voor de juiste intentie en entiteit. LUIS kiest eindpunt uitingen is het niet zeker weet.

## <a name="what-is-active-learning"></a>Wat is actief leren
Actief leren is een proces in twee stappen. Eerst selecteert LUIS uitingen die wordt ontvangen op van de app-eindpunt waarvoor validatie. De tweede stap wordt uitgevoerd door de app-eigenaar of de samen werker om de geselecteerde uitingen te valideren voor [controle](luis-how-to-review-endpoint-utterances.md), met inbegrip van de juiste intentie en entiteiten binnen de intentie. Bekijk de uitingen en trainen en publiceer de app opnieuw.

## <a name="which-utterances-are-on-the-review-list"></a>Welke uitingen zijn in de lijst met controleren
Als de bovenkant activeren kunt u lezen wat een lage score heeft of de bovenste twee intenties scores te sluiten zijn, LUIS uitingen toegevoegd aan de beoordeling-lijst.

## <a name="single-pool-for-utterances-per-app"></a>Één groep voor uitingen per app
De lijst uitingen van het **controle punt** wordt niet gewijzigd op basis van de versie. Er is één groep uitingen om te beoordelen, ongeacht welke versie van de uiting u actief bewerkt of welke versie van de app wordt gepubliceerd op het eindpunt.

## <a name="where-are-the-utterances-from"></a>Waar zijn de uitingen van
Eindpunt-uitingen zijn afkomstig uit de query's door eindgebruikers op HTTP-eindpunt van de toepassing. Als uw app is niet gepubliceerd of nog niet treffers ontvangen is, hoeft u niet alle uitingen om te controleren. Als er geen eindpunt treffers worden ontvangen voor een bepaald doel of de entiteit, hoeft u niet uitingen om te controleren die ze bevatten.

## <a name="schedule-review-periodically"></a>Controleer regelmatig plannen
Controleren van de voorgestelde uitingen hoeft niet elke dag worden uitgevoerd, maar moet deel uitmaken van uw regelmatig onderhoud van LUIS.

## <a name="delete-review-items-programmatically"></a>Objecten bekijken via een programma verwijderen
Gebruik de niet- **[gelabelde uitingen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** -API verwijderen. Maak een back-up van deze uitingen voordat u deze verwijdert door **[de logboek bestanden te exporteren](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="enable-active-learning"></a>Actief leren inschakelen

Als u actief leren wilt inschakelen, moet u gebruikers query's registreren. Dit wordt bereikt door de query van het [eind punt](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) aan te roepen met de para meter en waarde van de `log=true` query string.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [controleren](luis-how-to-review-endpoint-utterances.md) van eind punten uitingen
