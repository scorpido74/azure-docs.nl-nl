---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "77279649"
---
De clienttoepassing moet weten of een uiting niet zinvol of gepast is voor de toepassing. De intentie **Geen** wordt als onderdeel van het maakproces aan elke toepassing toegevoegd om te bepalen of een uiting niet moet worden beantwoord door de clienttoepassing.

Als LUIS de intentie **Geen** retourneert voor een uiting, kan de clienttoepassing vragen of de gebruiker het gesprek wil beëindigen of meer aanwijzingen voor het vervolgen van het gesprek wil geven.

Als de intentie **Geen** leeg wordt gelaten, wordt een uiting die buiten het domein van het onderwerp moet worden voorspeld, in een van de bestaande intenties van het bestaande onderwerpdomein voorspeld. Het resultaat is dat de clienttoepassing, zoals een chatbot, onjuiste bewerkingen uitvoert op basis van een onjuiste voorspelling.

1. Selecteer **Intents** in het linkerpaneel.

1. Selecteer de intent **None**. Voeg drie uitingen toe die een gebruiker kan invoeren, maar die niet relevant zijn voor de app waarmee u pizza's kunt bestellen:

    |`None` voorbeelduitingen|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    In deze voorbeelden mogen geen woorden voorkomen die u in uw onderwerpdomein verwacht, zoals `pizza`, `cheese`, `crust`, `pickup` of `deliver`.