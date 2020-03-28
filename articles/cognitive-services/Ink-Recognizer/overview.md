---
title: Wat is Ink Recognizer? - Ink Recognizer API
titleSuffix: Azure Cognitive Services
description: Integreer de Ink Recognizer in uw toepassingen, websites, hulpprogramma's en andere oplossingen, zodat gegevens over de pennenstreek kunnen worden geïdentificeerd en als invoer kunnen worden gebruikt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75448156"
---
# <a name="what-is-the-ink-recognizer-api"></a>Wat is de Ink Recognizer-API?


De Ink Recognizer Cognitive Service biedt een cloudgebaseerde REST API voor het analyseren en herkennen van digitale inktinhoud. In tegenstelling tot services die ocr (Optical Character Recognition) gebruiken, vereist de API digitale inktlijngegevens als invoer. Digitale pennenstreken zijn tijdsgeordende sets van 2D-punten (X,Y-coördinaten) die de beweging van invoertools zoals digitale pennen of vingers vertegenwoordigen. Vervolgens worden de vormen en handgeschreven inhoud uit de invoer herkend en wordt een JSON-antwoord geretourneerd met alle erkende entiteiten.

![Een stroomdiagram waarin een invoer van een pennenstreek naar de API wordt beschreven](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Functies

Met de Ink Recognizer API u eenvoudig handgeschreven inhoud in uw toepassingen herkennen. 

|Functie  |Beschrijving  |
|---------|---------|
| Handschriftherkenning | Herken handgeschreven inhoud in 63 [kerntalen en landplaatsen.](language-support.md) | 
| Indelingsherkenning | Krijg structurele informatie over de digitale inktinhoud. Breek de inhoud op in schrijfgebieden, alinea's, regels, woorden, lijsten met opsommingstekens. Uw toepassingen kunnen vervolgens de lay-outgegevens gebruiken om extra functies te bouwen, zoals automatische lijstopmaak en uitlijning van de vorm. |
| Vormherkenning | Herken de meest gebruikte [geometrische vormen](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) bij het maken van notities. |
| Gecombineerde vormen en tekstherkenning | Herken welke pennenstreken tot vormen of handgeschreven inhoud behoren en classificeer ze afzonderlijk.|

## <a name="workflow"></a>Werkstroom

De Ink Recognizer API is een RESTful webservice, waardoor het gemakkelijk is om te bellen vanuit elke programmeertaal die HTTP-verzoeken kan doen en JSON kan ontleden.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Na het aanmelden:

1. Neem uw inktlijngegevens op en [maak deze op](concepts/send-ink-data.md#sending-ink-data) in geldige JSON.
1. Stuur een aanvraag naar de Ink Recognizer API met uw gegevens.
1. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Probeer een snelle start in de volgende talen om te beginnen met het maken van aanroepen naar de Ink Recognizer API.
* [C #](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [Javascript](quickstarts/javascript.md)

Als u wilt zien hoe de Ink Recognition API werkt in een digitale inktapp, bekijkt u de volgende voorbeeldtoepassingen op GitHub:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
