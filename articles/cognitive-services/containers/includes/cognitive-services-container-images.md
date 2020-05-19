---
title: Container opslagplaatsen en installatie kopieën
services: cognitive-services
author: aahill
manager: nitinme
description: Twee tabellen die de container registers, opslag plaatsen en namen van installatie kopieën vertegenwoordigen voor alle cognitieve service aanbiedingen.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: 9025e016725a966061c557f16b610d8897c04c11
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590657"
---
### <a name="container-repositories-and-images"></a>Container opslagplaatsen en installatie kopieën

De onderstaande tabellen geven een lijst van de beschik bare container installatie kopieën die door Azure Cognitive Services worden aangeboden. Zie voor een volledige lijst met alle beschik bare namen van container installatie kopieën en de beschik bare labels [Cognitive Services container-afbeeldings codes](../container-image-tags.md). 

#### <a name="generally-available"></a>Algemeen verkrijgbaar 

De micro soft Container Registry (MCR) syndicateert alle algemeen beschik bare containers voor Cognitive Services. De containers zijn ook rechtstreeks beschikbaar vanuit de [docker-hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

#### <a name="luis"></a>[LUIS](#tab/luis)

| LUIS-container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

Zie [Luis-containers uitvoeren en installeren](../../LUIS/luis-container-howto.md) voor meer informatie.

#### <a name="text-analytics"></a>[Text Analytics](#tab/text-analytics)

| Text Analytics-container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|
| Sentimentanalyse v3 (Engels) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Sentimentanalyse v3 (Spaans) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Sentimentanalyse v3 (Frans) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Sentimentanalyse v3 (Italiaans) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Sentimentanalyse v3 (Duits) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Sentimentanalyse v3 (Chinees-vereenvoudigd) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Sentimentanalyse v3 (Chinees-traditioneel) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Sentimentanalyse v3 (Japans) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Sentimentanalyse v3 (Portugees) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Sentimentanalyse v3 (Nederlands) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

Zie [Text Analytics containers uitvoeren en installeren](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) voor meer informatie.

---

#### <a name="public-ungated-preview-container-registry-mcrmicrosoftcom"></a>Open bare preview-versie (container Registry: `mcr.microsoft.com` )

De volgende preview-containers zijn openbaar beschikbaar. Micro soft Container Registry (MCR) publiceert alle openbaar beschik bare niet-gegateeerde containers voor Cognitive Services. De containers zijn ook rechtstreeks beschikbaar vanuit de [docker-hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Anomalie detectie](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Open bare preview-versie van ' gated ' (container Registry: `containerpreview.azurecr.io` )

De volgende containers voor geteste preview worden gehost in het container voorbeeld register en vereisen een toepassing voor toegang. Zie [Cognitive Services beperking-proces](../../cognitive-services-gating-process.md) voor meer informatie.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulier herkenning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=stt) | Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=tts) | Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=ctts) | Aangepaste tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
