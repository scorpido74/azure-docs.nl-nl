---
title: 'Snelstartgids: Speech SDK voor Java script (browser) platform Setup-Speech Service'
titleSuffix: Azure Cognitive Services
description: Gebruik deze hand leiding om uw platform in te stellen voor het gebruik van Java script (browser) met de Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: fa8bf79c047911ca283bf60261bba0cbdd6816a7
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980124"
---
In deze hand leiding wordt uitgelegd hoe u de [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) voor Java script installeert voor gebruik met een webpagina.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Een nieuwe websitemap maken

Maak een nieuwe, lege map. Als u de voorbeeldtoepassing op een webserver wilt hosten, moet u ervoor zorgen dat de webserver toegang heeft tot de map.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>De Speech SDK voor JavaScript in die map uitpakken

Download de Speech SDK als een [ZIP-pakket](https://aka.ms/csspeech/jsbrowserpackage) en pak dit uit in de zojuist gemaakte map. Dit resulteert in vier uitgepakte bestanden:
* `microsoft.cognitiveservices.speech.sdk.bundle.js`Een door menselijke Lees bare versie van de Speech-SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map`Een kaart bestand dat wordt gebruikt voor fout opsporing SDK-code.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts`Object definities voor gebruik met type script
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js`Een minified-versie van de Speech-SDK.

## <a name="create-an-indexhtml-page"></a>Een index.html-pagina maken

Maak een nieuw bestand in de map met de naam `index.html` en open dit bestand met een teksteditor.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [windows](../quickstart-list.md)]