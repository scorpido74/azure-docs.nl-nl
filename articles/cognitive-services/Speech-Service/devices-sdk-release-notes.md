---
title: SDK-documentatie voor spraakapparaten
titleSuffix: Azure Cognitive Services
description: De release notes bieden een logboek van updates, verbeteringen, bug fixes en wijzigingen in de Speech Devices SDK. Dit artikel wordt bijgewerkt bij elke release van de Speech Devices SDK.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: wellsi
ms.openlocfilehash: 29dcb3c0e74482fd6670d1a0983e751043379d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371622"
---
# <a name="release-notes-speech-devices-sdk"></a>Release notes: Speech Devices SDK

In de volgende secties worden wijzigingen weergegeven in de meest recente versies.

## <a name="speech-devices-sdk-190"></a>Spraakapparaten SDK 1.9.0:

- De eerste binaire bestanden voor [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter) (Linux ARM64) zijn aanwezig.
- Roobo v1 gebruikt Maven nu voor de Speech SDK
- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.9.0. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-170"></a>Spraakapparaten SDK 1.7.0:

- Linux ARM wordt nu ondersteund.
- De eerste binaries voor [Roobo v2 DDK](https://aka.ms/sdsdk-download-roobov2) zijn aanwezig (Linux ARM64).
- Windows-gebruikers `AudioConfig.fromDefaultMicrophoneInput()` kunnen `AudioConfig.fromMicrophoneInput(deviceName)` de te gebruiken microfoon gebruiken of opgeven.
- De grootte van de bibliotheek is geoptimaliseerd.
- Ondersteuning voor multi-turn herkenning met hetzelfde object voor spraakherkenning/intentieherkenning.
- Fix af en toe hangen dat zou optreden tijdens het stoppen van de erkenning.
- Voorbeeld-apps bevatten nu een voorbeeld van deelnemers.properties-bestand om de indeling van het bestand weer te geven.
- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.7.0. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-160"></a>Spraakapparaten SDK 1.6.0:

- [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/) op Windows en Linux ondersteunen met algemene [voorbeeldtoepassing](https://aka.ms/sdsdk-download)
- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.6.0. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-151"></a>Spraakapparaten SDK 1.5.1:

- [Gesprekstranscriptie](conversation-transcription-service.md) opnemen in de voorbeeld-app.
- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.5.1. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-150-2019-may-release"></a>Spraakapparaten SDK 1.5.0: release 2019-mei

- Spraakapparaten SDK is nu GA en niet langer een gated preview.
- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.5.0. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).
- Nieuwe zoekwoordtechnologie brengt aanzienlijke kwaliteitsverbeteringen met zich mee, zie Breaking Changes.
- Nieuwe audioverwerkingspijplijn voor verbeterde vervelding.

**Wijzigingen doorbreken**

- Dankzij de nieuwe trefwoordtechnologie moeten alle zoekwoorden opnieuw worden gemaakt op onze verbeterde trefwoordportal. Als u oude zoekwoorden volledig van het apparaat wilt verwijderen, verwijdert u de oude app.
  - adb verwijderen com.microsoft.cognitiveservices.speech.samples.sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Spraakapparaten SDK 1.4.0: release 2019-Apr

- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.4.0. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Spraakapparaten SDK 1.3.1: release 2019-maart

- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.3.1. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).
- Bijgewerkte trefwoordverwerking, zie Wijzigingen verbreken.
- Voorbeeldtoepassing voegt taalkeuze toe voor zowel spraakherkenning als vertaling.

**Wijzigingen doorbreken**

- [Het installeren van een trefwoord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) is vereenvoudigd, het is nu onderdeel van de app en heeft geen aparte installatie op het apparaat nodig.
- De trefwoordherkenning is gewijzigd en twee gebeurtenissen worden ondersteund.
  - `RecognizingKeyword,`geeft het spraakresultaat (niet-geverifieerde) trefwoordtekst aan.
  - `RecognizedKeyword`, geeft aan dat trefwoordherkenning is voltooid door het opgegeven trefwoord te herkennen.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Spraakapparaten SDK 1.1.0: release 2018-nov

- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.1.0. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).
- Far Field Spraakherkenning nauwkeurigheid is verbeterd met onze verbeterde audio-processing algoritme.
- Voorbeeldtoepassing heeft ondersteuning voor Chinese spraakherkenning toegevoegd.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Spraakapparaten SDK 1.0.1: release 2018-okt

- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 1.0.1. Zie voor meer informatie de [releasenotes](https://aka.ms/csspeech/whatsnew).
- De nauwkeurigheid van spraakherkenning wordt verbeterd met ons verbeterde audioverwerkingsalgoritme
- Een continue herkenning audio sessie bug is opgelost.

**Wijzigingen doorbreken**

- Met deze release worden een aantal baanbrekende wijzigingen geïntroduceerd. Kijk op [deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie over de API's.
- De KWS-modelbestanden zijn niet compatibel met Spraakapparaten SDK 1.0.1. De bestaande trefwoordbestanden worden verwijderd nadat de nieuwe trefwoordbestanden naar het apparaat zijn geschreven.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Spraakapparaten SDK 0,5.0: release 2018-aug

- Verbeterde de nauwkeurigheid van spraakherkenning door het oplossen van een bug in de audioverwerkingscode.
- De [spraaksdkcomponent](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) bijgewerkt naar versie 0.5.0. Zie voor meer informatie de [releasenotes](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release).

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Spraakapparaten SDK 0,2.12733: release 2018-mei

De eerste openbare preview release van de Cognitive Services Speech Devices SDK.
