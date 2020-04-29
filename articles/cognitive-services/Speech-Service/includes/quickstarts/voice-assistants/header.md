---
title: 'Snelstartgids: een aangepaste spraak assistent maken-spraak service'
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
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80241141"
---
In deze Quick Start gebruikt u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor het maken van een aangepaste spraak assistent-toepassing die verbinding maakt met een bot die u al hebt gemaakt en geconfigureerd. Als u een bot wilt maken, raadpleegt u [de gerelateerde zelf studie](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md) voor een uitgebreidere hand leiding.

Wanneer u aan een paar vereisten hebt voldaan, worden er slechts enkele stappen uitgevoerd om de aangepaste spraak assistent te koppelen:
> [!div class="checklist"]
> * Maak een `BotFrameworkConfig` object op basis van uw abonnements sleutel en-regio.
> * Maak een `DialogServiceConnector` object met behulp van het `BotFrameworkConfig` bovenstaande object.
> * Gebruik het `DialogServiceConnector` -object om het Luister proces voor één utterance te starten.
> * Inspecteer `ActivityReceivedEventArgs` de geretourneerde.
