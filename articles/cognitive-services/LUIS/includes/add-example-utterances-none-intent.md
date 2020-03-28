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
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77279649"
---
De clienttoepassing moet weten of een uiting niet zinvol of gepast is voor de toepassing. De intentie **Geen** wordt aan elke toepassing toegevoegd als onderdeel van het aanmaakproces om te bepalen of een utterance niet door de clienttoepassing moet worden beantwoord.

Als LUIS de intentie **Geen** retourneert voor een uiting, kan de clienttoepassing vragen of de gebruiker het gesprek wil beëindigen of meer aanwijzingen voor het vervolgen van het gesprek wil geven.

Als u de intentie **Geen** leeg laat, wordt een utterance voorspeld die buiten het onderwerpdomein moet worden voorspeld in een van de bestaande intenties van het onderwerpdomein. Het resultaat is dat de clienttoepassing, zoals een chatbot, onjuiste bewerkingen uitvoert op basis van een onjuiste voorspelling.

1. Selecteer **Intents** in het linkerpaneel.

1. Selecteer de intent **None**. Voeg drie uitingen toe die uw gebruiker kan invoeren, maar die niet relevant zijn voor uw pizzabestel-app:

    |`None`voorbeelduitingen|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    In deze voorbeelden mogen geen woorden worden gebruikt `pizza`die `cheese` `crust`u `pickup` `deliver`in uw onderwerpdomein verwacht, zoals , , .