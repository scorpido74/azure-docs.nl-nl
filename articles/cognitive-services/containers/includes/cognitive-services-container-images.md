---
title: Container opslagplaatsen en installatie kopieën
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Twee tabellen die de container registers, opslag plaatsen en namen van installatie kopieën vertegenwoordigen voor alle cognitieve service aanbiedingen.
ms.service: cognitive-services
ms.topic: include
ms.date: 09/06/2019
ms.author: dapine
ms.openlocfilehash: 181f4acd86dfacb15592ded6f2df3287e3dc13bf
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130147"
---
### <a name="container-repositories-and-images"></a>Container opslagplaatsen en installatie kopieën

In de onderstaande tabellen vindt u een uitgebreid overzicht van de beschik bare container installatie kopieën die door Azure Cognitive Services worden aangeboden.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Openbaar "niet-degateeerd" (container `mcr.microsoft.com`register:)

De micro soft-Container Registry host alle openbaar beschik bare containers voor Cognitive Services.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sentimentanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Open bare preview-versie van ' gated ' `containerpreview.azurecr.io`(container Registry:)

Het voor beeld-container register host alle openbaar beschik bare ' gated '-containers voor Cognitive Services. Voor deze containers is een formele aanvraag voor toegang vereist om ze te kunnen gebruiken.

| Service | Container | Container Registry/opslagplaats/naam van installatie kopie |
|--|--|--|
| [Anomalie detectie](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Tekst herkennen | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulier herkenning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service-API](../../speech-service/speech-container-howto.md) | Spraak-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md) | Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
