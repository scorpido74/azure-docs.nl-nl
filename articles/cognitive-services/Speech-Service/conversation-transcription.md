---
title: Wat is een conversatie transcriptie (preview)? -Spraak service
titleSuffix: Azure Cognitive Services
description: Conversation transcriptie is een oplossing voor spraak naar tekst waarmee spraak herkenning, de identificatie van de spreker en de toewijzing van zinnen aan elke spreker (ook wel bekend als diarization) worden gecombineerd om te voorzien in realtime en/of asynchrone transcriptie van elke enger.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 07e3e498a3bd2693b72079a8a731c3d139414c90
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806283"
---
# <a name="what-is-conversation-transcription-preview"></a>Wat is een conversatie transcriptie (preview)?

Conversation transcriptie is een oplossing [voor spraak naar tekst](speech-to-text.md) waarmee spraak herkenning, de identificatie van de spreker en de toewijzing van zinnen aan elke spreker (ook wel bekend als _diarization_) worden gecombineerd om te voorzien in realtime en/of asynchroon transcriptie van een gesprek. Conversatie transcriptie onderscheidt sprekers in een gesprek om te bepalen wie wat en wanneer en wanneer, en maakt het gemakkelijk voor ontwikkel aars om spraak-naar-tekst toe te voegen aan hun toepassingen die Multi-Speaker diarization uitvoeren.

## <a name="key-features"></a>Belangrijke functies

- **Tijds tempels** : elke utterance van de spreker heeft een tijds tempel, zodat u gemakkelijk kunt vinden wanneer een woord groep werd genoemd.
- **Lees bare transcripten** : transcripten hebben automatisch opmaak en interpunctie toegevoegd om ervoor te zorgen dat de tekst goed overeenkomt met wat werd gezegd.
- **Gebruikers profielen** : gebruikers profielen worden gegenereerd door de voor beelden van gebruikers spraak te verzamelen en ze te verzenden naar het genereren van hand tekeningen.
- **Luidspreker identificatie** : de luid sprekers worden geïdentificeerd met behulp van gebruikers profielen en er wordt een _spreker-id_ aan elk toegewezen.
- **Multi-Speaker diarization** : Bepaal wie wat heeft gezegd door de audio stroom te syntheseen met elke luidspreker-id.
- **Real-time transcriptie** : bieden live transcripten van wie zegt wat en wanneer de conversatie plaatsvindt.
- **asynchroon transcriptie** : Geef transcripten met een grotere nauw keurigheid door gebruik te maken van een meerkanaals audio stroom.

> [!NOTE]
> Hoewel door de conversatie transcriptie geen limiet wordt ingesteld voor het aantal luid sprekers in de ruimte, is deze geoptimaliseerd voor 2-10 sprekers per sessie.

## <a name="use-cases"></a>Use cases

### <a name="inclusive-meetings"></a>Inclusieve vergaderingen

Als u vergaderingen voor iedereen wilt maken, zoals deel nemers die doof en slechthorend zijn, is het belang rijk om transcriptie in realtime te hebben. In de real-time modus van conversaties wordt audio aan de vergadering gestuurd en wordt bepaald wie er moet zeggen, waardoor alle deel nemers aan de vergadering de transcripten kunnen volgen en zonder enige vertraging deel nemen aan de vergadering.

### <a name="improved-efficiency"></a>Verbeterde efficiëntie

Deel nemers aan het vergaderen kunnen zich richten op de vergadering en moeten notities maken op het gesprek transcriptie. Deel nemers kunnen actief gebruikmaken van de vergadering en snel opvolgen tijdens de volgende stappen, waarbij de transcriptie wordt gebruikt in plaats van notities te maken en mogelijk iets te missen tijdens de vergadering.

## <a name="how-it-works"></a>Het werkt als volgt

Dit is een overzicht op hoog niveau van de werking van de conversatie transcriptie.

![Het diagram gesprek transcriptie importeren](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Verwachte invoer

- **Multi Channel audio stream** : Zie de [micro soft speech Device SDK-microfoon](https://aka.ms/cts/microphone)voor informatie over de specificatie en het ontwerp. Raadpleeg voor meer informatie of een Development Kit aanschaffen [micro soft speech Device SDK](https://aka.ms/cts/getsdk)(Engelstalig).
- Voor **beelden van gebruikers spraak** : conversatie transcriptie moet vóór de conversatie gebruikers profielen hebben. U moet audio-opnames van elke gebruiker verzamelen en vervolgens de opnamen naar de [service Signature generate](https://aka.ms/cts/signaturegenservice) verzenden om de audio te valideren en gebruikers profielen te genereren.

## <a name="real-time-vs-asynchronous"></a>Realtime versus asynchroon

De conversatie transcriptie biedt drie transcriptie-modi:

### <a name="real-time"></a>Real-time

Audio gegevens worden live verwerkt om de luid spreker-id en transcriptie te retour neren. Selecteer deze modus als uw transcriptie-oplossing vereist is om gesprek deel nemers te voorzien van een live transcriptie van hun doorlopende conversatie. Zo is het bouwen van een toepassing om vergaderingen toegankelijker te maken voor dove en hard of slechthorende deel nemers is een ideaal gebruiks voorbeeld voor realtime-transcriptie.

### <a name="asynchronous"></a>Asynchrone

De code van de audio gegevens wordt verwerkt om de luid spreker-id en transcriptie te retour neren. Selecteer deze modus als uw transcriptie vereist is om een hogere nauw keurigheid te bieden zonder de weer gave Live transcripten. Als u bijvoorbeeld een toepassing wilt maken waarmee deel nemers aan de vergadering gemakkelijk gemiste vergaderingen kunnen opsporen, gebruikt u de asynchrone transcriptie-modus om hoge nauw keurigheid transcriptie resultaten te krijgen.

### <a name="real-time-plus-asynchronous"></a>Real-time plus asynchroon

Audio gegevens worden live verwerkt om de luid spreker-id en transcriptie te retour neren, en daarnaast wordt er een aanvraag gemaakt om ook een afschrift met een hoge nauw keurigheid te krijgen via asynchrone verwerking. Selecteer deze modus als uw toepassing behoefte heeft aan een real-time transcriptie, maar ook een nauw keurige transcriptie nodig heeft voor gebruik nadat de conversatie of de vergadering heeft plaatsgevonden.

## <a name="language-support"></a>Taalondersteuning

Op dit moment ondersteunt de conversatie transcriptie "en-US" en "zh-CN" in de volgende regio's: *centralus* en *EastAsia*. Als u aanvullende ondersteuning voor de land instelling nodig hebt, neemt u contact op met de [transcriptie-functie](mailto:CTSFeatureCrew@microsoft.com)van de conversatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Rekken in realtime transcriberen](how-to-use-conversation-transcription-service.md)
