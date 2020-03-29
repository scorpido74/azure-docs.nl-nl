---
title: Aangepaste opdrachten (voorbeeld) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor Aangepaste opdrachten (Preview), een oplossing voor het maken van spraaktoepassingen.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367836"
---
# <a name="what-are-custom-commands-preview"></a>Wat zijn aangepaste opdrachten (Voorbeeld)?

Spraaktoepassingen zoals [spraakassistenten](voice-assistants.md) luisteren naar gebruikers en ondernemen een actie als reactie, waarbij ze vaak teruggesproken worden. Ze gebruiken [spraak-naar-tekst](speech-to-text.md) om de spraak van de gebruiker te transcriberen en ondernemen vervolgens actie op het natuurlijke taalbegrip van de tekst. Deze actie omvat vaak gesproken uitvoer van de assistent gegenereerd met [tekst-naar-spraak](text-to-speech.md). Apparaten maken verbinding met assistenten `DialogServiceConnector` met het object van de Speech SDK.

**Custom Commands (Preview)** is een gestroomlijnde oplossing voor het maken van spraaktoepassingen. Het biedt een uniforme ontwerpervaring, een automatisch hostingmodel en een relatief lagere complexiteit ten opzichte van andere opties zoals [Direct Line Speech.](direct-line-speech.md) Deze vereenvoudiging brengt echter een vermindering van de flexibiliteit met zich mee. Aangepaste opdrachten (Preview) zijn dus het meest geschikt voor taakvoltooiing of opdrachten- en controlescenario's. Het is bijzonder goed afgestemd op Internet of Things (IoT) en headless apparaten.

Voor complexe gespreksinteractie en integratie met andere oplossingen zoals de [Virtual Assistant Solution en Enterprise Template](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) wordt u aangemoedigd om Direct Line Speech te gebruiken.

Goede kandidaten voor Aangepaste Opdrachten (Preview) hebben een vaste woordenschat met goed gedefinieerde sets variabelen. Domoticataken, zoals het bedienen van een thermostaat, zijn bijvoorbeeld ideaal.

   ![Voorbeelden van scenario's voor taakvoltooiing](media/voice-assistants/task-completion-examples.png "voorbeelden van taakvoltooiing")

## <a name="getting-started-with-custom-commands-preview"></a>Aan de slag met aangepaste opdrachten (voorbeeld)

De eerste stap voor het gebruik van Aangepaste opdrachten (Preview) om een spraaktoepassing te maken, is om [een spraakabonnementssleutel](get-started.md) te krijgen en toegang te krijgen tot de Aangepaste opdrachten (Preview) builder in de [Spraakstudio](https://speech.microsoft.com). Van daaruit u een nieuwe aangepaste opdrachten (Preview)-toepassing maken en publiceren, waarna een toepassing op het apparaat ermee kan communiceren met de Speech SDK.

   ![Ontwerpstroom voor aangepaste opdrachten (voorbeeld)](media/voice-assistants/custom-commands-flow.png "De ontwerpstroom voor aangepaste opdrachten (Preview)")

We bieden quickstarts die zijn ontworpen om u in minder dan 10 minuten code te laten uitvoeren.

* [Een toepassing Aangepaste opdrachten (Voorbeeld) maken](quickstart-custom-speech-commands-create-new.md)
* [Een toepassing Aangepaste opdrachten (Preview) maken met parameters](quickstart-custom-speech-commands-create-parameters.md)
* [Verbinding maken met een aangepaste voorvertoningstoepassing met de SpraakSDK, C #](quickstart-custom-speech-commands-speech-sdk.md)

Zodra u klaar bent met de quickstarts, ontdek onze how-tos.

- [Validaties toevoegen aan aangepaste opdrachtparameters](./how-to-custom-speech-commands-validations.md)
- [Opdrachten voor de client uitvoeren met de Spraak-SDK](./how-to-custom-speech-commands-fulfill-sdk.md)
- [Een bevestiging toevoegen aan een aangepaste opdracht](./how-to-custom-speech-commands-confirmations.md)
- [Een correctie met één stap toevoegen aan een aangepaste opdracht](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>Volgende stappen

* [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
* [Ga naar de spraakstudio om aangepaste opdrachten uit te proberen](https://speech.microsoft.com)
* [Download de Spraak-SDK](speech-sdk.md)
