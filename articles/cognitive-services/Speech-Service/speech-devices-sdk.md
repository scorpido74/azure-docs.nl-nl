---
title: Speech-apparaten SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Aan de slag met de SDK voor spraak apparaten. De speech-service werkt met een groot aantal verschillende apparaten en audio bronnen. De SDK voor spraak apparaten is een vooraf afgestemde bibliotheek die is gekoppeld aan speciaal ontwikkelde, ontwikkel Kits voor microfoon matrices.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79370641"
---
# <a name="what-is-the-speech-devices-sdk"></a>Wat is de SDK voor spraak apparaten?

De [Speech-Service](overview.md) werkt met een groot aantal verschillende apparaten en audio bronnen. Nu kunt u uw spraak toepassingen naar een hoger niveau brengen met overeenkomende hardware en software. De SDK voor spraak apparaten is een vooraf afgestemde bibliotheek die is gekoppeld aan speciaal ontwikkelde, ontwikkel Kits voor microfoon matrices.

De SDK voor spraak apparaten kan u helpen bij het volgende:

- Test nieuwe spraak scenario's snel.
- Integreer de spraak service op basis van de Cloud eenvoudiger in uw apparaat.
- Maak een buitengewone gebruikers ervaring voor uw klanten.

De speech-apparaten SDK verbruikt de [spraak-SDK](speech-sdk.md). Met onze geavanceerde algoritmen voor geluids verwerking met de microfoon matrix van het apparaat kunt u de audio naar de [Speech-Service](overview.md)verzenden. Het biedt nauw keurige [spraak herkenning](speech-to-text.md) via ruis, Echo annulering, beamforming en dereverberation.

U kunt ook de speech apparaten SDK gebruiken om omgevings apparaten te bouwen die uw eigen [aangepaste sleutel woord](speech-devices-sdk-create-kws.md)hebben. Een aangepast tref woord biedt een hint waarmee een gebruikers interactie wordt gestart die uniek is voor uw merk.

De SDK voor spraak apparaten maakt een groot aantal scenario's met spraak functionaliteit mogelijk, zoals [spraak assistenten](https://aka.ms/bots/speech/va), bestel systemen voor stations, [conversatie transcriptie](conversation-transcription-service.md)en slimme sprekers. U kunt reageren op gebruikers met tekst, ze weer in een standaard-of [aangepaste stem](how-to-customize-voice-font.md)terugspreken, Zoek resultaten opgeven, [vertalen](speech-translation.md) naar andere talen en meer. We kijken ernaar uit om te zien wat u bouwt.

## <a name="get-the-speech-devices-sdk"></a>De Speech Devices SDK ophalen

### <a name="android"></a>Android

Voor Android downloaden apparaten de nieuwste versie van de [Android speech-apparaten SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Voor Windows wordt de voorbeeld toepassing meegeleverd als een platformoverschrijdende Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Windows. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

### <a name="linux"></a>Linux

Voor Linux wordt de voorbeeld toepassing meegeleverd als een cross-platform Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

Er zijn extra binaire bestanden ter ondersteuning van aanstaande apparaten, [roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)en [GGEC spreker](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw spraak apparaat kiezen](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
