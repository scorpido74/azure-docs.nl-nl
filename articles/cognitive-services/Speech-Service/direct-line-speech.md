---
title: Direct Line Speech - Spraakservice
titleSuffix: Azure Cognitive Services
description: Een overzicht van de functies, mogelijkheden en beperkingen voor spraakassistenten met Direct Line Speech met de Speech Software Development Kit (SDK).
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 6dc125b68af8f9a07731aaae9d858c19c51f934f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402397"
---
# <a name="what-is-direct-line-speech"></a>Wat is Direct Line-spraak?

**Direct Line Speech** is een robuuste, end-to-end oplossing voor het creëren van een flexibele, uitbreidbare spraakassistent. Het wordt aangedreven door het Bot Framework en zijn Direct Line Speech kanaal, dat is geoptimaliseerd voor voice-in, voice-out interactie met bots.

[Spraakassistenten](voice-assistants.md) luisteren naar gebruikers en ondernemen een actie in reactie, vaak terug te spreken. Ze gebruiken [spraak-naar-tekst](speech-to-text.md) om de spraak van de gebruiker te transcriberen en ondernemen vervolgens actie op het natuurlijke taalbegrip van de tekst. Deze actie omvat vaak gesproken uitvoer van de assistent gegenereerd met [tekst-naar-spraak](text-to-speech.md).

Direct Line Speech biedt de hoogste niveaus van maatwerk en verfijning voor spraakassistenten. Het is ontworpen voor gespreksscenario's die open, natuurlijk of hybriden van de twee zijn met taakvoltooiing of gebruik van opdrachten en besturing. Deze hoge mate van flexibiliteit wordt geleverd met een grotere complexiteit en scenario's die zijn afgestemd op goed gedefinieerde taken met behulp van natuurlijke taalinvoer, kunnen [aangepaste opdrachten (Preview)](custom-commands.md) overwegen voor een gestroomlijnde oplossingservaring.

## <a name="getting-started-with-direct-line-speech"></a>Aan de slag met Direct Line Speech

De eerste stappen voor het maken van een spraakassistent met Direct Line Speech zijn het [krijgen van een spraakabonnementssleutel,](get-started.md)het maken van een nieuwe bot die aan dat abonnement is gekoppeld en de bot verbinden met het Direct Line Speech-kanaal.

   ![Conceptueel diagram van de servicestroom van de direct line-spraakorkestratie](media/voice-assistants/overview-directlinespeech.png "De spraakkanaalstroom")

Zie [de zelfstudie voor spraakwaarmee u uw bot inschakelt met de Spraak- en Het Kanaal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md)voor een volledige stapsgewijze handleiding voor het maken van een eenvoudige spraakassistent met Direct Line Speech.

We bieden ook quickstarts die zijn ontworpen om u code te laten uitvoeren en de API's snel te leren. Deze tabel bevat een lijst met spraakassistenten die snel worden georganiseerd op taal en platform.

| Snelstartgids | Platform | API-verwijzing |
|------------|----------|---------------|
| C#, UWP | Windows | [Bladeren](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Bladeren](https://aka.ms/csspeech/javaref) |
| Java | Android | [Bladeren](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Voorbeeldcode

Voorbeeldcode voor het maken van een spraakassistent is beschikbaar op GitHub. Deze voorbeelden hebben betrekking op de clienttoepassing voor het verbinden met uw assistent in verschillende populaire programmeertalen.

* [Voorbeelden van spraakassistenten (SDK)](https://aka.ms/csspeech/samples)
* [Zelfstudie: Spraakinschakelen van uw assistent met de Spraak-SDK, C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Aanpassing

Spraakassistenten die zijn gebouwd met spraakservice, kunnen het volledige scala aan aanpassingsopties gebruiken die beschikbaar zijn voor [spraak-naar-tekst,](speech-to-text.md) [tekst-naar-spraak](text-to-speech.md)en [aangepaste trefwoordselectie.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> Aanpassingsopties verschillen per taal/landtaal (zie [Ondersteunde talen).](supported-languages.md)

Direct Line Speech en de bijbehorende functionaliteit voor spraakassistenten zijn een ideale aanvulling op de [virtual assistant solution en enterprise template.](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) Hoewel Direct Line Speech kan werken met elke compatibele bot, bieden deze bronnen een herbruikbare basislijn voor hoogwaardige conversatie-ervaringen en gemeenschappelijke ondersteunende vaardigheden en modellen om snel aan de slag te gaan.

## <a name="reference-docs"></a>Referentiedocumenten

* [Speech-SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Volgende stappen

* [Ontvang gratis een abonnementssleutel voor spraakservice](get-started.md)
* [Download de Spraak-SDK](speech-sdk.md)
* [Een basisbot maken en implementeren](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [De sjabloon voor virtuele assistent-oplossingen en ondernemingsoplossingen downloaden](https://github.com/Microsoft/AI)
