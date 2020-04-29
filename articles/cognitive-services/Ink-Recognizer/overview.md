---
title: Wat is inkt herkenning? -API voor inkt herkenning
titleSuffix: Azure Cognitive Services
description: Integreer de inkt herkenning in uw toepassingen, websites, hulpprogram ma's en andere oplossingen zodat inkt lijn gegevens kunnen worden geïdentificeerd en gebruikt als invoer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: 2d00a489af35f5960aaeb1dda7add5337d48d57d
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448156"
---
# <a name="what-is-the-ink-recognizer-api"></a>Wat is de Ink Recognizer-API?


De cognitieve service voor inkt herkenning biedt een REST API in de Cloud voor het analyseren en herkennen van inhoud van digitale inkt. In tegens telling tot services die gebruikmaken van optische teken herkenning (OCR), is voor de API digitale inkt lijn gegevens als invoer vereist. Digitale inkt streken zijn geordende sets van 2D-punten (X, Y-coördinaten) die de beweging van invoer hulpprogramma's zoals digitale pennen of vingers vertegenwoordigen. Vervolgens worden de vormen en handgeschreven inhoud van de invoer herkend en wordt een JSON-antwoord geretourneerd met alle herkende entiteiten.

![Een stroom diagram waarin de verzen ding van een Peninvoer naar de API wordt beschreven](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Functies

Met de API voor inkt herkenning kunt u eenvoudig handgeschreven inhoud in uw toepassingen herkennen. 

|Functie  |Beschrijving  |
|---------|---------|
| Handschrift herkenning | Handgeschreven inhoud in 63 kern [talen en land instellingen](language-support.md)herkennen. | 
| Indelings herkenning | Structuur informatie over de inhoud van de digitale inkt ophalen. De inhoud opsplitsen in schrijf gebieden, alinea's, lijnen, woorden, lijsten met opsommings tekens. Uw toepassingen kunnen vervolgens de lay-outinformatie gebruiken om aanvullende functies, zoals automatische lijst opmaak en uitlijning van shapes, te maken. |
| Vorm herkenning | De meest gebruikte [geometrische vormen](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) herkennen bij het maken van notities. |
| Gecombineerde vormen en tekst herkenning | U kunt herkennen welke inkt streken bij vormen of handgeschreven inhoud horen en ze afzonderlijk classificeren.|

## <a name="workflow"></a>Werkstroom

De API voor inkt herkenning is een REST-webservice, waarmee u eenvoudig kunt aanroepen vanuit elke programmeer taal die HTTP-aanvragen kan maken en JSON kan parseren.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Nadat u zich hebt aangemeld:

1. Maak uw inkt lijn gegevens en [Format teer deze](concepts/send-ink-data.md#sending-ink-data) in een geldige JSON.
1. Verzend een aanvraag naar de inkt Recognizer-API met uw gegevens.
1. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Voer een Snelstartgids uit in de volgende talen om te beginnen met het maken van aanroepen naar de API voor inkt herkenning.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [Javascript](quickstarts/javascript.md)

Bekijk de volgende voorbeeld toepassingen op GitHub voor meer informatie over de werking van de API voor inkt herkenning in een digitale hand schrift-app:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
