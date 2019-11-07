---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/05/2019
ms.author: diberry
ms.openlocfilehash: bbe40083159d433c0b5746834e1c530b23b03851
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648298"
---
De clienttoepassing moet weten of een uiting niet zinvol of gepast is voor de toepassing. De intentie **Geen** wordt als onderdeel van het maakproces aan elke toepassing toegevoegd om te bepalen of een uiting niet kan worden beantwoord door de clienttoepassing.

Als LUIS de intentie **Geen** retourneert voor een uiting, kan de clienttoepassing vragen of de gebruiker het gesprek wil beëindigen of meer aanwijzingen voor het vervolgen van het gesprek wil geven. 

Als **u niets opgeeft** , wordt een utterance die buiten het onderwerp van het domein moet worden voor speld, in een van de bestaande onderwerp-domein intentie voor speld. Het resultaat is dat de client toepassing, zoals een chat-bot, onjuiste bewerkingen uitvoert op basis van een onjuiste voor spelling. 

1. Selecteer **Intents** in het linkerpaneel.

1. Selecteer de intent **None**. Voeg drie utterances toe die een gebruiker misschien kan invoeren, maar die niet relevant zijn voor uw app:

    |`None` voorbeeld uitingen|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|
