---
title: Spraakcontainers installeren
titleSuffix: Azure Cognitive Services
description: Details van de spraak paraplu helm grafiek configuratie opties.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73522728"
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