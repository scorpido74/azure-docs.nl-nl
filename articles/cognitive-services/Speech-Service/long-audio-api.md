---
title: Lange audio-API (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over hoe de lange audio-API is ontworpen voor asynchrone synthese van lange-vorm tekst naar spraak.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: trbye
ms.openlocfilehash: b7cca314ec59e46cf17751b1aec28b5c3ea029ed
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81401060"
---
# <a name="long-audio-api-preview"></a>Lange audio-API (preview-versie)

De lange audio-API is ontworpen voor asynchrone synthese van tekst in lange vorm naar spraak (bijvoorbeeld: audio boeken). Deze API retourneert geen getakte audio in realtime. in plaats daarvan is het raadzaam om de reactie (s) te controleren en de uitvoer (en) te gebruiken zodra deze vanuit de service beschikbaar worden gesteld. In tegens telling tot de tekst-naar-spraak-API die wordt gebruikt door de spraak-SDK, kan de lange audio-API meer dan tien minuten gesynthesizerde audio maken, waardoor deze ideaal is voor uitgevers en platforms voor audio-inhoud.

Aanvullende voor delen van de API voor lange audio:

* De door de service geretourneerde gesynthesizerde spraak maakt gebruik van Neural stemmen, waarmee een hoge betrouw baarheid van audio-uitvoer wordt gegarandeerd.
* Omdat realtime-antwoorden niet worden ondersteund, hoeft u geen spraak-eind punt te implementeren.

> [!NOTE]
> De lange audio-API ondersteunt nu alleen [aangepaste Neural-stem](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

## <a name="workflow"></a>Werkstroom

Wanneer u de Long audio-API gebruikt, moet u doorgaans een tekst bestand of bestanden verzenden die moeten worden gesynthesizerd, de status controleren en vervolgens de status geslaagd hebben, kunt u de audio-uitvoer downloaden.

Dit diagram bevat een overzicht op hoog niveau van de werk stroom.

![Lang audio API-werk stroom diagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Inhoud voorbereiden voor synthese

Wanneer u het tekst bestand voorbereidt, moet u het volgende controleren:

* Is tekst zonder opmaak (. txt) of SSML tekst (. txt)
* Is gecodeerd als [UTF-8 met een byte order Mark (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Is één bestand, geen zip
* Bevat meer dan 400 tekens voor onbewerkte tekst of 400 [factureer bare tekens](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) voor SSML tekst en minder dan 10.000 alinea's
  * Voor tekst zonder opmaak wordt elke alinea gescheiden door op **Enter/Return** - [invoer voor beeld tekst zonder opmaak](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) weer te geven
  * Voor SSML tekst wordt elk SSML-stuk beschouwd als een alinea. SSML stuks moeten worden gescheiden door verschillende alinea's- [voor beeld van SSML-tekst invoer](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt) weer geven
> [!NOTE]
> Voor Chinees (vasteland), Chinees (Hongkong), Chinees (Taiwan), Japans en Koreaans wordt één woord als twee tekens beschouwd. 

## <a name="submit-synthesis-requests"></a>Synthese aanvragen verzenden

Nadat u de invoer inhoud hebt voor bereid, volgt u de Quick Start van de [Audio synthese voor lange vorm](https://aka.ms/long-audio-python) om de aanvraag in te dienen. Als u meer dan één invoer bestand hebt, moet u meerdere aanvragen indienen. Er zijn enkele beperkingen waar u rekening mee moet houden: 
* De client mag Maxi maal 5 aanvragen voor de server per seconde indienen voor elk account van een Azure-abonnement. Als deze de beperking overschrijdt, krijgt de client een fout code van 429 (te veel aanvragen). Verminder de aanvraag hoeveelheid per seconde
* De server mag Maxi maal 120 aanvragen voor elk account van een Azure-abonnement uitvoeren en in de wachtrij plaatsen. Als de limiet wordt overschreden, retourneert de server een fout code van 429 (te veel aanvragen). Een ogen blik geduld en het verzenden van een nieuwe aanvraag voor komen totdat sommige aanvragen zijn voltooid
* Server bewaart Maxi maal 20.000 aanvragen voor elk account van een Azure-abonnement. Als deze de beperking overschrijdt, moet u enkele aanvragen verwijderen alvorens nieuwe te verzenden

## <a name="audio-output-formats"></a>Indelingen audio-uitvoer

Flexibele indelingen voor audio-uitvoer worden ondersteund. U kunt audio-uitvoer per alinea genereren of de audio in één uitvoer samen voegen door de para meter ' concatenateResult ' in te stellen. De volgende indelingen voor audio-uitvoer worden ondersteund door de lange audio-API:

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
* [Voorbeeld code: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Voorbeeld code: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
