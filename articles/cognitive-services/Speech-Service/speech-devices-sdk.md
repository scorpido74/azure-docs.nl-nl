---
title: Over de speech-apparaten SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Aan de slag met de SDK voor spraak apparaten. De speech-service werkt met een groot aantal verschillende apparaten en audio bronnen. Nu kunt u uw spraak toepassingen naar een hoger niveau brengen met overeenkomende hardware en software. De SDK voor spraak apparaten is een vooraf afgestemde bibliotheek die is gekoppeld aan speciaal ontwikkelde, ontwikkel Kits voor microfoon matrices.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 243072477c7d249d7066a7a448061c51a0bd2f34
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468719"
---
# <a name="about-the-speech-devices-sdk"></a>Over de speech-apparaten SDK

De [spraak Services](overview.md) werken met een groot aantal verschillende apparaten en audio bronnen. Nu kunt u uw spraak toepassingen naar een hoger niveau brengen met overeenkomende hardware en software. De SDK voor spraak apparaten is een vooraf geclusterde bibliotheek die is gekoppeld aan de ontwikkelings Kits voor het doel van de microfoon matrix.

De SDK voor spraak apparaten kan u helpen bij het volgende:

* Test nieuwe spraak scenario's snel.
* De spraak Services op basis van de cloud kunt u gemakkelijker integreren in uw apparaat.
* Maak een buitengewone gebruikers ervaring voor uw klanten.

De speech-apparaten SDK verbruikt de [spraak-SDK](speech-sdk.md). De spraak-SDK wordt gebruikt voor het verzenden van de audio die door ons geavanceerde algoritme voor geluids verwerking van de microfoon matrix van het apparaat naar de [spraak Services](overview.md)wordt verwerkt. De oplossing maakt gebruik van meerkanaalse audio om nauw keurigere [spraak herkenning](speech-to-text.md) te bieden via geluids onderdrukking, Echo annulering, beamforming en dereverberation.

U kunt ook de speech apparaten SDK gebruiken om omgevings apparaten te bouwen die uw eigen [aangepaste tref woord](speech-devices-sdk-create-kws.md) hebben, zodat de hint die een gebruikers interactie initieert, uniek is voor uw merk.

De SDK voor spraak apparaten vereenvoudigt het maken van een groot aantal scenario's met spraak mogelijkheden, zoals [spraak assistenten](https://aka.ms/bots/speech/va), bestel systemen voor stations, [conversatie transcriptie](conversation-transcription-service.md)en slimme sprekers. U kunt reageren op gebruikers met tekst, ze weer in een standaard-of [aangepaste stem](how-to-customize-voice-font.md)terugspreken, Zoek resultaten opgeven, [vertalen](speech-translation.md) naar andere talen en meer. We kijken ernaar uit om te zien wat u bouwt.

## <a name="get-the-speech-devices-sdk"></a>De Speech Devices SDK ophalen

### <a name="android"></a>Android

Down load voor Android-apparaten de nieuwste versie van de [Android speech-apparaten SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Voor Windows wordt de voorbeeld toepassing meegeleverd als een cross-platform Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Windows. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

### <a name="linux"></a>Linux

Voor Linux wordt de voorbeeld toepassing meegeleverd als een cross-platform Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9). De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw spraak apparaat kiezen](get-speech-devices-sdk.md)
>
> [!div class="nextstepaction"]
> [Gratis een abonnements sleutel voor spraak Services aanschaffen](get-started.md)
