---
title: De audio stroom voor gesegmenteerde overdracht | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Gesegmenteerde trasfer gebruiken om een audio stroom te verzenden naar de Bing Speech-Service
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 60428a7a5e28b7c9d6cd76f31374f24df48423a6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966492"
---
# <a name="chunked-transfer-encoding"></a>Gesegmenteerde overdrachts codering

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Als u spraak naar tekst wilt transcriberen, kunt u met micro soft Speech Recognition API de audio als één geheel segment verzenden of de audio in kleine segmenten Chop. Voor efficiënte streaming van audio en het verminderen van transcriptie latentie, is het raadzaam om [gesegmenteerde overdrachts codering](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) te gebruiken voor het streamen van de audio naar de service. Andere implementaties kunnen leiden tot een snellere, door de gebruiker waargenomen latentie. Zie de pagina [Audio streams](../concepts.md#audio-streams) voor meer informatie.

> [!NOTE]
> U kunt niet meer dan 10 seconden aan audio in een aanvraag uploaden en de totale duur van de aanvraag mag niet langer zijn dan 14 seconden.
> [!NOTE]
> U moet de gesegmenteerde overdrachts codering alleen opgeven als u de [rest-api's](../GetStarted/GetStartedREST.md) gebruikt om de spraak service aan te roepen. Toepassingen die gebruikmaken van [client bibliotheken](../GetStarted/GetStartedClientLibraries.md) , hoeven de gesegmenteerde overdrachts codering niet te configureren.

De volgende code laat zien hoe u de gesegmenteerde overdrachts codering instelt en een audio bestand verzendt naar segmenten van 1024 bytes.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```
