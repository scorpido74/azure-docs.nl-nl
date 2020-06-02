---
title: Aangepaste opdrachten (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor aangepaste opdrachten (preview), een oplossing voor het maken van spraak toepassingen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 3c02e823202aa848a4de94885276835899562e4b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266012"
---
# <a name="what-is-custom-commands-preview"></a>Wat zijn aangepaste opdrachten (preview)?

Spraak toepassingen, zoals [spraak assistenten](voice-assistants.md) , Luis teren naar gebruikers en nemen een actie in antwoord, vaak gesp roken terug. Ze gebruiken [spraak-naar-tekst](speech-to-text.md) om de spraak van de gebruiker te transcriberen en vervolgens actie te ondernemen op het memorandum van natuurlijke taal van de tekst. Deze actie omvat vaak gesp roken uitvoer van de assistent die is gegenereerd met [tekst-naar-spraak](text-to-speech.md). Apparaten maken verbinding met assistenten met het object Speech SDK `DialogServiceConnector` .

**Aangepaste opdrachten (preview)** is een gestroomlijnde oplossing voor het maken van spraak toepassingen. Het biedt een geïntegreerde ontwerp ervaring, een automatisch hosting model en relatief lagere complexiteit en andere opties, zoals [directe lijn spraak](direct-line-speech.md). Deze vereenvoudiging wordt echter geleverd met een lagere flexibiliteit. Aangepaste opdrachten (preview) zijn dus het meest geschikt voor het volt ooien van taken of voor scenario's met opdrachten en besturings elementen. Het is met name goed afgestemd op Internet of Things (IoT) en headless apparaten.

Voor complexe conversatie-interacties en integratie met andere oplossingen, zoals de [Virtual assistent-oplossing en de ondernemings sjabloon,](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) wordt u geadviseerd om direct line speech te gebruiken.

Goede kandidaten voor aangepaste opdrachten (preview) hebben een vaste vocabulaire met goed gedefinieerde sets variabelen. Home Automation-taken, zoals het beheren van een thermo staat, zijn bijvoorbeeld ideaal.

   ![Voor beelden van scenario's voor het volt ooien van taken](media/voice-assistants/task-completion-examples.png "voor beelden van taak voltooiing")

## <a name="getting-started-with-custom-commands-preview"></a>Aan de slag met aangepaste opdrachten (preview-versie)

De eerste stap voor het gebruik van aangepaste opdrachten (preview) om een spraak toepassing te maken, is door [een sleutel voor een spraak abonnement te verkrijgen](get-started.md) en de opbouw functie voor aangepaste opdrachten (preview) te openen in de [Speech Studio](https://speech.microsoft.com). Van daaruit kunt u een nieuwe toepassing voor aangepaste opdrachten (preview) maken en deze publiceren, waarna een toepassing op het apparaat kan communiceren met de spraak-SDK.

   ![Ontwerp stroom voor aangepaste opdrachten (preview-versie)](media/voice-assistants/custom-commands-flow.png "De ontwerp stroom voor aangepaste opdrachten (preview-versie)")

We bieden Quick starts die zijn ontworpen om code in minder dan 10 minuten uit te voeren.

* [Een aangepaste opdrachten (preview)-toepassing maken](quickstart-custom-speech-commands-create-new.md)
* [Een aangepaste opdrachten (preview)-toepassing maken met para meters](quickstart-custom-speech-commands-create-parameters.md)
* [Verbinding maken met een toepassing voor aangepaste opdrachten (preview) met de Speech SDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Wanneer u klaar bent met de Quick starts, kunt u onze How-Tos verkennen.

- [Validaties toevoegen aan aangepaste opdracht parameters](./how-to-custom-speech-commands-validations.md)
- [Opdrachten op de client uitvoeren met de Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Een bevestiging toevoegen aan een aangepaste opdracht](./how-to-custom-speech-commands-confirmations.md)
- [Een correctie met één stap toevoegen aan een aangepaste opdracht](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Volgende stappen

* [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
* [Ga naar de speech Studio om aangepaste opdrachten uit te proberen](https://speech.microsoft.com)
* [De Speech SDK ophalen](speech-sdk.md)
