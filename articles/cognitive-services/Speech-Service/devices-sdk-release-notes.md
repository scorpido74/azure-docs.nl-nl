---
title: SDK-documentatie voor speech-apparaten
titleSuffix: Azure Cognitive Services
description: De release opmerkingen bieden een logboek van updates, verbeteringen, fout oplossingen en wijzigingen aan de SDK voor spraak apparaten. Dit artikel wordt bijgewerkt met elke release van de SDK voor spraak apparaten.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: wellsi
ms.openlocfilehash: 9421f730ea6480c9e4223dd9ddbd15852b1fce8f
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072603"
---
# <a name="release-notes-speech-devices-sdk"></a>Release opmerkingen: SDK voor spraak apparaten

De volgende secties bevatten een lijst met wijzigingen in de meest recente releases.

## <a name="speech-devices-sdk-160"></a>Speech-apparaten SDK 1.6.0:

- Ondersteuning voor [Azure KINECT DK](https://azure.microsoft.com/services/kinect-dk/) op Windows en Linux met een gemeen schappelijke [voorbeeld toepassing](https://aka.ms/sdsdk-download)
- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.6.0. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.

## <a name="speech-devices-sdk-151"></a>Speech-apparaten SDK 1.5.1:

- Neem de [conversatie-transcriptie](conversation-transcription-service.md) op in de voor beeld-app.
- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.5.1. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.

## <a name="speech-devices-sdk-150-2019-may-release"></a>Speech-apparaten SDK 1.5.0:2019-mei release

- De SDK voor spraak apparaten is nu beschikbaar en is niet langer een geteste preview.
- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.5.0. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.
- Nieuwe trefwoord technologie brengt belang rijke verbeteringen met zich mee.
- Nieuwe pijp lijn voor de verwerking van audio voor verbeterde herkenning van het ver-veld.

**Belangrijke wijzigingen**

- Vanwege de nieuwe trefwoord technologie moeten alle tref woorden opnieuw worden gemaakt in onze verbeterde trefwoord Portal. Oude tref woorden volledig verwijderen van het apparaat verwijderen van de oude app.
  - ADB uninstall com. micro soft. coginitiveservices. speech. samples. sdsdkstarterapp

## <a name="speech-devices-sdk-140-2019-apr-release"></a>Speech-apparaten SDK 1.4.0:2019-apr release

- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.4.0. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.

## <a name="speech-devices-sdk-131-2019-mar-release"></a>Speech-apparaten SDK 1.3.1:2019-mrt release

- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.3.1. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.
- De verwerking van tref woorden is bijgewerkt, zie wijzigingen afbreken.
- Voorbeeld toepassing voegt taal keuze voor zowel spraak herkenning als omzetting toe.

**Belangrijke wijzigingen**

- Het [installeren van een tref woord](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) is vereenvoudigd, het maakt nu deel uit van de app en vereist geen afzonderlijke installatie op het apparaat.
- De herkenning van het tref woord is gewijzigd en twee gebeurtenissen worden ondersteund.
  - RecognizingKeyword: Hiermee wordt aangegeven dat het resultaat van de spraak (niet-geverifieerde) trefwoord tekst bevat.
  - RecognizedKeyword geeft aan dat de trefwoord herkenning het opgegeven tref woord heeft herkend.

## <a name="speech-devices-sdk-110-2018-nov-release"></a>Speech-apparaten SDK 1.1.0:2018-nov release

- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.1.0. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.
- De nauw keurigheid van spraak herkenning in het verre veld is verbeterd met ons uitgebreide algoritme voor geluids verwerking.
- Voorbeeld toepassing toegevoegd Chinese spraak herkenning ondersteuning.

## <a name="speech-devices-sdk-101-2018-oct-release"></a>Speech-apparaten SDK 1.0.1:2018-okt release

- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 1.0.1. Zie de [release opmerkingen](https://aka.ms/csspeech/whatsnew)voor meer informatie.
- Nauw keurigheid van spraak herkenning is verbeterd met onze verbeterde algoritme voor geluids verwerking
- Er is een probleem met een continue opname van audio sessie opgelost.

**Belangrijke wijzigingen**

- Met deze release zijn een aantal belangrijke wijzigingen worden geïntroduceerd. Raadpleeg [Deze pagina](https://aka.ms/csspeech/breakingchanges_1_0_0) voor meer informatie over de api's.
- De KWS-model bestanden zijn niet compatibel met Speech-apparaten SDK 1.0.1. De bestaande trefwoord bestanden worden verwijderd nadat de nieuwe trefwoord bestanden naar het apparaat zijn geschreven.

## <a name="speech-devices-sdk-050-2018-aug-release"></a>Speech-apparaten SDK 0.5.0:2018-aug release

- De nauw keurigheid van spraak herkenning is verbeterd door een fout in de code voor audio verwerking te corrigeren.
- Het [Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) -onderdeel is bijgewerkt naar versie 0.5.0. Zie de [release opmerkingen](releasenotes.md#cognitive-services-speech-sdk-050-2018-july-release)voor meer informatie.

## <a name="speech-devices-sdk-0212733-2018-may-release"></a>Speech-apparaten SDK 0.2.12733:2018-mei release

De eerste open bare preview-versie van de SDK voor Cognitive Services speech-apparaten.
