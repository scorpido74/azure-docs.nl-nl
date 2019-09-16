---
title: Aan de slag met de Bing Speech Recognition API in Java script | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gebruik de Bing Speech Recognition API in Cognitive Services om toepassingen te ontwikkelen die voortdurend gesp roken audio naar tekst converteren.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: eef1a4885b77ae94f11d3d5bda5ded9b70ed63a4
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965819"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Aan de slag met de API voor spraak herkenning in Java script

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

U kunt toepassingen ontwikkelen die gesp roken audio naar tekst converteren met behulp van de API voor spraak herkenning. De Java script-client bibliotheek maakt gebruik van het [WebSocket-protocol Speech Service](../API-Reference-REST/websocketprotocol.md), waarmee u tegelijkertijd getranscribeerde tekst kunt praten en ontvangen. Dit artikel helpt u aan de slag te gaan met de API voor spraak herkenning in Java script.

## <a name="prerequisites"></a>Vereisten

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Abonneer u op de spraakherkennings-API en ontvang een gratis proef abonnement sleutel

De Speech-API maakt deel uit van Cognitive Services. U kunt abonnements sleutels voor een gratis proef versie downloaden van de pagina [Cognitive Services abonnement](https://azure.microsoft.com/try/cognitive-services/) . Nadat u de spraak-API hebt geselecteerd, selecteert u **API-sleutel ophalen** om de sleutel op te halen. Er wordt een primaire en secundaire sleutel geretourneerd. Beide sleutels zijn gekoppeld aan hetzelfde quotum, dus u kunt beide sleutels gebruiken.

> [!IMPORTANT]
> Een abonnements sleutel ophalen. Voordat u Speech client-bibliotheken kunt gebruiken, moet u een [abonnements sleutel](https://azure.microsoft.com/try/cognitive-services/)hebben.

## <a name="get-started"></a>Aan de slag

In deze sectie wordt u begeleid bij de stappen die nodig zijn om een HTML-voorbeeld pagina te laden. Het voor beeld bevindt zich in onze [github-opslag plaats](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). U kunt **het voor beeld rechtstreeks** vanuit de opslag plaats openen of **het voor beeld openen vanuit een lokale kopie** van de opslag plaats.

> [!NOTE]
> Sommige browsers blok keren de toegang tot onveilige oorsprong van de microfoon. Het wordt daarom aangeraden om het ' voor beeld '/' uw app ' te hosten op https om deze te laten werken op alle ondersteunde browsers.

### <a name="open-the-sample-directly"></a>Het voor beeld rechtstreeks openen

Schaf een abonnements sleutel aan zoals hierboven wordt beschreven. Open vervolgens de [koppeling naar het voor beeld](https://htmlpreview.github.io/?https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Hiermee wordt de pagina in de standaard browser geladen (gerenderd met [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Het voor beeld openen vanuit een lokale kopie

Als u het voor beeld lokaal wilt uitproberen, moet u deze opslag plaats klonen:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Compileer de type script-bronnen en bundel ze in één java script-bestand ([NPM](https://www.npmjs.com/) moet op uw computer worden geïnstalleerd). Ga naar de hoofdmap van de gekloonde opslag plaats en voer de volgende opdrachten uit:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Open `samples\browser\Sample.html` in uw favoriete browser.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u de SDK in uw eigen webpagina kunt toevoegen, is [hier](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)beschikbaar.

## <a name="remarks"></a>Opmerkingen

- De API voor spraak herkenning ondersteunt drie [herkennings modi](../concepts.md#recognition-modes). U kunt overschakelen naar de modus door de functie **Setup ()** in het HTML-voorbeeld bestand te bijwerken. In het voor beeld wordt de `Interactive` modus standaard ingesteld op. Als u de modus wilt wijzigen, werkt `SR.RecognitionMode.Interactive` u de para meter bij naar een andere modus. Wijzig de para meter bijvoorbeeld in `SR.RecognitionMode.Conversation`.
- Zie [ondersteunde talen](../API-Reference-REST/supportedlanguages.md)voor een volledige lijst met ondersteunde talen.

## <a name="related-topics"></a>Verwante onderwerpen

- [Java script-voorbeeld opslagplaats voor spraakherkennings-API](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Aan de slag met de REST API](GetStartedREST.md)
