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
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: e4147fbb27c8538f801f6c49f8b535a283faf50f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325912"
---
De clienttoepassing moet weten of een uiting niet zinvol of gepast is voor de toepassing. De **enige** intentie wordt toegevoegd aan elke toepassing als onderdeel van het aanmaak proces om te bepalen of een utterance niet moet worden beantwoord door de client toepassing.

Als LUIS de intentie **Geen** retourneert voor een uiting, kan de clienttoepassing vragen of de gebruiker het gesprek wil beëindigen of meer aanwijzingen voor het vervolgen van het gesprek wil geven. 

Als **u niets opgeeft** , wordt een utterance die buiten het onderwerp van het domein moet worden voor speld, in een van de bestaande onderwerp-domein intentie voor speld. Het resultaat is dat de client toepassing, zoals een chat-bot, onjuiste bewerkingen uitvoert op basis van een onjuiste voor spelling. 

1. Selecteer **Intents** in het linkerpaneel.

1. Selecteer de intent **None**. Voeg drie uitingen toe die uw gebruiker kan invoeren, maar die niet relevant zijn voor uw pizza-ordenings-app:

    |`None` voorbeeld uitingen|
    |--|
    |`Barking dogs are annoying`|
    |`Order a pizza for me`|
    |`Penguins in the ocean`|

    Deze voor beelden mogen geen woorden gebruiken die u verwacht in uw onderwerps domein, zoals `pizza`, `cheese``crust``pickup` `deliver`.