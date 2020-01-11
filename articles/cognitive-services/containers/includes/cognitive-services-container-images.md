---
title: Container opslagplaatsen en installatie kopieën
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Twee tabellen die de container registers, opslag plaatsen en namen van installatie kopieën vertegenwoordigen voor alle cognitieve service aanbiedingen.
ms.service: cognitive-services
ms.topic: include
ms.date: 01/10/2020
ms.author: dapine
ms.openlocfilehash: 7c229ea306d89f85fb37a68fc84e3e4f6770e5ad
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75866941"
---
### <a name="container-repositories-and-images"></a>Container opslagplaatsen en installatie kopieën

De onderstaande tabellen geven een lijst van de beschik bare container installatie kopieën die door Azure Cognitive Services worden aangeboden. Zie voor een volledige lijst met alle beschik bare namen van container installatie kopieën en de beschik bare labels [Cognitive Services container-afbeeldings codes](../container-image-tags.md). Er zijn momenteel geen Cognitive Services containers die algemeen beschikbaar zijn (GA). Voor de tijd dat u verdere aankondigingen hebt gemaakt--containers zijn beschikbaar als preview-versie van *open bare* of *open bare gated*.

 - *Openbaar niet-gegate*: containers zijn openbaar zonder beperking-mechanisme.
 - *Public gated preview*: containers zijn openbaar beschikbaar, maar eerst een formele aanvraag voor toegang tot het container register.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Openbaar "niet-gegateeerd" (container register: `mcr.microsoft.com`)

Met de micro soft-Container Registry (MCR) worden alle openbaar beschik bare ' niet-gegateeerde ' containers gepubliceerd voor Cognitive Services. De containers zijn ook rechtstreeks beschikbaar vanuit de [docker-hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sentimentanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Open bare preview-versie van ' gated ' (container Registry: `containerpreview.azurecr.io`)

Het voor beeld-container register host alle openbaar beschik bare ' gated '-containers voor Cognitive Services. Deze containers vereisen een formeel verzoek om toegang te krijgen tot deze via hun container register.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Anomalie detectie](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulier herkenning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=stt) | Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=cstt) | Custom Speech-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=tts) | Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=ctts) | Aangepaste tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
