---
title: Aangepaste tref woorden-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor aangepaste tref woorden met behulp van de speech Software Development Kit (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: dd5748cf8afe19a49e5ea406aea9b558432eeaf3
ms.sourcegitcommit: c8a0fbfa74ef7d1fd4d5b2f88521c5b619eb25f8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82802179"
---
# <a name="what-is-a-keyword"></a>Wat is een tref woord?

Een tref woord is een woord of korte woord groep waarmee uw product spraak kan worden geactiveerd. "Hey Cortana" is bijvoorbeeld het tref woord voor de Cortana-assistent. Met de functie voor spraak activering kunnen uw gebruikers hun product volledig zelf door nemen door het tref woord te spreken. Als uw product voortdurend luistert naar het tref woord, wordt alle audio lokaal op het apparaat van de gebruiker verwerkt totdat er een detectie plaatsvindt om ervoor te zorgen dat de gegevens zo privé blijven.

## <a name="core-features-of-custom-keyword"></a>Belangrijkste functies van aangepast sleutel woord

Met de aanpassings-, prestatie-en integratie functies van het aangepaste sleutel woord kunt u spraak activering aanpassen aan het beste bij de visie van uw product en gebruikers behoeften.

| Onderdeel | Beschrijving |
|----------|----------|
| Trefwoord aanpassing | Een sleutel woord is een uitbrei ding van uw merk en versterkt het eigen vermogen dat u met uw klanten hebt gemaakt. Met de portal voor aangepaste tref woorden in speech Studio kunt u een woord of korte woord groep opgeven die het beste uw merk vormt. U kunt uw tref woord verder aanpassen door de juiste uitspraak te kiezen. dit wordt gerespecteerd door het gegenereerde trefwoord model.
| Trefwoord verificatie | Als er een hoge mate van vertrouwen in het sleutel woord lokaal wordt gedetecteerd, wordt audio naar de Cloud verzonden voor verdere verificatie dat een gebruiker het tref woord heeft genoemd. Verificatie van tref woorden biedt een extra beveiligingslaag door de impact van een onjuiste lokale detectie te verminderen en de privacy van gebruikers te beschermen.
| Spraak assistent & Speech SDK-integratie | Tref woorden die zijn gegenereerd op basis van het aangepaste tref woord in speech Studio, kunnen eenvoudig worden geïntegreerd binnen uw apparaat of toepassing via de spraak-SDK. U hoeft alleen maar de SDK te laten verwijzen naar het trefwoord model dat wordt meegeleverd met Speech Studio en uw product is geactiveerd met spraak, dat wordt ondersteund door de verificatie van het sleutel woord. U kunt de spraak ervaring van uw product volt ooien door uw eigen [Voice Assistant](voice-assistants.md)te bouwen.

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor het integreren van een trefwoord model is beschikbaar op GitHub. Deze voor beelden beslaan de client toepassing voor het integreren van uw tref woord in verschillende populaire programmeer talen.

* [Zelf studie: spraak-Activeer uw product met de Speech SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Voor beelden van Voice Assistant (SDK)](https://aka.ms/csspeech/samples)

## <a name="tutorial"></a>Zelfstudie

* [Een aangepast sleutel woord maken met behulp van speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws).
* Hoe u [uw product kunt activeren met behulp van de Speech SDK](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="reference-docs"></a>Referentie documenten

* [Richt lijnen voor naamgeving van aangepaste tref woorden](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-kws-guidelines)
* [Speech-SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
* [Een aangepast tref woord maken](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* [De Speech SDK ophalen](speech-sdk.md)
* [Meer informatie over spraak assistenten](voice-assistants.md)
