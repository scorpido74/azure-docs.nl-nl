---
title: 'Snelstart: een aangepaste spraakassistent maken - Spraakservice'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80241141"
---
In deze quickstart gebruikt u de [Spraak-SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) om een aangepaste spraakassistenttoepassing te maken die verbinding maakt met een bot die u al hebt geschreven en geconfigureerd. Als u een bot wilt maken, raadpleegt u [de gerelateerde zelfstudie](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) voor een uitgebreidere handleiding.

Nadat u aan een aantal vereisten hebt voldaan, neemt het aansluiten van uw aangepaste spraakassistent slechts een paar stappen:
> [!div class="checklist"]
> * Maak `BotFrameworkConfig` een object op basis van uw abonnementssleutel en regio.
> * Maak `DialogServiceConnector` een object `BotFrameworkConfig` met het object van bovenaf.
> * Start `DialogServiceConnector` het luisterproces voor één utterance met het object.
> * Inspecteer de `ActivityReceivedEventArgs` teruggekeerden.
