---
title: Lange audio-API (Preview) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Ontdek hoe de Lange Audio API is ontworpen voor asynchrone synthese van tekst naar spraak in lange vorm.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: 033103e10971be2f6c220ccdb2c3586c7dc2ef05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76900245"
---
# <a name="long-audio-api-preview"></a>Lange audio-API (voorbeeld)

De Long Audio API is ontworpen voor asynchrone synthese van tekst naar spraak in lange vorm (bijvoorbeeld: audioboeken). Deze API retourneert gesynthetiseerde audio niet in realtime, in plaats daarvan is de verwachting dat u de respons(s) zult peilen en de uitvoer(s) verbruikt zoals deze beschikbaar worden gesteld vanuit de service. In tegenstelling tot de tekst-naar-spraak-API die wordt gebruikt door de Speech SDK, kan de Long Audio API gesynthetiseerde audio langer dan 10 minuten maken, waardoor deze ideaal is voor uitgevers en audio-inhoudsplatforms.

Extra voordelen van de Long Audio API:

* Gesynthetiseerde spraak die door de service wordt geretourneerd, maakt gebruik van neurale stemmen, wat zorgt voor high-fidelity audio-uitgangen.
* Aangezien realtime reacties niet worden ondersteund, is het niet nodig om een spraakeindpunt te implementeren.

> [!NOTE]
> De Long Audio API ondersteunt nu alleen [Custom Neural Voice.](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices)

## <a name="workflow"></a>Werkstroom

Wanneer u de Lange Audio-API gebruikt, dient u doorgaans een tekstbestand of bestanden in dat moet worden gesynthetiseerd, peiling voor de status en u, als de status succesvol is, de audio-uitvoer downloaden.

Dit diagram biedt een overzicht op hoog niveau van de workflow.

![Lange audio-API-werkstroomdiagram](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Inhoud voorbereiden op synthese

Controleer bij het voorbereiden van uw tekstbestand of het:

* Is ofwel platte tekst (.txt) of SSML-tekst (.txt)
* Is gecodeerd als [UTF-8 met Byte Order Mark (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* Is een enkel bestand, geen zip
* Bevat meer dan 400 tekens voor platte tekst of 400 [opeenstoterede tekens](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech#pricing-note) voor SSML-tekst en minder dan 10.000 alinea's
  * Voor platte tekst wordt elke alinea gescheiden door op **Enter/Return** - Voorbeeld van [platte tekstinvoer](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) weergeven
  * Voor SSML-tekst wordt elk SSML-stuk beschouwd als een alinea. SSML-stukken worden gescheiden door verschillende alinea's - Voorbeeld van [SSML-tekstinvoer weergeven](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)
> [!NOTE]
> Voor Chinees (Vasteland), Chinees (Hong Kong), Chinees (Taiwan), Japans, en Koreaans, zal één woord als twee karakters worden geteld. 

## <a name="submit-synthesis-requests"></a>Syntheseverzoeken indienen

Volg na het voorbereiden van de invoerinhoud de [audiosynthese in het lange formulier snel aan](https://aka.ms/long-audio-python) om de aanvraag in te dienen. Als u meer dan één invoerbestand hebt, moet u meerdere aanvragen indienen. Er zijn een aantal beperkingen om bewust te zijn van: 
* Client mag maximaal 5 aanvragen indienen bij server per seconde voor elk Azure-abonnementsaccount. Als deze de beperking overschrijdt, krijgt de client een foutcode van 429 (te veel aanvragen). Verlaag het aanvraagbedrag per seconde
* Server mag maximaal 120 aanvragen uitvoeren en in de wachtrij staan voor elk Azure-abonnementsaccount. Als de beperking wordt beperkt, retourneert de server een foutcode van 429 (te veel aanvragen). Wacht en vermijd het indienen van nieuwe aanvraag totdat sommige aanvragen zijn voltooid
* Server houdt maximaal 20.000 aanvragen bij voor elk Azure-abonnementsaccount. Als de beperking wordt beperkt, verwijdert u een aantal aanvragen voordat u nieuwe aanvragen indient

## <a name="audio-output-formats"></a>Audio-uitvoerindelingen

Wij ondersteunen flexibele audio-uitvoerformaten. U audio-uitgangen per alinea genereren of de audio in één uitvoer inéén uitgang en de parameter 'concatenateResult' instellen. De volgende audio-uitvoerindelingen worden ondersteund door de Lange Audio-API:

> [!NOTE]
> De standaard audio-indeling is riff-16khz-16bit-mono-pcm.

* riff-8khz-16bit-mono-pcm
* riff-16khz-16bit-mono-pcm
* riff-24khz-16bit-mono-pcm
* riff-48khz-16bit-mono-pcm
* audio-16khz-32kbitrate-mono-mp3
* audio-16khz-64kbitrate-mono-mp3
* audio-16khz-128kbitrate-mono-mp3
* audio-24khz-48kbitrate-mono-mp3
* audio-24khz-96kbitrate-mono-mp3
* audio-24khz-160kbitrate-mono-mp3

## <a name="quickstarts"></a>Snelstartgidsen

We bieden quickstarts die zijn ontworpen om u te helpen de Long Audio API succesvol uit te voeren. Deze tabel bevat een lijst met quickstarts voor lange audio-API die zijn georganiseerd op taal.

* [Snelstart: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Voorbeeldcode
Voorbeeldcode voor Long Audio API is beschikbaar op GitHub.

* [Voorbeeldcode: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Voorbeeldcode: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Voorbeeldcode: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)
