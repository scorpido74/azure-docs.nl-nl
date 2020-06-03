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
ms.openlocfilehash: 550579b40470d7a1ad02031b8140e7d0a7164f46
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310594"
---
# <a name="long-audio-api-preview"></a>Lange audio-API (preview-versie)

De API voor lange audio is ontworpen voor asynchrone synthese van tekst in lange vorm naar spraak (bijvoorbeeld: audio boeken, nieuws artikelen en documenten). Deze API retourneert geen getakte audio in realtime. in plaats daarvan is het raadzaam om de reactie (s) te controleren en de uitvoer (en) te gebruiken zodra deze vanuit de service beschikbaar worden gesteld. In tegens telling tot de tekst-naar-spraak-API die wordt gebruikt door de spraak-SDK, kan de lange audio-API meer dan tien minuten gesynthesizerde audio maken, waardoor deze ideaal is voor uitgevers en platforms voor audio-inhoud.

Aanvullende voor delen van de API voor lange audio:

* De gesynthesizerde spraak die door de service wordt geretourneerd, maakt gebruik van de beste Neural stemmen.
* Het is niet nodig om een spraak eindpunt te implementeren omdat er stemmen zijn die geen real-time batch-modus hebben.

> [!NOTE]
> De lange audio-API ondersteunt nu zowel [open bare Neural stemmen](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) als [aangepaste Neural stemmen](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice#custom-neural-voices).

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
> Voor Chinees (vasteland), Chinees (Hong Kong SAR), Chinees (Taiwan), Japans en Koreaans wordt één woord als twee tekens beschouwd. 

## <a name="submit-synthesis-requests"></a>Synthese aanvragen verzenden

Nadat u de invoer inhoud hebt voor bereid, volgt u de Quick Start van de [Audio synthese voor lange vorm](https://aka.ms/long-audio-python) om de aanvraag in te dienen. Als u meer dan één invoer bestand hebt, moet u meerdere aanvragen indienen. 

De **HTTP-status codes** wijzen op veelvoorkomende fouten.

| API | HTTP-statuscode | Beschrijving | Voorstel |
|-----|------------------|-------------|----------|
| Maken | 400 | De stem synthese is niet ingeschakeld in deze regio. | Wijzig de sleutel van het spraak abonnement met een ondersteunde regio. |
|        | 400 | Alleen het **standaard** spraak abonnement voor deze regio is geldig. | Wijzig de code van het spraak abonnement in de prijs categorie Standard. |
|        | 400 | Overschrijdt de limiet van 20.000 aanvragen voor het Azure-account. Verwijder enkele aanvragen voordat u nieuwe verzendt. | De server bewaart Maxi maal 20.000 aanvragen voor elk Azure-account. Enkele aanvragen verwijderen voordat nieuwe worden verzonden. |
|        | 400 | Dit model kan niet worden gebruikt in de spraak-synthese: {modelID}. | Controleer of de status van de {modelID} juist is. |
|        | 400 | De regio voor de aanvraag komt niet overeen met de regio voor het model: {modelID}. | Controleer of de regio van de {modelID} overeenkomt met de regio van de aanvraag. |
|        | 400 | De spraak-synthese ondersteunt alleen het tekst bestand in UTF-8-code ring met de byte volgorde markering. | Zorg ervoor dat de invoer bestanden zich in UTF-8-code ring bevinden met de markering byte volgorde. |
|        | 400 | Alleen geldige SSML-invoer waarden zijn toegestaan in de Voice Synthesis-aanvraag. | Controleer of de ingevoerde SSML-expressies juist zijn. |
|        | 400 | De spraak naam {Voice name} is niet gevonden in het invoer bestand. | De stem naam van de invoer SSML is niet uitgelijnd met de model-ID. |
|        | 400 | De hoeveelheid alinea's in het invoer bestand moet kleiner zijn dan 10.000. | Zorg ervoor dat de alinea in het bestand kleiner is dan 10.000. |
|        | 400 | Het invoer bestand moet langer zijn dan 400 tekens. | Zorg ervoor dat uw invoer bestand langer is dan 400 tekens. |
|        | 404 | Het model dat is gedeclareerd in de definitie van de audio-synthese, is niet gevonden: {modelID}. | Controleer of {modelID} juist is. |
|        | 429 | De limiet voor actieve audio-synthese overschrijdt. Wacht tot sommige aanvragen zijn voltooid. | De server mag Maxi maal 120 aanvragen voor elk Azure-account uitvoeren en in de wachtrij plaatsen. Wacht tot er nieuwe aanvragen zijn verzonden totdat sommige aanvragen zijn voltooid. |
| Alles       | 429 | Er zijn te veel aanvragen. | De client mag Maxi maal 5 aanvragen voor de server per seconde indienen voor elk Azure-account. Verminder de aanvraag hoeveelheid per seconde. |
| Verwijderen    | 400 | De stem synthese taak is nog in gebruik. | U kunt alleen **voltooide** of **mislukte**aanvragen verwijderen. |
| GetByID   | 404 | De opgegeven entiteit is niet gevonden. | Controleer of de synthese-ID juist is. |

## <a name="regions-and-endpoints"></a>Regio's en eind punten

De lange audio-API is beschikbaar in meerdere regio's met unieke eind punten.

| Regio | Eindpunt |
|--------|----------|
| Australië - oost | `https://australiaeast.customvoice.api.speech.microsoft.com` |
| Canada - midden | `https://canadacentral.customvoice.api.speech.microsoft.com` |
| VS - oost | `https://eastus.customvoice.api.speech.microsoft.com` |
| India - centraal | `https://centralindia.customvoice.api.speech.microsoft.com` |
| VS - zuid-centraal | `https://southcentralus.customvoice.api.speech.microsoft.com` |
| Azië - zuidoost | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Verenigd Koninkrijk Zuid | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa -west | `https://westeurope.customvoice.api.speech.microsoft.com` |
| VS - west 2 | `https://westus2.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Indelingen audio-uitvoer

Flexibele indelingen voor audio-uitvoer worden ondersteund. U kunt audio-uitvoer per alinea genereren of de audio-uitvoer in één uitvoer samen voegen door de para meter ' concatenateResult ' in te stellen. De volgende indelingen voor audio-uitvoer worden ondersteund door de lange audio-API:

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
