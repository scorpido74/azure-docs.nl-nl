---
title: Gesprekstranscriptie (preview) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Conversation Transcription is een oplossing voor vergaderingen, die herkenning, luidspreker-ID en diarisatie combineert om transcriptie van elk gesprek te bieden.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: ba56c7fb989658195f6394c7390c4f83027c7c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479736"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>Wat is gesprekstranscriptie in vergaderingen (Voorbeeld)?

Conversation Transcription is een [spraak-naar-tekst](speech-to-text.md) oplossing die spraakherkenning, luidsprekeridentificatie en zinstoeschrijving combineert aan elke spreker (ook bekend als _diarisatie)_ om real-time en/of asynchrone transcriptie van elk gesprek te bieden. Conversation Transcription onderscheidt sprekers in een gesprek om te bepalen wie wat en wanneer heeft gezegd, en maakt het voor ontwikkelaars gemakkelijk om spraak-naar-tekst toe te voegen aan hun toepassingen die diarisatie met meerdere luidsprekers uitvoeren.

## <a name="key-features"></a>Belangrijke functies

- **Tijdstempels** - elke spreker uiting heeft een tijdstempel, zodat u gemakkelijk vinden wanneer een zin werd gezegd.
- **Leesbare transcripties** - transcripties hebben opmaak en interpunctie automatisch toegevoegd om ervoor te zorgen dat de tekst nauw overeenkomt met wat er werd gezegd.
- **Gebruikersprofielen** - gebruikersprofielen worden gegenereerd door het verzamelen van spraakvoorbeelden van gebruikers en ze naar het genereren van handtekeningen te sturen.
- **Luidsprekeridentificatie** - sprekers worden geïdentificeerd met behulp van gebruikersprofielen en aan elk luidspreker-id wordt een _luidspreker-id_ toegewezen.
- **Diarization met meerdere luidsprekers** - bepaal wie wat heeft gezegd door de audiostream met elke luidspreker-id te synthetiseren.
- **Real-time transcriptie** - bieden live transcripties van wie zegt wat en wanneer, terwijl het gesprek gebeurt.
- **asynchrone transcriptie** – geef transcripties met een hogere nauwkeurigheid met behulp van een multichannel audiostream.

> [!NOTE]
> Hoewel Conversation Transcription geen limiet stelt aan het aantal luidsprekers in de kamer, is het geoptimaliseerd voor 2-10 luidsprekers per sessie.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="inclusive-meetings"></a>Inclusieve vergaderingen

Om vergaderingen inclusief te maken voor iedereen, zoals deelnemers die doof en slechthorend zijn, is het belangrijk om transcriptie in real time te hebben. Gesprek Transcriptie in real-time modus neemt vergadering audio en bepaalt wie wat zegt, zodat alle deelnemers aan de vergadering om het transcript te volgen en deel te nemen aan de vergadering zonder vertraging.

### <a name="improved-efficiency"></a>Verbeterde efficiëntie

Deelnemers aan de vergadering kunnen zich concentreren op de vergadering en het maken van notities overlaten aan gesprekstranscriptie. Deelnemers kunnen actief deelnemen aan de vergadering en snel de volgende stappen opvolgen, waarbij ze het transcript gebruiken in plaats van notities te maken en mogelijk iets missen tijdens de vergadering.

## <a name="how-it-works"></a>Hoe werkt het?

Dit is een overzicht op hoog niveau van hoe conversation transcription werkt.

![Het transcriptiediagram voor gespreksgesprekken importeren](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Verwachte ingangen

- **Multi-channel audiostream** – Zie Microsoft [Speech Device SDK Microphone](https://aka.ms/cts/microphone)voor specificatie- en ontwerpdetails. Zie Microsoft Speech Device SDK [ophalen](https://aka.ms/cts/getsdk)voor meer informatie of een ontwikkelkit aanschaffen.
- **Voice samples van gebruikers** – Conversation Transcription heeft gebruikersprofielen nodig voorafgaand aan het gesprek. U moet audio-opnamen van elke gebruiker verzamelen en vervolgens de opnamen naar de [Signature Generation Service](https://aka.ms/cts/signaturegenservice) sturen om de audio te valideren en gebruikersprofielen te genereren.

## <a name="real-time-vs-asynchronous"></a>Real-time vs. asynchroon

Conversation Transcription biedt drie transcriptiemodi:

### <a name="real-time"></a>Real-time

Audiogegevens worden live verwerkt om de luidspreker-id + transcript terug te sturen. Selecteer deze modus als de vereiste transcriptieoplossing is om deelnemers aan het gesprek een live transcriptieweergave van hun lopende gesprek te bieden. Het bouwen van een applicatie om vergaderingen toegankelijker te maken voor doven en slechthorenden is bijvoorbeeld een ideale use case voor real-time transcriptie.

### <a name="asynchronous"></a>Asynchrone

Audiogegevens worden batch verwerkt om de luidspreker-id en transcriptie terug te sturen. Selecteer deze modus als uw vereiste voor transcriptieoplossingen is om een hogere nauwkeurigheid te bieden zonder live transcript-weergave. Als u bijvoorbeeld een toepassing wilt bouwen om deelnemers aan de vergadering in staat te stellen gemiste vergaderingen eenvoudig in te halen, gebruikt u de asynchrone transcriptiemodus om transcriptieresultaten met hoge nauwkeurigheid te verkrijgen.

### <a name="real-time-plus-asynchronous"></a>Real-time plus asynchroon

Audiogegevens worden live verwerkt om de luidspreker-id + transcript terug te sturen, en daarnaast wordt een verzoek gemaakt om ook een transcriptie met hoge nauwkeurigheid te krijgen via asynchrone verwerking. Selecteer deze modus als uw toepassing behoefte heeft aan real-time transcriptie, maar ook een transcriptie met een hogere nauwkeurigheid vereist voor gebruik nadat het gesprek of de vergadering heeft plaatsgevonden.

## <a name="language-support"></a>Taalondersteuning

Momenteel ondersteunt Conversation Transcription "en-US" en "zh-CN" in de volgende regio's: *centralus* en *eastasia*. Als u extra ondersteuning voor de landine nodig hebt, neemt u contact op met de [Crew gesprekstranscriptie.](mailto:CTSFeatureCrew@microsoft.com)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Transcribeer gesprekken in realtime](how-to-use-conversation-transcription-service.md)
