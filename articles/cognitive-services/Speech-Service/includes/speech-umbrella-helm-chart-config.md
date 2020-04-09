---
title: Spraakcontainers installeren
titleSuffix: Azure Cognitive Services
description: Details van de spraak paraplu helm grafiek configuratie opties.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874336"
---
### <a name="speech-umbrella-chart"></a>Toespraak (paraplugrafiek)

Waarden in het bovenste niveau "paraplu" grafiek overschrijven de bijbehorende sub-grafiek waarden. Daarom moeten alle on-premises aangepaste waarden hier worden toegevoegd.

|Parameter|Beschrijving|Standaard|
| -- | -- | -- | -- |
| `speechToText.enabled` | Of de **spraak-naar-tekstservice** is ingeschakeld. | `true` |
| `speechToText.verification.enabled` | Of `helm test` de mogelijkheid voor **spraak-naar-tekstservice** is ingeschakeld. | `true` |
| `speechToText.verification.image.registry` | De docker-afbeeldingsopslagplaats die `helm test` wordt gebruikt om **spraak-naar-tekstservice te** testen. Helm maakt aparte pod in het cluster voor het testen en haalt de *test-use* afbeelding uit dit register. | `docker.io` |
| `speechToText.verification.image.repository` | De docker-afbeeldingsopslagplaats die `helm test` wordt gebruikt om **spraak-naar-tekstservice te** testen. Helm test pod maakt gebruik van deze repository te trekken *test-use* afbeelding. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | De docker-afbeeldingstag waarmee `helm test` u wordt gebruikt voor **spraak-naar-tekstservice.** Helm test pod maakt gebruik van deze tag om *test-use* afbeelding te trekken. | `latest` |
| `speechToText.verification.image.pullByHash` | Of de *test-use* docker afbeelding wordt getrokken door hash. `true`Als, `speechToText.verification.image.hash` moet worden toegevoegd, met geldige afbeelding hash waarde. | `false` |
| `speechToText.verification.image.arguments` | De argumenten die worden gebruikt om de *dockerafbeelding voor testgebruik* uit te voeren. Helm test pod geeft deze argumenten `helm test`aan de container bij het uitvoeren van . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Of de **tekst-naar-spraakservice** is ingeschakeld. | `true` |
| `textToSpeech.verification.enabled` | Of `helm test` de mogelijkheid voor **spraak-naar-tekstservice** is ingeschakeld. | `true` |
| `textToSpeech.verification.image.registry` | De docker-afbeeldingsopslagplaats die `helm test` wordt gebruikt om **spraak-naar-tekstservice te** testen. Helm maakt aparte pod in het cluster voor het testen en haalt de *test-use* afbeelding uit dit register. | `docker.io` |
| `textToSpeech.verification.image.repository` | De docker-afbeeldingsopslagplaats die `helm test` wordt gebruikt om **spraak-naar-tekstservice te** testen. Helm test pod maakt gebruik van deze repository te trekken *test-use* afbeelding. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | De docker-afbeeldingstag waarmee `helm test` u wordt gebruikt voor **spraak-naar-tekstservice.** Helm test pod maakt gebruik van deze tag om *test-use* afbeelding te trekken. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Of de *test-use* docker afbeelding wordt getrokken door hash. `true`Als, `textToSpeech.verification.image.hash` moet worden toegevoegd, met geldige afbeelding hash waarde. | `false` |
| `textToSpeech.verification.image.arguments` | De argumenten die moeten worden uitgevoerd met de dockerafbeelding *voor testgebruik.* De helmtestpod geeft deze argumenten `helm test`door aan de container wanneer deze wordt uitgevoerd . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |