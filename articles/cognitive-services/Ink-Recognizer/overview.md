---
title: Wat is Ink Recognizer? - Ink Recognizer API
titleSuffix: Azure Cognitive Services
description: Integreer de Ink Recognizer in uw toepassingen, websites, hulpprogramma's en andere oplossingen zodat inktstreekgegevens kunnen worden geïdentificeerd en gebruikt als invoer.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: 7bcda79403e021384fc987a67865441dd8732885
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927450"
---
# <a name="what-is-the-ink-recognizer-api"></a>Wat is de Ink Recognizer-API?


De Ink Recognizer Cognitive Service biedt een REST API in de cloud voor het analyseren en herkennen van digitale inktinhoud. In tegenstelling tot services die gebruikmaken van Optical Character Recognition (OCR), vereist deze API digitale inktstreekgegevens als invoer. Digitale inktstreken zijn op tijd geordende sets van 2D-punten (X/Y-coördinaten) die de beweging van invoerhulpprogramma's als digitale pennen of vingers vertegenwoordigen. Vervolgens worden de vormen en handgeschreven inhoud van de invoer herkend en wordt een JSON-antwoord met alle herkende entiteiten geretourneerd.

![Een stroomdiagram waarin de verzending van een inktstreek naar de API wordt weergegeven](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Functies

Met de Ink Recognizer API kunt u eenvoudig handgeschreven inhoud in uw toepassingen herkennen. 

|Functie  |Beschrijving  |
|---------|---------|
| Handschriftherkenning | U kunt handgeschreven inhoud herkennen in 63 [kerntalen en -landinstellingen](language-support.md). | 
| Indelingsherkenning | U kunt structurele informatie ophalen over de digitale inktinhoud. U kunt de inhoud opsplitsen in schrijfgebieden, alinea's, lijnen, woorden en lijsten met opsommingstekens. Uw toepassingen kunnen vervolgens de indelingsinformatie gebruiken om aanvullende functies, zoals automatisch lijsten opmaken en vormen uitlijnen, te maken. |
| Vormherkenning | U kunt de meest gebruikte [geometrische vormen](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) herkennen bij het maken van notities. |
| Gecombineerde herkenning van vormen en tekst | U kunt herkennen welke inktstreken bij vormen of handgeschreven inhoud horen en ze afzonderlijk classificeren.|

## <a name="workflow"></a>Werkstroom

De Ink Recognizer API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Nadat u zich hebt geregistreerd:

1. [Formatteer](concepts/send-ink-data.md#sending-ink-data) uw inktstreekgegevens in een geldige JSON. De API accepteert maximaal 1500 inktstreken per aanvraag. 
1. Verzend een aanvraag naar de Ink Recognizer-API met uw gegevens.
1. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="next-steps"></a>Volgende stappen

Voer een quickstart uit in de volgende talen om aanroepen uit te voeren naar de Ink Recognizer API.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Bekijk de volgende voorbeeldtoepassingen op GitHub voor meer informatie over de werking van de Ink Recognition-API in een app voor digitaal handschrift:
* [C# en Universal Windows Platform (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# en Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Webbrowser-app (Javascript)](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Mobiele app (Java en Android)](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Mobiele app (Swift en iOS)](https://go.microsoft.com/fwlink/?linkid=2089805)
