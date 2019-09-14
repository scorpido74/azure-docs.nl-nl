---
title: Bing Speech herkennings modus kiezen | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: De beste herkennings modus kiezen in Bing Speech.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1860acb571c837a5eb4c75be69a96d1b22682118
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965675"
---
# <a name="bing-speech-recognition-modes"></a>Herkennings modi Bing Speech

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

De Bing Speech to Text-Api's ondersteunen meerdere modi van spraak herkenning. Kies de modus die de beste herkennings resultaten voor uw toepassing produceert.

| Modus | Description |
|---|---|
| *interactief* | Herkenning van de opdracht en controle voor interactieve gebruikers toepassings scenario's. Gebruikers spreken korte zinsneden die zijn bedoeld als opdrachten voor een toepassing. |
| *dicteren* | Voortdurende herkenning voor dicteer scenario's. Gebruikers spreken meer zinnen die worden weer gegeven als tekst. Gebruikers voeren een formeler spreek stijl in. |
| *enger* | Voortdurende herkenning voor het verzetten van conversaties tussen mensen. Gebruikers voeren een minder formele spreek stijl in en kunnen wisselen tussen langere zinnen en kortere woord groepen.

> [!NOTE]
> Deze modi zijn van toepassing wanneer u de [rest-api's](../GetStarted/GetStartedREST.md)gebruikt. De- [client bibliotheken](../GetStarted/GetStartedClientLibraries.md) gebruiken verschillende para meters om de herkennings modus op te geven. Zie de client bibliotheek van uw keuze voor meer informatie.

Zie de pagina [herkennings modi](../concepts.md#recognition-modes) voor meer informatie.
