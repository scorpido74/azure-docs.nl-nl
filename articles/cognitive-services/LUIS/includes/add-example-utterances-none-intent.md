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
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279649"
---
De clienttoepassing moet weten of een uiting niet zinvol of gepast is voor de toepassing. De **enige** intentie wordt toegevoegd aan elke toepassing als onderdeel van het aanmaak proces om te bepalen of een utterance niet moet worden beantwoord door de client toepassing.

Als LUIS de intentie **Geen** retourneert voor een uiting, kan de clienttoepassing vragen of de gebruiker het gesprek wil beëindigen of meer aanwijzingen voor het vervolgen van het gesprek wil geven.

Als **u niets opgeeft** , wordt een utterance die buiten het onderwerp van het domein moet worden voor speld, in een van de bestaande onderwerp-domein intentie voor speld. Het resultaat is dat de client toepassing, zoals een chat-bot, onjuiste bewerkingen uitvoert op basis van een onjuiste voor spelling.

1. Selecteer **Intents** in het linkerpaneel.

1. Selecteer de intent **None**. Voeg drie uitingen toe die uw gebruiker kan invoeren, maar die niet relevant zijn voor uw pizza-ordenings-app:

    |`None` voorbeeld uitingen|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    Deze voor beelden mogen geen woorden gebruiken die u verwacht in uw onderwerps domein, zoals `pizza`, `cheese``crust``pickup` `deliver`.