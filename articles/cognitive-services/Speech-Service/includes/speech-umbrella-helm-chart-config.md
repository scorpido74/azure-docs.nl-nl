---
title: Spraak containers installeren
titleSuffix: Azure Cognitive Services
description: Meer informatie over de configuratie opties voor de spraak-paraplu helm grafiek.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: ed64412ccf9d192506fafe546b1ccee7941aa43a
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522728"
---
### <a name="speech-umbrella-chart"></a>Spraak (paraplu-diagram)

De waarden in de grafiek op het hoogste niveau van de paraplu overschrijven de bijbehorende subdiagram waarden. Daarom moeten alle on-premises aangepaste waarden hier worden toegevoegd.

|Parameter|Beschrijving|Standaard|
| -- | -- | -- | -- |
| `speechToText.enabled` | Hiermee wordt aangegeven of de **spraak-naar-tekst-** service is ingeschakeld. | `true` |
| `speechToText.verification.enabled` | Hiermee wordt aangegeven of de `helm test` mogelijkheid voor **spraak-naar-tekst-** service is ingeschakeld. | `true` |
| `speechToText.verification.image.registry` | De opslag plaats voor de docker-installatie kopie die `helm test` gebruikt voor het testen van de **spraak-naar-tekst** service. Helm maakt in het cluster afzonderlijke pod voor het testen en haalt de *test* installatie kopie op uit het REGI ster. | `docker.io` |
| `speechToText.verification.image.repository` | De opslag plaats voor de docker-installatie kopie die `helm test` gebruikt voor het testen van de **spraak-naar-tekst** service. Helm test pod gebruikt deze opslag plaats voor het pullen van *test-use-* installatie kopieën. | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | De code van de docker-installatie kopie die wordt gebruikt met `helm test` voor de service **spraak naar tekst** . Helm test pod gebruikt deze tag voor het pullen van *test-use-* installatie kopieën. | `latest` |
| `speechToText.verification.image.pullByHash` | Hiermee wordt aangegeven of de docker-installatie kopie van *test-use* wordt opgehaald door hash. Als `true`, moet `speechToText.verification.image.hash` worden toegevoegd met een geldige hash-waarde voor de installatie kopie. | `false` |
| `speechToText.verification.image.arguments` | De argumenten die worden gebruikt voor het uitvoeren van de docker-installatie kopie voor *test* doeleinden. Helm test pod geeft deze argumenten door aan de container wanneer `helm test`wordt uitgevoerd. | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Hiermee wordt aangegeven of de **tekst-naar-spraak** -service is ingeschakeld. | `true` |
| `textToSpeech.verification.enabled` | Hiermee wordt aangegeven of de `helm test` mogelijkheid voor **spraak-naar-tekst-** service is ingeschakeld. | `true` |
| `textToSpeech.verification.image.registry` | De opslag plaats voor de docker-installatie kopie die `helm test` gebruikt voor het testen van de **spraak-naar-tekst** service. Helm maakt in het cluster afzonderlijke pod voor het testen en haalt de *test* installatie kopie op uit het REGI ster. | `docker.io` |
| `textToSpeech.verification.image.repository` | De opslag plaats voor de docker-installatie kopie die `helm test` gebruikt voor het testen van de **spraak-naar-tekst** service. Helm test pod gebruikt deze opslag plaats voor het pullen van *test-use-* installatie kopieën. | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | De code van de docker-installatie kopie die wordt gebruikt met `helm test` voor de service **spraak naar tekst** . Helm test pod gebruikt deze tag voor het pullen van *test-use-* installatie kopieën. | `latest` |
| `textToSpeech.verification.image.pullByHash` | Hiermee wordt aangegeven of de docker-installatie kopie van *test-use* wordt opgehaald door hash. Als `true`, moet `textToSpeech.verification.image.hash` worden toegevoegd met een geldige hash-waarde voor de installatie kopie. | `false` |
| `textToSpeech.verification.image.arguments` | De argumenten die moeten worden uitgevoerd met *behulp* van de docker-installatie kopie voor test doeleinden. De helm-test pod geeft deze argumenten door aan de container wanneer `helm test`wordt uitgevoerd. | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |