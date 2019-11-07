---
title: Lange audio-API (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 391cddbbd1b69fb7cb5422adbaea2f3378e273bf
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580166"
---
# <a name="long-audio-api-preview"></a>Lange audio-API (preview-versie)

De lange audio-API is ontworpen voor asynchrone synthese van tekst in lange vorm naar spraak (bijvoorbeeld: audio boeken). Deze API retourneert geen getakte audio in realtime. in plaats daarvan is het raadzaam om de reactie (s) te controleren en de uitvoer (en) te gebruiken zodra deze vanuit de service beschikbaar worden gesteld. In tegens telling tot de tekst-naar-spraak-API die wordt gebruikt door de spraak-SDK, kan de lange audio-API meer dan tien minuten gesynthesizerde audio maken, waardoor deze ideaal is voor uitgevers en platforms voor audio-inhoud.

Aanvullende voor delen van de API voor lange audio:

* De door de service geretourneerde gesynthesizerde spraak maakt gebruik van Neural stemmen, waarmee een hoge betrouw baarheid van audio-uitvoer wordt gegarandeerd.
* Omdat realtime-antwoorden niet worden ondersteund, hoeft u geen spraak-eind punt te implementeren.

## <a name="workflow"></a>Werkstroom

Wanneer u de Long audio-API gebruikt, moet u doorgaans een tekst bestand of bestanden verzenden die moeten worden gesynthesizerd, de status controleren en vervolgens de status geslaagd hebben, kunt u de audio-uitvoer downloaden.

Dit diagram bevat een overzicht op hoog niveau van de werk stroom.

![Lang audio API-werk stroom diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Inhoud voorbereiden voor synthese

Wanneer u het tekst bestand voorbereidt, moet u het volgende controleren:

* Is tekst zonder opmaak (. txt) of SSML tekst (. txt)
  * Voor tekst zonder opmaak wordt elke alinea gescheiden door op **Enter/Return** - [invoer voor beeld tekst zonder opmaak](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) weer te geven
  * Voor SSML tekst wordt elk SSML-stuk beschouwd als een alinea. SSML stuks moeten worden gescheiden door verschillende alinea's: [SSML tekst invoer voorbeeld](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)weer geven. Zie voor taal code [spraak synthese Markup Language (SSML) (Engelstalig)](speech-synthesis-markup.md)
* Is gecodeerd als [UTF-8 met een byte order Mark (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Bevat meer dan 10.000 tekens of meer dan 50 alinea's
* Is één bestand, geen zip

## <a name="audio-output-formats"></a>Indelingen audio-uitvoer

De volgende indelingen voor audio-uitvoer worden ondersteund door de lange audio-API:

> [!NOTE]
> De standaard audio-indeling is RIFF-16khz-16-mono-PCM.

* riff-8khz-16-mono-PCM
* riff-16khz-16-mono-PCM
* riff-24khz-16-mono-PCM
* riff-48khz-16-mono-PCM
* Audio-16khz-32kbitrate-mono-mp3
* Audio-16khz-64kbitrate-mono-mp3
* Audio-16khz-128kbitrate-mono-mp3
* Audio-24khz-48kbitrate-mono-mp3
* Audio-24khz-96kbitrate-mono-mp3
* Audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Snelstartgidsen

We bieden Quick starts die zijn ontworpen om u te helpen de lange audio-API uit te voeren. Deze tabel bevat een lijst met lange audio-API-Quick starts, geordend op taal.

* [Snelstartgids: python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Voorbeeldcode
Voorbeeld code voor lange audio-API is beschikbaar op GitHub.

* [Voorbeeld code: python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Voorbeeld code:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Voorbeeld code: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
