---
title: Aangepaste opdrachten (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor aangepaste opdrachten (preview), een oplossing voor het maken van spraak assistenten.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: e41a037e657a8d2b484a3fcb62eb3bc03f53682d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507086"
---
# <a name="custom-commands-preview"></a>Aangepaste opdrachten (preview-versie)

[Spraak assistenten](voice-assistants.md) worden naar gebruikers geluisterd en nemen een actie in antwoord, vaak gesp roken terug. Ze gebruiken [spraak-naar-tekst](speech-to-text.md) om de spraak van de gebruiker te transcriberen en vervolgens actie te ondernemen op het memorandum van natuurlijke taal van de tekst. Deze actie omvat vaak gesp roken uitvoer van de assistent die is gegenereerd met [tekst-naar-spraak](text-to-speech.md). Apparaten maken verbinding met assistenten met het `DialogServiceConnector`-object van de Speech SDK.

**Aangepaste opdrachten (preview)** is een gestroomlijnde oplossing voor het maken van een spraak assistent. Het biedt een uniforme ontwerp-ervaring, een automatisch hosting model en relatief lagere complexiteit versus andere opties voor het maken van een assistent, zoals [direct line speech](direct-line-speech.md). Deze vereenvoudiging wordt echter geleverd met een lagere flexibiliteit. Aangepaste opdrachten (preview) zijn dus het meest geschikt voor het volt ooien van taken of voor scenario's met opdrachten en besturings elementen.

Voor complexe conversatie-interacties en integratie met andere oplossingen, zoals de [Virtual assistent-oplossing en de ondernemings sjabloon,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) wordt u geadviseerd om direct line speech te gebruiken.

Goede kandidaten voor aangepaste opdrachten (preview) hebben een vaste vocabulaire met goed gedefinieerde sets variabelen. Home Automation-taken, zoals het beheren van een thermo staat, zijn bijvoorbeeld ideaal.

   ![Voor beelden van scenario's voor het volt ooien van taken](media/voice-assistants/task-completion-examples.png "voor beelden van taak voltooiing")

## <a name="getting-started-with-custom-commands-preview"></a>Aan de slag met aangepaste opdrachten (preview-versie)

De eerste stap voor het gebruik van aangepaste opdrachten (preview) voor het maken van een spraak assistent is het [verkrijgen van een sleutel voor een spraak abonnement](get-started.md) en het openen van de opbouw functie voor aangepaste opdrachten (preview) in de [Speech Studio](https://speech.microsoft.com). Van daaruit kunt u een nieuwe toepassing voor aangepaste opdrachten (preview) maken en deze publiceren, waarna een toepassing op het apparaat kan communiceren met de spraak-SDK.

   ![Ontwerp stroom voor aangepaste opdrachten (preview-versie)](media/voice-assistants/custom-commands-flow.png "De ontwerp stroom voor aangepaste opdrachten (preview-versie)")

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren.

* [Een aangepaste opdrachten (preview)-toepassing maken](quickstart-custom-speech-commands-create-new.md)
* [Een aangepaste opdrachten (preview)-toepassing maken met para meters](quickstart-custom-speech-commands-create-parameters.md)
* [Verbinding maken met een toepassing voor aangepaste opdrachten (preview) met de Speech SDK,C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeld code voor het maken van een spraak assistent met aangepaste opdrachten (preview) is beschikbaar op GitHub.

* [Voor beelden van Voice Assistant (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>Aanpassing

Spraak assistenten die zijn gemaakt met behulp van Azure speech Services kunnen gebruikmaken van het volledige aanbod aan aanpassings opties voor [spraak naar tekst](speech-to-text.md), [tekst naar spraak](text-to-speech.md)en [aangepaste trefwoord selectie](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Aanpassings opties variëren per taal/land instelling (Zie [ondersteunde talen](supported-languages.md)).

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
* [De Speech SDK ophalen](speech-sdk.md)
