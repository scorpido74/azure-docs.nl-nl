---
title: Spraak-naar-tekst-spraak-service
titleSuffix: Azure Cognitive Services
description: Met de functie voor spraak naar tekst kunt u transcriptie in realtime omzetten in tekst. Uw toepassingen, hulpprogram ma's of apparaten kunnen deze tekst invoer gebruiken, weer geven en actie ondernemen. Deze service werkt probleemloos met de functies voor tekst naar spraak (spraak synthese) en spraak omzetting.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: trbye
ms.openlocfilehash: b1e84f9acb439324317ed2f0d11ed06eb3e43ad8
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84678060"
---
# <a name="what-is-speech-to-text"></a>Wat is spraak-naar-tekst?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Spraak-naar-tekst van de speech-service, ook wel spraak herkenning genoemd, biedt realtime transcriptie van audio-streams in tekst. Uw toepassingen, hulpprogram ma's of apparaten kunnen deze tekst gebruiken, weer geven en actie ondernemen als invoer van de opdracht. Deze service wordt aangestuurd door dezelfde herkennings technologie die door micro soft wordt gebruikt voor Cortana en Office-producten. Het werkt probleemloos met de <a href="./speech-translation.md" target="_blank">Vertaal <span class="docon docon-navigate-external x-hidden-focus"></span> </a> -en <a href="./text-to-speech.md" target="_blank">tekst-naar- <span class="docon docon-navigate-external x-hidden-focus"></span> spraak-</a> service aanbiedingen. Zie [ondersteunde talen](language-support.md#speech-to-text)voor een volledige lijst met beschik bare spraak-naar-tekst talen.

De spraak-naar-tekst-service is standaard ingesteld op het gebruik van het universele-taal model. Dit model is getraind met gegevens van micro soft en wordt geïmplementeerd in de Cloud. Het is optimaal voor gespreks-en dicteer scenario's. Wanneer u spraak naar tekst gebruikt voor herkenning en transcriptie in een unieke omgeving, kunt u aangepaste akoestische, taal-en uitspraak modellen maken en trainen. Aanpassing is handig voor het adresseren van omgevings lawaai of branchespecifieke woorden lijsten.

Met aanvullende referentie tekst als invoer, biedt spraak-naar-tekst-service ook de mogelijkheid om de uitspraak [te beoordelen en](rest-speech-to-text.md#pronunciation-assessment-parameters) feedback te geven over de nauw keurigheid en fluency van gesp roken audio. Met het beoordelen van uitspraak kunnen taal kennisers oefenen, direct feedback krijgen en de uitspraak verbeteren zodat ze kunnen praten en presen teren. Docenten kunnen de mogelijkheid gebruiken om de uitspraak van meerdere sprekers in realtime te evalueren. De functie ondersteunt momenteel de Engelse versie en vertrouwt samen met spraak beoordelingen die door experts worden uitgevoerd.

> [!NOTE]
> Bing Speech is uit bedrijf genomen op 15 oktober 2019. Als uw toepassingen, hulpprogram ma's of producten gebruikmaken van de Bing Speech Api's, hebben we gidsen gemaakt om u te helpen migreren naar de spraak service.
> - [Migreren van Bing Speech naar de speech-service](how-to-migrate-from-bing-speech.md)

## <a name="get-started-with-speech-to-text"></a>Aan de slag met spraak naar tekst

De spraak-naar-tekst-service is beschikbaar via de [Speech-SDK](speech-sdk.md). Er zijn verschillende algemene scenario's beschikbaar als Quick starts in verschillende talen en platformen:

 - [Snelstartgids: spraak herkennen met microfoon invoer](quickstarts/speech-to-text-from-microphone.md)
 - [Quick Start: spraak herkennen vanuit een bestand](quickstarts/speech-to-text-from-file.md)
 - [Quick Start: spraak herkennen die zijn opgeslagen in Blob Storage](quickstarts/from-blob.md)

Zie [rest api's](rest-speech-to-text.md)als u de spraak-naar-tekst rest-service wilt gebruiken.

 - [Snelstartgids: beoordeling van de uitspraak met referentie-invoer](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="tutorials-and-sample-code"></a>Zelf studies en voorbeeld code

Nadat u de spraak service hebt bekeken, kunt u de zelf studie uitproberen die u leert hoe u de intenties kunt herkennen vanuit spraak met behulp van de Speech SDK en LUIS.

- [Zelf studie: intenties herkennen vanuit spraak met de Speech SDK en LUIS met behulp van C #](how-to-recognize-intents-from-speech-csharp.md)

Voorbeeld code voor de Speech SDK is beschikbaar op GitHub. Deze voor beelden hebben betrekking op veelvoorkomende scenario's, zoals het lezen van audio van een bestand of stream, continue en eenmalige herkenning en het werken met aangepaste modellen.

- [Voor beelden van spraak naar tekst (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Voor beelden van batch transcriptie (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Evaluatie voorbeelden van uitspraak (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>Aanpassing

Naast het standaard spraak service model kunt u aangepaste modellen maken. Aanpassing helpt de obstakels voor spraak herkenning, zoals spreek stijl, vocabulaire en achtergrond ruis, te overwinnen [Custom speech](how-to-custom-speech.md). Aanpassings opties variëren per taal/land instelling. Zie [ondersteunde talen](supported-languages.md) voor het controleren van de ondersteuning.

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>Volgende stappen

- [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
- [De Speech SDK ophalen](speech-sdk.md)
