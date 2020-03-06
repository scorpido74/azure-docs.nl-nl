---
title: Over de speech-apparaten SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Aan de slag met de SDK van de apparaten spraak. De speech-service werkt met een groot aantal verschillende apparaten en audio bronnen. De spraak-apparaten-SDK is een vooraf afgestemd bibliotheek die gekoppeld met speciaal ontwikkelde, microfoon SDK in de matrix.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: erhopf
ms.openlocfilehash: 43d08b466076672587e7f6545193e326283c0031
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330784"
---
# <a name="about-the-speech-devices-sdk"></a>Over de Speech Devices SDK

De [Speech-Service](overview.md) werkt met een groot aantal verschillende apparaten en audio bronnen. Nu kunt u uw spraaktoepassingen-naar een hoger niveau met overeenkomende hardware en software uitvoeren. De spraak-apparaten-SDK is een pretuned bibliotheek die gekoppeld met speciaal ontwikkelde, microfoon SDK in de matrix.

De SDK van de apparaten spraak kan u helpen:

- Snel test nieuwe stem-scenario's.
- Meer de Speech cloud-gebaseerde service voor het eenvoudig integreren in uw apparaat.
- Maak een uitstekende gebruikerservaring voor uw klanten.

De speech-apparaten SDK verbruikt de [spraak-SDK](speech-sdk.md). Met onze geavanceerde algoritmen voor geluids verwerking met de microfoon matrix van het apparaat kunt u de audio naar de [Speech-Service](overview.md)verzenden. Het biedt nauw keurige [spraak herkenning](speech-to-text.md) via ruis, Echo annulering, beamforming en dereverberation.

U kunt ook de speech apparaten SDK gebruiken om omgevings apparaten te bouwen die uw eigen [aangepaste sleutel woord](speech-devices-sdk-create-kws.md)hebben. Een aangepast tref woord biedt een hint waarmee een gebruikers interactie wordt gestart die uniek is voor uw merk.

De SDK voor spraak apparaten maakt een groot aantal scenario's met spraak functionaliteit mogelijk, zoals [spraak assistenten](https://aka.ms/bots/speech/va), bestel systemen voor stations, [conversatie transcriptie](conversation-transcription-service.md)en slimme sprekers. U kunt reageren op gebruikers met tekst, ze weer in een standaard-of [aangepaste stem](how-to-customize-voice-font.md)terugspreken, Zoek resultaten opgeven, [vertalen](speech-translation.md) naar andere talen en meer. We kijken ernaar uit om te zien wat u bouwen!

## <a name="get-the-speech-devices-sdk"></a>De Speech Devices SDK ophalen

### <a name="android"></a>Android

Voor Android downloaden apparaten de nieuwste versie van de [Android speech-apparaten SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Voor Windows wordt de voorbeeld toepassing meegeleverd als een platformoverschrijdende Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Windows. De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

### <a name="linux"></a>Linux

Voor Linux wordt de voorbeeld toepassing meegeleverd als een cross-platform Java-toepassing. Down load de nieuwste versie van de software voor [jre speech-apparaten](https://aka.ms/sdsdk-download-JRE).
De toepassing is gebouwd met het Speech SDK-pakket en de eclips Java IDE (v4) op 64-bits Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). De toepassing wordt uitgevoerd op een 64-bit Java 8 runtime-omgeving (JRE).

Er zijn extra binaire bestanden ter ondersteuning van aanstaande apparaten, [roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2)en [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw spraak apparaat kiezen](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Gratis een abonnements sleutel voor een spraak service ophalen](get-started.md)
