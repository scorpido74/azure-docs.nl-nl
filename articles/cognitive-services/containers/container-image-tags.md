---
title: Tags voor Cognitive Services-containerinstallatiekopie
titleSuffix: Azure Cognitive Services
description: Een uitgebreide lijst van alle tagen van de Cognitive Service-containerafbeeldingen.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: reference
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: eed2223dbfeee307b552cdd010530f27c379f5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219443"
---
# <a name="azure-cognitive-services-container-image-tags"></a>Image-imagetags voor Azure Cognitive Services-container

Azure Cognitive Services biedt veel containerafbeeldingen. De containerregisters en bijbehorende opslagplaatsen variëren tussen containerafbeeldingen. Elke naam van de containerafbeelding biedt meerdere tags. Een containerafbeeldingstag is een mechanisme voor het versien van de containerafbeelding. Dit artikel is bedoeld om te worden gebruikt als een uitgebreide referentie voor het vermelden van alle Cognitive Services container afbeeldingen en hun beschikbare tags.

> [!TIP]
> Let [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/)bij gebruik goed op de behuizing van het containerregister, de opslagplaats, de naam van de containerafbeelding en de bijbehorende tag - omdat ze **hoofdlettergevoelig**zijn .

## <a name="anomaly-detector"></a>Anomaly Detector

De [anomaliedetectorcontainerafbeelding][ad-containers] is te `containerpreview.azurecr.io` vinden in het containerregister. Het bevindt `microsoft` zich in `cognitive-services-anomaly-detector`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-anomaly-detector`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008500001-amd64-preview` |       |

## <a name="computer-vision"></a>Computer Vision

De [containerafbeelding van Computer][cv-containers] Vision `containerpreview.azurecr.io` is te vinden in het containerregister. Het bevindt `microsoft` zich in `cognitive-services-read`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-read`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.011580001-amd64-preview` |       |
| `1.1.009920003-amd64-preview` |       |
| `1.1.009910003-amd64-preview` |       |

## <a name="face"></a>Face

De afbeelding van de [Face-container][fa-containers] is te vinden in het `containerpreview.azurecr.io` containerregister. Het bevindt `microsoft` zich in `cognitive-services-face`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-face`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |
| `1.1.006490002-amd64-preview` |       |
| `1.0.005940002-amd64-preview` |       |
| `1.0.005550001-amd64-preview` |       |

## <a name="form-recognizer"></a>Form Recognizer

De [containerafbeelding van Form Recognizer][fr-containers] is te vinden in het `containerpreview.azurecr.io` containerregister. Het bevindt `microsoft` zich in `cognitive-services-form-recognizer`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008640001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |

## <a name="language-understanding-luis"></a>Language Understanding (LUIS)

De [LUIS][lu-containers] container afbeelding is `mcr.microsoft.com` te vinden op de container register syndicaat. Het bevindt `azure-cognitive-services` zich in `luis`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `mcr.microsoft.com/azure-cognitive-services/luis`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.010330004-amd64-preview` |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008710001-amd64-preview` |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.008010002-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.007020001-amd64-preview` |       |

## <a name="custom-speech-to-text"></a>Aangepaste spraak-naar-tekst

De afbeelding van de [aangepaste spraak-naar-tekstcontainer][sp-cstt] is te vinden in het `containerpreview.azurecr.io` containerregister. Het bevindt `microsoft` zich in `cognitive-services-custom-speech-to-text`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags            | Opmerkingen |
|-----------------------|:------|
| `latest`              |       |
| `2.1.1-amd64-preview` |       |
| `2.1.0-amd64-preview` |       |
| `2.0.2-amd64-preview` |       |
| `2.0.0-amd64-preview` |       |

## <a name="custom-text-to-speech"></a>Aangepaste tekst-naar-spraak

De [aangepaste tekst-naar-spraak][sp-ctts] container afbeelding `containerpreview.azurecr.io` is te vinden in het containerregister. Het bevindt `microsoft` zich in `cognitive-services-custom-text-to-speech`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags            | Opmerkingen |
|-----------------------|:------|
| `latest`              |       |
| `1.3.0-amd64-preview` |       |

## <a name="speech-to-text"></a>Spraak naar tekst

