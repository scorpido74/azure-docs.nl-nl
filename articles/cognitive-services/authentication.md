---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Er zijn drie manieren om een aanvraag te verifiëren voor een Azure Cognitive Services-bron: een abonnementssleutel, een token aan toonder of een multiserviceabonnement. In dit artikel leert u over elke methode en hoe u een verzoek indient.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423940"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Aanvragen verifiëren voor Azure Cognitive Services

Elk verzoek om een Azure Cognitive Service moet een verificatiekop bevatten. Deze header geeft een abonnementssleutel of toegangstoken door, die wordt gebruikt om uw abonnement voor een service of groep services te valideren. In dit artikel leert u over drie manieren om een aanvraag te verifiëren en de vereisten voor elk verzoek.

* [Verifiëren met een abonnementssleutel voor één service](#authenticate-with-a-single-service-subscription-key)
* [Verifiëren met een multi-service abonnementssleutel](#authenticate-with-a-multi-service-subscription-key)
* [Verifiëren met een token](#authenticate-with-an-authentication-token)
* [Verifiëren met Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Vereisten

Voordat u een aanvraag indient, hebt u een Azure-account en een Azure Cognitive Services-abonnement nodig. Als je al een account hebt, ga je door naar de volgende sectie. Als u geen account hebt, hebben we een handleiding om u binnen enkele minuten in te stellen: [Een Cognitive Services-account maken voor Azure.](cognitive-services-apis-create-account.md)

U uw abonnementssleutel van de [Azure-portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) ophalen nadat u uw account hebt gemaakt of een [gratis proefversie](https://azure.microsoft.com/try/cognitive-services/my-apis)activeren.

## <a name="authentication-headers"></a>Verificatiekoppen

Laten we snel de verificatiekoppen bekijken die beschikbaar zijn voor gebruik met Azure Cognitive Services.

| Header | Beschrijving |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Gebruik deze koptekst om te verifiëren met een abonnementssleutel voor een specifieke service of een multiserviceabonnementssleutel. |
| Ocp-Apim-Subscription-Regio | Deze header is alleen vereist wanneer u een multi-service abonnementssleutel gebruikt met de [Translator Text API.](./Translator/reference/v3-0-reference.md) Gebruik deze koptekst om het abonnementsgebied op te geven. |
| Autorisatie | Gebruik deze koptekst als u een verificatietoken gebruikt. De stappen om een tokenuitwisseling uit te voeren, worden in de volgende secties beschreven. De opgegeven waarde volgt `Bearer <TOKEN>`op deze notatie: . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Verifiëren met een abonnementssleutel voor één service

De eerste optie is om een aanvraag te verifiëren met een abonnementssleutel voor een specifieke service, zoals Translator Text. De sleutels zijn beschikbaar in de Azure-portal voor elke resource die u hebt gemaakt. Als u een abonnementssleutel wilt gebruiken om een `Ocp-Apim-Subscription-Key` aanvraag te verifiëren, moet deze worden doorgegeven als de koptekst.

Deze voorbeeldaanvragen laten zien `Ocp-Apim-Subscription-Key` hoe u de koptekst moet gebruiken. Houd er rekening mee dat u bij het gebruik van dit voorbeeld een geldige abonnementssleutel moet opnemen.

Dit is een voorbeeldaanroep naar de Bing Web Search API:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Dit is een voorbeeldaanroep naar de Translator Text API:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

De volgende video toont het gebruik van een Cognitive Services-sleutel.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Verifiëren met een multi-service abonnementssleutel

>[!WARNING]
> Op dit moment ondersteunen deze services **geen** multiservicesleutels: QnA Maker, Speech Services, Custom Vision en Anomaly Detector.

Deze optie maakt ook gebruik van een abonnementssleutel om aanvragen te verifiëren. Het belangrijkste verschil is dat een abonnementssleutel niet is gekoppeld aan een specifieke service, in plaats daarvan kan één sleutel worden gebruikt om aanvragen voor meerdere cognitieve services te verifiëren. Zie [Prijzen voor Cognitieve Services](https://azure.microsoft.com/pricing/details/cognitive-services/) voor informatie over regionale beschikbaarheid, ondersteunde functies en prijzen.

De abonnementssleutel wordt in elke `Ocp-Apim-Subscription-Key` aanvraag als koptekst geleverd.

[![Multi-service abonnementssleuteldemonstratie voor Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Ondersteunde regio’s

Wanneer u de multiservice-abonnementssleutel gebruikt `api.cognitive.microsoft.com`om een verzoek in te dienen, moet u de regio opnemen in de URL. Bijvoorbeeld: `westus.api.cognitive.microsoft.com`.

Wanneer u een abonnementssleutel voor meerdere diensten gebruikt met de `Ocp-Apim-Subscription-Region` Translator Text API, moet u het abonnementsgebied opgeven met de koptekst.

Multiserviceverificatie wordt in deze regio's ondersteund:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Voorbeeldaanvragen

Dit is een voorbeeldaanroep naar de Bing Web Search API:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Dit is een voorbeeldaanroep naar de Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Verifiëren met een verificatietoken

Sommige Azure Cognitive Services accepteren en vereisen in sommige gevallen een verificatietoken. Momenteel ondersteunen deze services verificatietokens:

* API voor tekstvertaling
* Spraakdiensten: REST-API voor spraak naar tekst
* Spraakdiensten: REST API voor tekst naar spraak

>[!NOTE]
> QnA Maker gebruikt ook de header Autorisatie, maar vereist een eindpuntsleutel. Zie [QnA Maker: Antwoord van kennisbank](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)voor meer informatie.

>[!WARNING]
> De services die verificatietokens ondersteunen, kunnen in de loop van de tijd veranderen, controleer de API-verwijzing voor een service voordat u deze verificatiemethode gebruikt.

Zowel single service- als multiservice-abonnementssleutels kunnen worden ingewisseld voor verificatietokens. Verificatietokens zijn 10 minuten geldig.

Verificatietokens zijn opgenomen in `Authorization` een aanvraag als de koptekst. De opgegeven tokenwaarde moet `Bearer`worden voorafgegaan `Bearer YOUR_AUTH_TOKEN`door bijvoorbeeld: .

### <a name="sample-requests"></a>Voorbeeldaanvragen

Gebruik deze URL om een abonnementssleutel `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`in te wisselen voor een verificatietoken: .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Deze multi-service regio's ondersteunen token-uitwisseling:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Nadat u een verificatietoken hebt gekregen, moet u deze `Authorization` in elk verzoek als koptekst doorgeven. Dit is een voorbeeldaanroep naar de Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Zie ook

* [Wat zijn cognitieve services?](welcome.md)
* [Prijzen van Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Aangepaste subdomeinen](cognitive-services-custom-subdomains.md)
