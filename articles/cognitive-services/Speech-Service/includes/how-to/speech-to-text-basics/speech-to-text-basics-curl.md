---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425203"
---
In deze quickstart leert u hoe u spraak naar tekst kunt converteren met behulp van de Speech-service en cURL.

Zie het artikel [Overzicht](../../../speech-to-text.md) voor een diepgaande bespreking van de concepten van spraak-naar-tekst.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een Azure-account en een abonnement op de Speech-service hebt. Als u geen account en abonnement hebt, [kunt u de Speech-service gratis uitproberen](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Spraak naar tekst converteren

Voer de volgende opdracht uit op een opdrachtprompt. U moet de volgende waarden gebruiken in de opdracht.
- Uw abonnementssleutel voor de Speech-service.
- De regio voor de Speech-service.
- Het bestandspad voor audio-invoer. U kunt audiobestanden genereren met [tekst-naar-spraak](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

U zou een antwoord moeten krijgen dat lijkt op het volgende:

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Zie voor meer informatie de [naslaghandleiding](../../../rest-speech-to-text.md)voor de  REST-API voor spraak-naar-tekst.