De [spraak-naar-tekst][sp-stt] container afbeelding is `containerpreview.azurecr.io` te vinden in het containerregister. Het bevindt `microsoft` zich in `cognitive-services-speech-to-text`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                  | Opmerkingen                                    |
|-----------------------------|:-----------------------------------------|
| `latest`                    | Containerafbeelding met `en-US` de landint. |
| `2.1.1-amd64-ar-ae-preview` | Containerafbeelding met `ar-AE` de landint. |
| `2.1.1-amd64-ar-eg-preview` | Containerafbeelding met `ar-EG` de landint. |
| `2.1.1-amd64-ar-kw-preview` | Containerafbeelding met `ar-KW` de landint. |
| `2.1.1-amd64-ar-qa-preview` | Containerafbeelding met `ar-QA` de landint. |
| `2.1.1-amd64-ar-sa-preview` | Containerafbeelding met `ar-SA` de landint. |
| `2.1.1-amd64-ca-es-preview` | Containerafbeelding met `ca-ES` de landint. |
| `2.1.1-amd64-da-dk-preview` | Containerafbeelding met `da-DK` de landint. |
| `2.1.1-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `2.1.1-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `2.1.1-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `2.1.1-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `2.1.1-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `2.1.1-amd64-en-nz-preview` | Containerafbeelding met `en-NZ` de landint. |
| `2.1.1-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `2.1.1-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `2.1.1-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `2.1.1-amd64-fi-fi-preview` | Containerafbeelding met `fi-FI` de landint. |
| `2.1.1-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `2.1.1-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `2.1.1-amd64-gu-in-preview` | Containerafbeelding met `gu-IN` de landint. |
| `2.1.1-amd64-hi-in-preview` | Containerafbeelding met `hi-IN` de landint. |
| `2.1.1-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `2.1.1-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `2.1.1-amd64-ko-kr-preview` | Containerafbeelding met `ko-KR` de landint. |
| `2.1.1-amd64-mr-in-preview` | Containerafbeelding met `mr-IN` de landint. |
| `2.1.1-amd64-nb-no-preview` | Containerafbeelding met `nb-NO` de landint. |
| `2.1.1-amd64-nl-nl-preview` | Containerafbeelding met `nl-NL` de landint. |
| `2.1.1-amd64-pl-pl-preview` | Containerafbeelding met `pl-PL` de landint. |
| `2.1.1-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `2.1.1-amd64-pt-pt-preview` | Containerafbeelding met `pt-PT` de landint. |
| `2.1.1-amd64-ru-ru-preview` | Containerafbeelding met `ru-RU` de landint. |
| `2.1.1-amd64-sv-se-preview` | Containerafbeelding met `sv-SE` de landint. |
| `2.1.1-amd64-ta-in-preview` | Containerafbeelding met `ta-IN` de landint. |
| `2.1.1-amd64-te-in-preview` | Containerafbeelding met `te-IN` de landint. |
| `2.1.1-amd64-th-th-preview` | Containerafbeelding met `th-TH` de landint. |
| `2.1.1-amd64-tr-tr-preview` | Containerafbeelding met `tr-TR` de landint. |
| `2.1.1-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `2.1.1-amd64-zh-hk-preview` | Containerafbeelding met `zh-HK` de landint. |
| `2.1.1-amd64-zh-tw-preview` | Containerafbeelding met `zh-TW` de landint. |
| `2.1.0-amd64-ar-ae-preview` | Containerafbeelding met `ar-AE` de landint. |
| `2.1.0-amd64-ar-eg-preview` | Containerafbeelding met `ar-EG` de landint. |
| `2.1.0-amd64-ar-kw-preview` | Containerafbeelding met `ar-KW` de landint. |
| `2.1.0-amd64-ar-qa-preview` | Containerafbeelding met `ar-QA` de landint. |
| `2.1.0-amd64-ar-sa-preview` | Containerafbeelding met `ar-SA` de landint. |
| `2.1.0-amd64-ca-es-preview` | Containerafbeelding met `ca-ES` de landint. |
| `2.1.0-amd64-da-dk-preview` | Containerafbeelding met `da-DK` de landint. |
| `2.1.0-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `2.1.0-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `2.1.0-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `2.1.0-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `2.1.0-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `2.1.0-amd64-en-nz-preview` | Containerafbeelding met `en-NZ` de landint. |
| `2.1.0-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `2.1.0-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `2.1.0-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `2.1.0-amd64-fi-fi-preview` | Containerafbeelding met `fi-FI` de landint. |
| `2.1.0-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `2.1.0-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `2.1.0-amd64-gu-in-preview` | Containerafbeelding met `gu-IN` de landint. |
| `2.1.0-amd64-hi-in-preview` | Containerafbeelding met `hi-IN` de landint. |
| `2.1.0-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `2.1.0-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `2.1.0-amd64-ko-kr-preview` | Containerafbeelding met `ko-KR` de landint. |
| `2.1.0-amd64-mr-in-preview` | Containerafbeelding met `mr-IN` de landint. |
| `2.1.0-amd64-nb-no-preview` | Containerafbeelding met `nb-NO` de landint. |
| `2.1.0-amd64-nl-nl-preview` | Containerafbeelding met `nl-NL` de landint. |
| `2.1.0-amd64-pl-pl-preview` | Containerafbeelding met `pl-PL` de landint. |
| `2.1.0-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `2.1.0-amd64-pt-pt-preview` | Containerafbeelding met `pt-PT` de landint. |
| `2.1.0-amd64-ru-ru-preview` | Containerafbeelding met `ru-RU` de landint. |
| `2.1.0-amd64-sv-se-preview` | Containerafbeelding met `sv-SE` de landint. |
| `2.1.0-amd64-ta-in-preview` | Containerafbeelding met `ta-IN` de landint. |
| `2.1.0-amd64-te-in-preview` | Containerafbeelding met `te-IN` de landint. |
| `2.1.0-amd64-th-th-preview` | Containerafbeelding met `th-TH` de landint. |
| `2.1.0-amd64-tr-tr-preview` | Containerafbeelding met `tr-TR` de landint. |
| `2.1.0-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `2.1.0-amd64-zh-hk-preview` | Containerafbeelding met `zh-HK` de landint. |
| `2.1.0-amd64-zh-tw-preview` | Containerafbeelding met `zh-TW` de landint. |
| `2.0.3-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `2.0.2-amd64-ar-ae-preview` | Containerafbeelding met `ar-AE` de landint. |
| `2.0.2-amd64-ar-eg-preview` | Containerafbeelding met `ar-EG` de landint. |
| `2.0.2-amd64-ar-kw-preview` | Containerafbeelding met `ar-KW` de landint. |
| `2.0.2-amd64-ar-qa-preview` | Containerafbeelding met `ar-QA` de landint. |
| `2.0.2-amd64-ar-sa-preview` | Containerafbeelding met `ar-SA` de landint. |
| `2.0.2-amd64-ca-es-preview` | Containerafbeelding met `ca-ES` de landint. |
| `2.0.2-amd64-da-dk-preview` | Containerafbeelding met `da-DK` de landint. |
| `2.0.2-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `2.0.2-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `2.0.2-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `2.0.2-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `2.0.2-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `2.0.2-amd64-en-nz-preview` | Containerafbeelding met `en-NZ` de landint. |
| `2.0.2-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `2.0.2-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `2.0.2-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `2.0.2-amd64-fi-fi-preview` | Containerafbeelding met `fi-FI` de landint. |
| `2.0.2-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `2.0.2-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `2.0.2-amd64-gu-in-preview` | Containerafbeelding met `gu-IN` de landint. |
| `2.0.2-amd64-hi-in-preview` | Containerafbeelding met `hi-IN` de landint. |
| `2.0.2-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `2.0.2-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `2.0.2-amd64-ko-kr-preview` | Containerafbeelding met `ko-KR` de landint. |
| `2.0.2-amd64-mr-in-preview` | Containerafbeelding met `mr-IN` de landint. |
| `2.0.2-amd64-nb-no-preview` | Containerafbeelding met `nb-NO` de landint. |
| `2.0.2-amd64-nl-nl-preview` | Containerafbeelding met `nl-NL` de landint. |
| `2.0.2-amd64-pl-pl-preview` | Containerafbeelding met `pl-PL` de landint. |
| `2.0.2-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `2.0.2-amd64-pt-pt-preview` | Containerafbeelding met `pt-PT` de landint. |
| `2.0.2-amd64-ru-ru-preview` | Containerafbeelding met `ru-RU` de landint. |
| `2.0.2-amd64-sv-se-preview` | Containerafbeelding met `sv-SE` de landint. |
| `2.0.2-amd64-ta-in-preview` | Containerafbeelding met `ta-IN` de landint. |
| `2.0.2-amd64-te-in-preview` | Containerafbeelding met `te-IN` de landint. |
| `2.0.2-amd64-th-th-preview` | Containerafbeelding met `th-TH` de landint. |
| `2.0.2-amd64-tr-tr-preview` | Containerafbeelding met `tr-TR` de landint. |
| `2.0.2-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `2.0.2-amd64-zh-hk-preview` | Containerafbeelding met `zh-HK` de landint. |
| `2.0.2-amd64-zh-tw-preview` | Containerafbeelding met `zh-TW` de landint. |
| `2.0.1-amd64-ar-ae-preview` | Containerafbeelding met `ar-AE` de landint. |
| `2.0.1-amd64-ar-eg-preview` | Containerafbeelding met `ar-EG` de landint. |
| `2.0.1-amd64-ar-kw-preview` | Containerafbeelding met `ar-KW` de landint. |
| `2.0.1-amd64-ar-qa-preview` | Containerafbeelding met `ar-QA` de landint. |
| `2.0.1-amd64-ar-sa-preview` | Containerafbeelding met `ar-SA` de landint. |
| `2.0.1-amd64-ca-es-preview` | Containerafbeelding met `ca-ES` de landint. |
| `2.0.1-amd64-da-dk-preview` | Containerafbeelding met `da-DK` de landint. |
| `2.0.1-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `2.0.1-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `2.0.1-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `2.0.1-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `2.0.1-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `2.0.1-amd64-en-nz-preview` | Containerafbeelding met `en-NZ` de landint. |
| `2.0.1-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `2.0.1-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `2.0.1-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `2.0.1-amd64-fi-fi-preview` | Containerafbeelding met `fi-FI` de landint. |
| `2.0.1-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `2.0.1-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `2.0.1-amd64-gu-in-preview` | Containerafbeelding met `gu-IN` de landint. |
| `2.0.1-amd64-hi-in-preview` | Containerafbeelding met `hi-IN` de landint. |
| `2.0.1-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `2.0.1-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `2.0.1-amd64-ko-kr-preview` | Containerafbeelding met `ko-KR` de landint. |
| `2.0.1-amd64-mr-in-preview` | Containerafbeelding met `mr-IN` de landint. |
| `2.0.1-amd64-nb-no-preview` | Containerafbeelding met `nb-NO` de landint. |
| `2.0.1-amd64-nl-nl-preview` | Containerafbeelding met `nl-NL` de landint. |
| `2.0.1-amd64-pl-pl-preview` | Containerafbeelding met `pl-PL` de landint. |
| `2.0.1-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `2.0.1-amd64-pt-pt-preview` | Containerafbeelding met `pt-PT` de landint. |
| `2.0.1-amd64-ru-ru-preview` | Containerafbeelding met `ru-RU` de landint. |
| `2.0.1-amd64-sv-se-preview` | Containerafbeelding met `sv-SE` de landint. |
| `2.0.1-amd64-ta-in-preview` | Containerafbeelding met `ta-IN` de landint. |
| `2.0.1-amd64-te-in-preview` | Containerafbeelding met `te-IN` de landint. |
| `2.0.1-amd64-th-th-preview` | Containerafbeelding met `th-TH` de landint. |
| `2.0.1-amd64-tr-tr-preview` | Containerafbeelding met `tr-TR` de landint. |
| `2.0.1-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `2.0.1-amd64-zh-hk-preview` | Containerafbeelding met `zh-HK` de landint. |
| `2.0.1-amd64-zh-tw-preview` | Containerafbeelding met `zh-TW` de landint. |
| `2.0.0-amd64-ar-eg-preview` | Containerafbeelding met `ar-EG` de landint. |
| `2.0.0-amd64-ca-es-preview` | Containerafbeelding met `ca-ES` de landint. |
| `2.0.0-amd64-da-dk-preview` | Containerafbeelding met `da-DK` de landint. |
| `2.0.0-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `2.0.0-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `2.0.0-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `2.0.0-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `2.0.0-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `2.0.0-amd64-en-nz-preview` | Containerafbeelding met `en-NZ` de landint. |
| `2.0.0-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `2.0.0-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `2.0.0-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `2.0.0-amd64-fi-fi-preview` | Containerafbeelding met `fi-FI` de landint. |
| `2.0.0-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `2.0.0-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `2.0.0-amd64-hi-in-preview` | Containerafbeelding met `hi-IN` de landint. |
| `2.0.0-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `2.0.0-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `2.0.0-amd64-ko-kr-preview` | Containerafbeelding met `ko-KR` de landint. |
| `2.0.0-amd64-nb-no-preview` | Containerafbeelding met `nb-NO` de landint. |
| `2.0.0-amd64-nl-nl-preview` | Containerafbeelding met `nl-NL` de landint. |
| `2.0.0-amd64-pl-pl-preview` | Containerafbeelding met `pl-PL` de landint. |
| `2.0.0-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `2.0.0-amd64-pt-pt-preview` | Containerafbeelding met `pt-PT` de landint. |
| `2.0.0-amd64-ru-ru-preview` | Containerafbeelding met `ru-RU` de landint. |
| `2.0.0-amd64-sv-se-preview` | Containerafbeelding met `sv-SE` de landint. |
| `2.0.0-amd64-th-th-preview` | Containerafbeelding met `th-TH` de landint. |
| `2.0.0-amd64-tr-tr-preview` | Containerafbeelding met `tr-TR` de landint. |
| `2.0.0-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `2.0.0-amd64-zh-hk-preview` | Containerafbeelding met `zh-HK` de landint. |
| `2.0.0-amd64-zh-tw-preview` | Containerafbeelding met `zh-TW` de landint. |
| `1.2.0-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `1.2.0-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `1.2.0-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `1.2.0-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `1.2.0-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `1.2.0-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `1.2.0-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `1.2.0-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `1.2.0-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `1.2.0-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `1.2.0-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `1.2.0-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `1.2.0-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `1.2.0-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `1.1.3-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `1.1.3-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `1.1.3-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `1.1.3-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `1.1.3-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `1.1.3-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `1.1.3-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `1.1.3-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `1.1.3-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `1.1.3-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `1.1.3-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `1.1.3-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `1.1.3-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `1.1.3-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `1.1.2-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `1.1.2-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `1.1.2-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `1.1.2-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `1.1.2-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `1.1.2-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `1.1.2-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `1.1.2-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `1.1.2-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `1.1.2-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `1.1.2-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `1.1.2-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `1.1.2-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `1.1.2-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `1.1.1-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `1.1.1-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `1.1.1-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `1.1.1-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `1.1.1-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `1.1.1-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `1.1.1-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `1.1.1-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `1.1.1-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `1.1.1-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `1.1.1-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `1.1.1-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `1.1.1-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `1.1.1-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `1.1.0-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `1.1.0-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `1.1.0-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `1.1.0-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `1.1.0-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `1.1.0-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `1.1.0-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `1.1.0-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `1.1.0-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `1.1.0-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `1.1.0-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `1.1.0-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `1.1.0-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `1.1.0-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |
| `1.0.0-amd64-de-de-preview` | Containerafbeelding met `de-DE` de landint. |
| `1.0.0-amd64-en-au-preview` | Containerafbeelding met `en-AU` de landint. |
| `1.0.0-amd64-en-ca-preview` | Containerafbeelding met `en-CA` de landint. |
| `1.0.0-amd64-en-gb-preview` | Containerafbeelding met `en-GB` de landint. |
| `1.0.0-amd64-en-in-preview` | Containerafbeelding met `en-IN` de landint. |
| `1.0.0-amd64-en-us-preview` | Containerafbeelding met `en-US` de landint. |
| `1.0.0-amd64-es-es-preview` | Containerafbeelding met `es-ES` de landint. |
| `1.0.0-amd64-es-mx-preview` | Containerafbeelding met `es-MX` de landint. |
| `1.0.0-amd64-fr-ca-preview` | Containerafbeelding met `fr-CA` de landint. |
| `1.0.0-amd64-fr-fr-preview` | Containerafbeelding met `fr-FR` de landint. |
| `1.0.0-amd64-it-it-preview` | Containerafbeelding met `it-IT` de landint. |
| `1.0.0-amd64-ja-jp-preview` | Containerafbeelding met `ja-JP` de landint. |
| `1.0.0-amd64-pt-br-preview` | Containerafbeelding met `pt-BR` de landint. |
| `1.0.0-amd64-zh-cn-preview` | Containerafbeelding met `zh-CN` de landint. |

## <a name="text-to-speech"></a>Tekst naar spraak

De [tekst-naar-spraak][sp-tts] container afbeelding is `containerpreview.azurecr.io` te vinden in het containerregister. Het bevindt `microsoft` zich in `cognitive-services-text-to-speech`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                                  | Opmerkingen                                                                      |
|---------------------------------------------|:---------------------------------------------------------------------------|
| `latest`                                    | Containerafbeelding met `en-US` de `en-US-JessaRUS` land- en stem.        |
| `1.3.0-amd64-ar-eg-hoda-preview`            | Containerafbeelding met `ar-EG` de `ar-EG-Hoda` land- en stem.            |
| `1.3.0-amd64-ar-sa-naayf-preview`           | Containerafbeelding met `ar-SA` de `ar-SA-Naayf` land- en stem.           |
| `1.3.0-amd64-bg-bg-ivan-preview`            | Containerafbeelding met `bg-BG` de `bg-BG-Ivan` land- en stem.            |
| `1.3.0-amd64-ca-es-herenarus-preview`       | Containerafbeelding met `ca-ES` de `ca-ES-HerenaRUS` land- en stem.       |
| `1.3.0-amd64-cs-cz-jakub-preview`           | Containerafbeelding met `cs-CZ` de `cs-CZ-Jakub` land- en stem.           |
| `1.3.0-amd64-da-dk-hellerus-preview`        | Containerafbeelding met `da-DK` de `da-DK-HelleRUS` land- en stem.        |
| `1.3.0-amd64-de-at-michael-preview`         | Containerafbeelding met `de-AT` de `de-AT-Michael` land- en stem.         |
| `1.3.0-amd64-de-ch-karsten-preview`         | Containerafbeelding met `de-CH` de `de-CH-Karsten` land- en stem.         |
| `1.3.0-amd64-de-de-hedda-preview`           | Containerafbeelding met `de-DE` de `de-DE-Hedda` land- en stem.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Containerafbeelding met `de-DE` de `de-DE-Hedda` land- en stem.           |
| `1.3.0-amd64-de-de-heddarus-preview`        | Containerafbeelding met `de-DE` de `de-DE-HeddaRUS` land- en stem.        |
| `1.3.0-amd64-de-de-stefan-apollo-preview`   | Containerafbeelding met `de-DE` de `de-DE-Stefan-Apollo` land- en stem.   |
| `1.3.0-amd64-el-gr-stefanos-preview`        | Containerafbeelding met `el-GR` de `el-GR-Stefanos` land- en stem.        |
| `1.3.0-amd64-en-au-catherine-preview`       | Containerafbeelding met `en-AU` de `en-AU-Catherine` land- en stem.       |
| `1.3.0-amd64-en-au-hayleyrus-preview`       | Containerafbeelding met `en-AU` de `en-AU-HayleyRUS` land- en stem.       |
| `1.3.0-amd64-en-ca-heatherrus-preview`      | Containerafbeelding met `en-CA` de `en-CA-HeatherRUS` land- en stem.      |
| `1.3.0-amd64-en-ca-linda-preview`           | Containerafbeelding met `en-CA` de `en-CA-Linda` land- en stem.           |
| `1.3.0-amd64-en-gb-george-apollo-preview`   | Containerafbeelding met `en-GB` de `en-GB-George-Apollo` land- en stem.   |
| `1.3.0-amd64-en-gb-hazelrus-preview`        | Containerafbeelding met `en-GB` de `en-GB-HazelRUS` land- en stem.        |
| `1.3.0-amd64-en-gb-susan-apollo-preview`    | Containerafbeelding met `en-GB` de `en-GB-Susan-Apollo` land- en stem.    |
| `1.3.0-amd64-en-ie-sean-preview`            | Containerafbeelding met `en-IE` de `en-IE-Sean` land- en stem.            |
| `1.3.0-amd64-en-in-heera-apollo-preview`    | Containerafbeelding met `en-IN` de `en-IN-Heera-Apollo` land- en stem.    |
| `1.3.0-amd64-en-in-priyarus-preview`        | Containerafbeelding met `en-IN` de `en-IN-PriyaRUS` land- en stem.        |
| `1.3.0-amd64-en-in-ravi-apollo-preview`     | Containerafbeelding met `en-IN` de `en-IN-Ravi-Apollo` land- en stem.     |
| `1.3.0-amd64-en-us-benjaminrus-preview`     | Containerafbeelding met `en-US` de `en-US-BenjaminRUS` land- en stem.     |
| `1.3.0-amd64-en-us-guy24krus-preview`       | Containerafbeelding met `en-US` de `en-US-Guy24kRUS` land- en stem.       |
| `1.3.0-amd64-en-us-jessa24krus-preview`     | Containerafbeelding met `en-US` de `en-US-Jessa24kRUS` land- en stem.     |
| `1.3.0-amd64-en-us-jessarus-preview`        | Containerafbeelding met `en-US` de `en-US-JessaRUS` land- en stem.        |
| `1.3.0-amd64-en-us-zirarus-preview`         | Containerafbeelding met `en-US` de `en-US-ZiraRUS` land- en stem.         |
| `1.3.0-amd64-es-es-helenarus-preview`       | Containerafbeelding met `es-ES` de `es-ES-HelenaRUS` land- en stem.       |
| `1.3.0-amd64-es-es-laura-apollo-preview`    | Containerafbeelding met `es-ES` de `es-ES-Laura-Apollo` land- en stem.    |
| `1.3.0-amd64-es-es-pablo-apollo-preview`    | Containerafbeelding met `es-ES` de `es-ES-Pablo-Apollo` land- en stem.    |
| `1.3.0-amd64-es-mx-hildarus-preview`        | Containerafbeelding met `es-MX` de `es-MX-HildaRUS` land- en stem.        |
| `1.3.0-amd64-es-mx-raul-apollo-preview`     | Containerafbeelding met `es-MX` de `es-MX-Raul-Apollo` land- en stem.     |
| `1.3.0-amd64-fi-fi-heidirus-preview`        | Containerafbeelding met `fi-FI` de `fi-FI-HeidiRUS` land- en stem.        |
| `1.3.0-amd64-fr-ca-caroline-preview`        | Containerafbeelding met `fr-CA` de `fr-CA-Caroline` land- en stem.        |
| `1.3.0-amd64-fr-ca-harmonierus-preview`     | Containerafbeelding met `fr-CA` de `fr-CA-HarmonieRUS` land- en stem.     |
| `1.3.0-amd64-fr-ch-guillaume-preview`       | Containerafbeelding met `fr-CH` de `fr-CH-Guillaume` land- en stem.       |
| `1.3.0-amd64-fr-fr-hortenserus-preview`     | Containerafbeelding met `fr-FR` de `fr-FR-HortenseRUS` land- en stem.     |
| `1.3.0-amd64-fr-fr-julie-apollo-preview`    | Containerafbeelding met `fr-FR` de `fr-FR-Julie-Apollo` land- en stem.    |
| `1.3.0-amd64-fr-fr-paul-apollo-preview`     | Containerafbeelding met `fr-FR` de `fr-FR-Paul-Apollo` land- en stem.     |
| `1.3.0-amd64-he-il-asaf-preview`            | Containerafbeelding met `he-IL` de `he-IL-Asaf` land- en stem.            |
| `1.3.0-amd64-hi-in-hemant-preview`          | Containerafbeelding met `hi-IN` de `hi-IN-Hemant` land- en stem.          |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Containerafbeelding met `hi-IN` de `hi-IN-Kalpana-Apollo` land- en stem.  |
| `1.3.0-amd64-hi-in-kalpana-apollo-preview`  | Containerafbeelding met `hi-IN` de `hi-IN-Kalpana` land- en stem.         |
| `1.3.0-amd64-hi-in-kalpana-preview`         | Containerafbeelding met `hi-IN` de `hi-IN-Kalpana` land- en stem.         |
| `1.3.0-amd64-hr-hr-matej-preview`           | Containerafbeelding met `hr-HR` de `hr-HR-Matej` land- en stem.           |
| `1.3.0-amd64-hu-hu-szabolcs-preview`        | Containerafbeelding met `hu-HU` de `hu-HU-Szabolcs` land- en stem.        |
| `1.3.0-amd64-id-id-andika-preview`          | Containerafbeelding met `id-ID` de `id-ID-Andika` land- en stem.          |
| `1.3.0-amd64-it-it-cosimo-apollo-preview`   | Containerafbeelding met `it-IT` de `it-IT-Cosimo-Apollo` land- en stem.   |
| `1.3.0-amd64-it-it-luciarus-preview`        | Containerafbeelding met `it-IT` de `it-IT-LuciaRUS` land- en stem.        |
| `1.3.0-amd64-ja-jp-ayumi-apollo-preview`    | Containerafbeelding met `ja-JP` de `ja-JP-Ayumi-Apollo` land- en stem.    |
| `1.3.0-amd64-ja-jp-harukarus-preview`       | Containerafbeelding met `ja-JP` de `ja-JP-HarukaRUS` land- en stem.       |
| `1.3.0-amd64-ja-jp-ichiro-apollo-preview`   | Containerafbeelding met `ja-JP` de `ja-JP-Ichiro-Apollo` land- en stem.   |
| `1.3.0-amd64-ko-kr-heamirus-preview`        | Containerafbeelding met `ko-KR` de `ko-KR-HeamiRUS` land- en stem.        |
| `1.3.0-amd64-ms-my-rizwan-preview`          | Containerafbeelding met `ms-MY` de `ms-MY-Rizwan` land- en stem.          |
| `1.3.0-amd64-nb-no-huldarus-preview`        | Containerafbeelding met `nb-NO` de `nb-NO-HuldaRUS` land- en stem.        |
| `1.3.0-amd64-nl-nl-hannarus-preview`        | Containerafbeelding met `nl-NL` de `nl-NL-HannaRUS` land- en stem.        |
| `1.3.0-amd64-pl-pl-paulinarus-preview`      | Containerafbeelding met `pl-PL` de `pl-PL-PaulinaRUS` land- en stem.      |
| `1.3.0-amd64-pt-br-daniel-apollo-preview`   | Containerafbeelding met `pt-BR` de `pt-BR-Daniel-Apollo` land- en stem.   |
| `1.3.0-amd64-pt-br-heloisarus-preview`      | Containerafbeelding met `pt-BR` de `pt-BR-HeloisaRUS` land- en stem.      |
| `1.3.0-amd64-pt-pt-heliarus-preview`        | Containerafbeelding met `pt-PT` de `pt-PT-HeliaRUS` land- en stem.        |
| `1.3.0-amd64-ro-ro-andrei-preview`          | Containerafbeelding met `ro-RO` de `ro-RO-Andrei` land- en stem.          |
| `1.3.0-amd64-ru-ru-ekaterinarus-preview`    | Containerafbeelding met `ru-RU` de `ru-RU-EkaterinaRUS` land- en stem.    |
| `1.3.0-amd64-ru-ru-irina-apollo-preview`    | Containerafbeelding met `ru-RU` de `ru-RU-Irina-Apollo` land- en stem.    |
| `1.3.0-amd64-ru-ru-pavel-apollo-preview`    | Containerafbeelding met `ru-RU` de `ru-RU-Pavel-Apollo` land- en stem.    |
| `1.3.0-amd64-sk-sk-filip-preview`           | Containerafbeelding met `sk-SK` de `sk-SK-Filip` land- en stem.           |
| `1.3.0-amd64-sl-si-lado-preview`            | Containerafbeelding met `sl-SI` de `sl-SI-Lado` land- en stem.            |
| `1.3.0-amd64-sv-se-hedvigrus-preview`       | Containerafbeelding met `sv-SE` de `sv-SE-HedvigRUS` land- en stem.       |
| `1.3.0-amd64-ta-in-valluvar-preview`        | Containerafbeelding met `ta-IN` de `ta-IN-Valluvar` land- en stem.        |
| `1.3.0-amd64-te-in-chitra-preview`          | Containerafbeelding met `te-IN` de `te-IN-Chitra` land- en stem.          |
| `1.3.0-amd64-th-th-pattara-preview`         | Containerafbeelding met `th-TH` de `th-TH-Pattara` land- en stem.         |
| `1.3.0-amd64-tr-tr-sedarus-preview`         | Containerafbeelding met `tr-TR` de `tr-TR-SedaRUS` land- en stem.         |
| `1.3.0-amd64-vi-vn-an-preview`              | Containerafbeelding met `vi-VN` de `vi-VN-An` land- en stem.              |
| `1.3.0-amd64-zh-cn-huihuirus-preview`       | Containerafbeelding met `zh-CN` de `zh-CN-HuihuiRUS` land- en stem.       |
| `1.3.0-amd64-zh-cn-kangkang-apollo-preview` | Containerafbeelding met `zh-CN` de `zh-CN-Kangkang-Apollo` land- en stem. |
| `1.3.0-amd64-zh-cn-yaoyao-apollo-preview`   | Containerafbeelding met `zh-CN` de `zh-CN-Yaoyao-Apollo` land- en stem.   |
| `1.3.0-amd64-zh-hk-danny-apollo-preview`    | Containerafbeelding met `zh-HK` de `zh-HK-Danny-Apollo` land- en stem.    |
| `1.3.0-amd64-zh-hk-tracy-apollo-preview`    | Containerafbeelding met `zh-HK` de `zh-HK-Tracy-Apollo` land- en stem.    |
| `1.3.0-amd64-zh-hk-tracyrus-preview`        | Containerafbeelding met `zh-HK` de `zh-HK-TracyRUS` land- en stem.        |
| `1.3.0-amd64-zh-tw-hanhanrus-preview`       | Containerafbeelding met `zh-TW` de `zh-TW-HanHanRUS` land- en stem.       |
| `1.3.0-amd64-zh-tw-yating-apollo-preview`   | Containerafbeelding met `zh-TW` de `zh-TW-Yating-Apollo` land- en stem.   |
| `1.3.0-amd64-zh-tw-zhiwei-apollo-preview`   | Containerafbeelding met `zh-TW` de `zh-TW-Zhiwei-Apollo` land- en stem.   |
| `1.2.0-amd64-de-de-heddarus-preview`        | Containerafbeelding met `de-DE` de `de-DE-Hedda` land- en stem.           |
| `1.2.0-amd64-de-de-heddarus-preview`        | Containerafbeelding met `de-DE` de `de-DE-HeddaRUS` land- en stem.        |
| `1.2.0-amd64-de-de-stefan-apollo-preview`   | Containerafbeelding met `de-DE` de `de-DE-Stefan-Apollo` land- en stem.   |
| `1.2.0-amd64-en-au-catherine-preview`       | Containerafbeelding met `en-AU` de `en-AU-Catherine` land- en stem.       |
| `1.2.0-amd64-en-au-hayleyrus-preview`       | Containerafbeelding met `en-AU` de `en-AU-HayleyRUS` land- en stem.       |
| `1.2.0-amd64-en-gb-george-apollo-preview`   | Containerafbeelding met `en-GB` de `en-GB-George-Apollo` land- en stem.   |
| `1.2.0-amd64-en-gb-hazelrus-preview`        | Containerafbeelding met `en-GB` de `en-GB-HazelRUS` land- en stem.        |
| `1.2.0-amd64-en-gb-susan-apollo-preview`    | Containerafbeelding met `en-GB` de `en-GB-Susan-Apollo` land- en stem.    |
| `1.2.0-amd64-en-in-heera-apollo-preview`    | Containerafbeelding met `en-IN` de `en-IN-Heera-Apollo` land- en stem.    |
| `1.2.0-amd64-en-in-priyarus-preview`        | Containerafbeelding met `en-IN` de `en-IN-PriyaRUS` land- en stem.        |
| `1.2.0-amd64-en-in-ravi-apollo-preview`     | Containerafbeelding met `en-IN` de `en-IN-Ravi-Apollo` land- en stem.     |
| `1.2.0-amd64-en-us-benjaminrus-preview`     | Containerafbeelding met `en-US` de `en-US-BenjaminRUS` land- en stem.     |
| `1.2.0-amd64-en-us-guy24krus-preview`       | Containerafbeelding met `en-US` de `en-US-Guy24kRUS` land- en stem.       |
| `1.2.0-amd64-en-us-jessa24krus-preview`     | Containerafbeelding met `en-US` de `en-US-Jessa24kRUS` land- en stem.     |
| `1.2.0-amd64-en-us-jessarus-preview`        | Containerafbeelding met `en-US` de `en-US-JessaRUS` land- en stem.        |
| `1.2.0-amd64-en-us-zirarus-preview`         | Containerafbeelding met `en-US` de `en-US-ZiraRUS` land- en stem.         |
| `1.2.0-amd64-es-es-helenarus-preview`       | Containerafbeelding met `es-ES` de `es-ES-HelenaRUS` land- en stem.       |
| `1.2.0-amd64-es-es-laura-apollo-preview`    | Containerafbeelding met `es-ES` de `es-ES-Laura-Apollo` land- en stem.    |
| `1.2.0-amd64-es-es-pablo-apollo-preview`    | Containerafbeelding met `es-ES` de `es-ES-Pablo-Apollo` land- en stem.    |
| `1.2.0-amd64-es-mx-hildarus-preview`        | Containerafbeelding met `es-MX` de `es-MX-HildaRUS` land- en stem.        |
| `1.2.0-amd64-es-mx-raul-apollo-preview`     | Containerafbeelding met `es-MX` de `es-MX-Raul-Apollo` land- en stem.     |
| `1.2.0-amd64-fr-ca-caroline-preview`        | Containerafbeelding met `fr-CA` de `fr-CA-Caroline` land- en stem.        |
| `1.2.0-amd64-fr-ca-harmonierus-preview`     | Containerafbeelding met `fr-CA` de `fr-CA-HarmonieRUS` land- en stem.     |
| `1.2.0-amd64-fr-fr-hortenserus-preview`     | Containerafbeelding met `fr-FR` de `fr-FR-HortenseRUS` land- en stem.     |
| `1.2.0-amd64-fr-fr-julie-apollo-preview`    | Containerafbeelding met `fr-FR` de `fr-FR-Julie-Apollo` land- en stem.    |
| `1.2.0-amd64-fr-fr-paul-apollo-preview`     | Containerafbeelding met `fr-FR` de `fr-FR-Paul-Apollo` land- en stem.     |
| `1.2.0-amd64-it-it-cosimo-apollo-preview`   | Containerafbeelding met `it-IT` de `it-IT-Cosimo-Apollo` land- en stem.   |
| `1.2.0-amd64-it-it-luciarus-preview`        | Containerafbeelding met `it-IT` de `it-IT-LuciaRUS` land- en stem.        |
| `1.2.0-amd64-ja-jp-ayumi-apollo-preview`    | Containerafbeelding met `ja-JP` de `ja-JP-Ayumi-Apollo` land- en stem.    |
| `1.2.0-amd64-ja-jp-harukarus-preview`       | Containerafbeelding met `ja-JP` de `ja-JP-HarukaRUS` land- en stem.       |
| `1.2.0-amd64-ja-jp-ichiro-apollo-preview`   | Containerafbeelding met `ja-JP` de `ja-JP-Ichiro-Apollo` land- en stem.   |
| `1.2.0-amd64-ko-kr-heamirus-preview`        | Containerafbeelding met `ko-KR` de `ko-KR-HeamiRUS` land- en stem.        |
| `1.2.0-amd64-pt-br-daniel-apollo-preview`   | Containerafbeelding met `pt-BR` de `pt-BR-Daniel-Apollo` land- en stem.   |
| `1.2.0-amd64-pt-br-heloisarus-preview`      | Containerafbeelding met `pt-BR` de `pt-BR-HeloisaRUS` land- en stem.      |
| `1.2.0-amd64-zh-cn-huihuirus-preview`       | Containerafbeelding met `zh-CN` de `zh-CN-HuihuiRUS` land- en stem.       |
| `1.2.0-amd64-zh-cn-kangkang-apollo-preview` | Containerafbeelding met `zh-CN` de `zh-CN-Kangkang-Apollo` land- en stem. |
| `1.2.0-amd64-zh-cn-yaoyao-apollo-preview`   | Containerafbeelding met `zh-CN` de `zh-CN-Yaoyao-Apollo` land- en stem.   |
| `1.1.0-amd64-de-de-hedda-preview`           | Containerafbeelding met `de-DE` de `de-DE-Hedda` land- en stem.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Containerafbeelding met `de-DE` de `de-DE-Hedda` land- en stem.           |
| `1.1.0-amd64-de-de-heddarus-preview`        | Containerafbeelding met `de-DE` de `de-DE-HeddaRUS` land- en stem.        |
| `1.1.0-amd64-de-de-stefan-apollo-preview`   | Containerafbeelding met `de-DE` de `de-DE-Stefan-Apollo` land- en stem.   |
| `1.1.0-amd64-en-au-catherine-preview`       | Containerafbeelding met `en-AU` de `en-AU-Catherine` land- en stem.       |
| `1.1.0-amd64-en-au-hayleyrus-preview`       | Containerafbeelding met `en-AU` de `en-AU-HayleyRUS` land- en stem.       |
| `1.1.0-amd64-en-gb-george-apollo-preview`   | Containerafbeelding met `en-GB` de `en-GB-George-Apollo` land- en stem.   |
| `1.1.0-amd64-en-gb-hazelrus-preview`        | Containerafbeelding met `en-GB` de `en-GB-HazelRUS` land- en stem.        |
| `1.1.0-amd64-en-gb-susan-apollo-preview`    | Containerafbeelding met `en-GB` de `en-GB-Susan-Apollo` land- en stem.    |
| `1.1.0-amd64-en-in-heera-apollo-preview`    | Containerafbeelding met `en-IN` de `en-IN-Heera-Apollo` land- en stem.    |
| `1.1.0-amd64-en-in-priyarus-preview`        | Containerafbeelding met `en-IN` de `en-IN-PriyaRUS` land- en stem.        |
| `1.1.0-amd64-en-in-ravi-apollo-preview`     | Containerafbeelding met `en-IN` de `en-IN-Ravi-Apollo` land- en stem.     |
| `1.1.0-amd64-en-us-benjaminrus-preview`     | Containerafbeelding met `en-US` de `en-US-BenjaminRUS` land- en stem.     |
| `1.1.0-amd64-en-us-guy24krus-preview`       | Containerafbeelding met `en-US` de `en-US-Guy24kRUS` land- en stem.       |
| `1.1.0-amd64-en-us-jessa24krus-preview`     | Containerafbeelding met `en-US` de `en-US-Jessa24kRUS` land- en stem.     |
| `1.1.0-amd64-en-us-jessarus-preview`        | Containerafbeelding met `en-US` de `en-US-JessaRUS` land- en stem.        |
| `1.1.0-amd64-en-us-zirarus-preview`         | Containerafbeelding met `en-US` de `en-US-ZiraRUS` land- en stem.         |
| `1.1.0-amd64-es-es-helenarus-preview`       | Containerafbeelding met `es-ES` de `es-ES-HelenaRUS` land- en stem.       |
| `1.1.0-amd64-es-es-laura-apollo-preview`    | Containerafbeelding met `es-ES` de `es-ES-Laura-Apollo` land- en stem.    |
| `1.1.0-amd64-es-es-pablo-apollo-preview`    | Containerafbeelding met `es-ES` de `es-ES-Pablo-Apollo` land- en stem.    |
| `1.1.0-amd64-es-mx-hildarus-preview`        | Containerafbeelding met `es-MX` de `es-MX-HildaRUS` land- en stem.        |
| `1.1.0-amd64-es-mx-raul-apollo-preview`     | Containerafbeelding met `es-MX` de `es-MX-Raul-Apollo` land- en stem.     |
| `1.1.0-amd64-fr-ca-caroline-preview`        | Containerafbeelding met `fr-CA` de `fr-CA-Caroline` land- en stem.        |
| `1.1.0-amd64-fr-ca-harmonierus-preview`     | Containerafbeelding met `fr-CA` de `fr-CA-HarmonieRUS` land- en stem.     |
| `1.1.0-amd64-fr-fr-hortenserus-preview`     | Containerafbeelding met `fr-FR` de `fr-FR-HortenseRUS` land- en stem.     |
| `1.1.0-amd64-fr-fr-julie-apollo-preview`    | Containerafbeelding met `fr-FR` de `fr-FR-Julie-Apollo` land- en stem.    |
| `1.1.0-amd64-fr-fr-paul-apollo-preview`     | Containerafbeelding met `fr-FR` de `fr-FR-Paul-Apollo` land- en stem.     |
| `1.1.0-amd64-it-it-cosimo-apollo-preview`   | Containerafbeelding met `it-IT` de `it-IT-Cosimo-Apollo` land- en stem.   |
| `1.1.0-amd64-it-it-luciarus-preview`        | Containerafbeelding met `it-IT` de `it-IT-LuciaRUS` land- en stem.        |
| `1.1.0-amd64-ja-jp-ayumi-apollo-preview`    | Containerafbeelding met `ja-JP` de `ja-JP-Ayumi-Apollo` land- en stem.    |
| `1.1.0-amd64-ja-jp-harukarus-preview`       | Containerafbeelding met `ja-JP` de `ja-JP-HarukaRUS` land- en stem.       |
| `1.1.0-amd64-ja-jp-ichiro-apollo-preview`   | Containerafbeelding met `ja-JP` de `ja-JP-Ichiro-Apollo` land- en stem.   |
| `1.1.0-amd64-ko-kr-heamirus-preview`        | Containerafbeelding met `ko-KR` de `ko-KR-HeamiRUS` land- en stem.        |
| `1.1.0-amd64-pt-br-daniel-apollo-preview`   | Containerafbeelding met `pt-BR` de `pt-BR-Daniel-Apollo` land- en stem.   |
| `1.1.0-amd64-pt-br-heloisarus-preview`      | Containerafbeelding met `pt-BR` de `pt-BR-HeloisaRUS` land- en stem.      |
| `1.1.0-amd64-zh-cn-huihuirus-preview`       | Containerafbeelding met `zh-CN` de `zh-CN-HuihuiRUS` land- en stem.       |
| `1.1.0-amd64-zh-cn-kangkang-apollo-preview` | Containerafbeelding met `zh-CN` de `zh-CN-Kangkang-Apollo` land- en stem. |
| `1.1.0-amd64-zh-cn-yaoyao-apollo-preview`   | Containerafbeelding met `zh-CN` de `zh-CN-Yaoyao-Apollo` land- en stem.   |
| `1.0.0-amd64-en-us-benjaminrus-preview`     | Containerafbeelding met `en-US` de `en-US-BenjaminRUS` land- en stem.     |
| `1.0.0-amd64-en-us-guy24krus-preview`       | Containerafbeelding met `en-US` de `en-US-Guy24kRUS` land- en stem.       |
| `1.0.0-amd64-en-us-jessa24krus-preview`     | Containerafbeelding met `en-US` de `en-US-Jessa24kRUS` land- en stem.     |
| `1.0.0-amd64-en-us-jessarus-preview`        | Containerafbeelding met `en-US` de `en-US-JessaRUS` land- en stem.        |
| `1.0.0-amd64-en-us-zirarus-preview`         | Containerafbeelding met `en-US` de `en-US-ZiraRUS` land- en stem.         |
| `1.0.0-amd64-zh-cn-huihuirus-preview`       | Containerafbeelding met `zh-CN` de `zh-CN-HuihuiRUS` land- en stem.       |
| `1.0.0-amd64-zh-cn-kangkang-apollo-preview` | Containerafbeelding met `zh-CN` de `zh-CN-Kangkang-Apollo` land- en stem. |
| `1.0.0-amd64-zh-cn-yaoyao-apollo-preview`   | Containerafbeelding met `zh-CN` de `zh-CN-Yaoyao-Apollo` land- en stem.   |

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

De [key phrase extraction][ta-kp] container afbeelding `mcr.microsoft.com` is te vinden op de container register syndicaat. Het bevindt `azure-cognitive-services` zich in `keyphrase`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `mcr.microsoft.com/azure-cognitive-services/keyphrase`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="language-detection"></a>Taaldetectie

De [containerafbeelding Taaldetectie][ta-la] is `mcr.microsoft.com` te vinden in het containerregistersyndicaat. Het bevindt `azure-cognitive-services` zich in `language`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `mcr.microsoft.com/azure-cognitive-services/language`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

## <a name="sentiment-analysis"></a>Sentimentanalyse

De [sentimentanalyse][ta-se] container afbeelding is `mcr.microsoft.com` te vinden op de container register syndicaat. Het bevindt `azure-cognitive-services` zich in `sentiment`de repository en is vernoemd. De volledig gekwalificeerde containerafbeeldingsnaam is, `mcr.microsoft.com/azure-cognitive-services/sentiment`.

Deze containerafbeelding heeft de volgende tags beschikbaar:

| Afbeeldingstags                    | Opmerkingen |
|-------------------------------|:------|
| `latest`                      |       |
| `1.1.009301-amd64-preview`    |       |
| `1.1.008510001-amd64-preview` |       |
| `1.1.007750002-amd64-preview` |       |
| `1.1.007360001-amd64-preview` |       |
| `1.1.006770001-amd64-preview` |       |

[ad-containers]: ../anomaly-Detector/anomaly-detector-container-howto.md
[cv-containers]: ../computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ../face/face-how-to-install-containers.md
[fr-containers]: ../form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ../luis/luis-container-howto.md
[sp-stt]: ../speech-service/speech-container-howto.md?tabs=stt
[sp-cstt]: ../speech-service/speech-container-howto.md?tabs=cstt
[sp-tts]: ../speech-service/speech-container-howto.md?tabs=tts
[sp-ctts]: ../speech-service/speech-container-howto.md?tabs=ctts
[ta-kp]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=keyphrase
[ta-la]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=language
[ta-se]: ../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment
