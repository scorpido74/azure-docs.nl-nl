---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940737"
---
In deze quickstart leert u hoe u tekst naar spraak kunt converteren met behulp van de Speech-service en cURL.

Zie het artikel [Overzicht](../../../text-to-speech.md) voor een diepgaande bespreking van de concepten van tekst-naar-spraak.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Tekst naar spraak converteren

Voer de volgende opdracht uit bij een opdrachtprompt: U moet de volgende waarden in de opdracht gebruiken.
- De abonnementssleutel voor de Speech-service.
- De regio voor de Speech-service.

Mogelijk wilt u ook de volgende waarden wijzigen.
- De waarde van de `X-Microsoft-OutputFormat`-header, waarmee de indeling van de audio-uitvoer wordt bepaald. Een lijst met ondersteunde indelingen voor audio-uitvoer vindt u in de [referentie voor tekst-naar-spraak REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- De stem voor de uitvoer. Zie de volgende sectie voor een lijst met stemmen die beschikbaar zijn voor uw Speech-eindpunt.
- Het uitvoerbestand. In dit voorbeeld wordt het antwoord van de server naar het bestand `output.wav` gestuurd.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Vermelden van beschikbare stemmen voor uw Speech-eindpunt

Voer de volgende opdracht uit om de beschikbare stemmen voor uw Speech-eindpunt te vermelden.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

U krijgt een antwoord dat lijkt op het volgende.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
