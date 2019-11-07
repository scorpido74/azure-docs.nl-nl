---
title: Container opslagplaatsen en installatie kopieën
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Twee tabellen die de container registers, opslag plaatsen en namen van installatie kopieën vertegenwoordigen voor alle cognitieve service aanbiedingen.
ms.service: cognitive-services
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: c2e1707c6d757b2bf568f413ce2ab9f7a28ab6e4
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604443"
---
### <a name="container-repositories-and-images"></a>Container opslagplaatsen en installatie kopieën

In de onderstaande tabellen vindt u een uitgebreid overzicht van de beschik bare container installatie kopieën die door Azure Cognitive Services worden aangeboden.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Openbaar "niet-gegateeerd" (container register: `mcr.microsoft.com`)

De micro soft-Container Registry host alle openbaar beschik bare containers voor Cognitive Services.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sentimentanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Open bare preview-versie van ' gated ' (container Registry: `containerpreview.azurecr.io`)

Het voor beeld-container register host alle openbaar beschik bare ' gated '-containers voor Cognitive Services. Voor deze containers is een formele aanvraag voor toegang vereist om ze te kunnen gebruiken.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Anomaly Detector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Gaat](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulier herkenning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=stt) | Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=tts) | Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=ctts) | Aangepaste tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Translator Text](../../translator/how-to-install-containers.md) | Translator Text | `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` |

