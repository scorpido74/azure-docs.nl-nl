---
title: Containeropslagplaatsen en -afbeeldingen
services: cognitive-services
author: aahill
manager: nitinme
description: Twee tabellen die de containerregisters, opslagplaatsen en afbeeldingsnamen voor alle Cognitive Service-aanbiedingen vertegenwoordigen.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: a854a090af908da691e9b26f5b0714c6560fc0ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876817"
---
### <a name="container-repositories-and-images"></a>Containeropslagplaatsen en -afbeeldingen

De onderstaande tabellen zijn een lijst van de beschikbare containerafbeeldingen die worden aangeboden door Azure Cognitive Services. Zie [Tagingsafbeeldingen](../container-image-tags.md)voor cognitive services voor een volledige lijst met alle beschikbare containerafbeeldingsnamen en de beschikbare tags. Momenteel zijn er geen Cognitive Services-containers die algemeen beschikbaar zijn (GA). Voor het moment, totdat verdere aankondigingen worden gedaan - containers zijn beschikbaar als *ofwel Openbare Ungated* of *Public Gated Preview*.

 - *Openbaar Ungated*: containers zijn openbaar beschikbaar zonder een gating mechanisme.
 - *Openbare Gated Preview*: containers zijn openbaar beschikbaar, maar vereisen eerst een formeel verzoek om toegang te krijgen tot het containerregister.

#### <a name="public-ungated-container-registry-mcrmicrosoftcom"></a>Openbaar "Ungated" (containerregister: `mcr.microsoft.com`)

De Microsoft Container Registry (MCR) syndicaten alle openbaar beschikbare "ungated" containers voor Cognitive Services. De containers zijn ook direct verkrijgbaar vanaf de [Docker hub.](https://hub.docker.com/_/microsoft-azure-cognitive-services)

| Service | Container | Containerregister / Repository / Afbeeldingsnaam |
|--|--|--|
| [LUIS](../../LUIS/luis-container-howto.md) | LUIS | `mcr.microsoft.com/azure-cogni'ive-services/luis` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |
| [Tekstanalyse](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Sentimentanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

#### <a name="public-gated-preview-container-registry-containerpreviewazurecrio"></a>Openbare "Gated" Preview (containerregister: `containerpreview.azurecr.io`)

Het Container Preview-register host alle openbaar beschikbare 'gated' containers voor cognitive services. Deze containers vereisen een formeel verzoek om toegang hen via hun container register.

| Service | Container | Containerregister / Repository / Afbeeldingsnaam |
|--|--|--|
| [Anomaliedetector](../../anomaly-detector/anomaly-detector-container-howto.md) | Anomaly Detector | `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector` |
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Lezen | `containerpreview.azurecr.io/microsoft/cognitive-services-read` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Formulierherkenning](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=stt) | Spraak naar tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=cstt) | Aangepaste spraak-naar-tekst | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=tts) | Tekst naar spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |
| [Speech Service-API](../../speech-service/speech-container-howto.md?tab=ctts) | Aangepaste tekst-naar-spraak | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
