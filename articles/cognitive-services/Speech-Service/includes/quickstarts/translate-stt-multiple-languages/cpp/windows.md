---
title: 'Quickstart: Spraak omzetten naar meerdere talen, C++ (Windows) - Spraakservice'
titleSuffix: Azure Cognitive Services
description: NOG TE BEPALEN
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: erhopf
ms.openlocfilehash: 9f2988f736886734a2de5e0a895f878bc9965b38
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226200"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure Speech-service maken](../../../../get-started.md)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende codefragment:

   ```C++
   #include <iostream>
   #include <vector>
   #include <speechapi_cxx.h>

   using namespace std;
   using namespace Microsoft::CognitiveServices::Speech;
   using namespace Microsoft::CognitiveServices::Speech::Translation;

   void TranslateSpeechToText()
   {
       // Creates an instance of a speech translation config with specified subscription key and service region.
       // Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
       auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

       // Sets source and target languages.
       // Replace with the languages of your choice, from list found here: https://aka.ms/speech/sttt-languages
       auto fromLanguage = "en-US";
       config->SetSpeechRecognitionLanguage(fromLanguage);
       config->AddTargetLanguage("de");
       config->AddTargetLanguage("fr");

       // Creates a translation recognizer using the default microphone audio input device.
       auto recognizer = TranslationRecognizer::FromConfig(config);

       // Starts translation, and returns after a single utterance is recognized. The end of a
       // single utterance is determined by listening for silence at the end or until a maximum of 15
       // seconds of audio is processed. The task returns the recognized text as well as the translation.
       // Note: Since RecognizeOnceAsync() returns only a single utterance, it is suitable only for single
       // shot recognition like command or query.
       // For long-running multi-utterance recognition, use StartContinuousRecognitionAsync() instead.
       cout << "Say something...\n";
       auto result = recognizer->RecognizeOnceAsync().get();

       // Checks result.
       if (result->Reason == ResultReason::TranslatedSpeech)
       {
           cout << "RECOGNIZED '" << fromLanguage << "': " << result->Text << std::endl;
           for (const auto& it : result->Translations)
           {
               cout << "TRANSLATED into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
           }
       }
       else if (result->Reason == ResultReason::RecognizedSpeech)
       {
           cout << "RECOGNIZED '" << fromLanguage << "' " << result->Text << " (text could not be translated)" << std::endl;
       }
       else if (result->Reason == ResultReason::NoMatch)
       {
           cout << "NOMATCH: Speech could not be recognized." << std::endl;
       }
       else if (result->Reason == ResultReason::Canceled)
       {
           auto cancellation = CancellationDetails::FromResult(result);
           cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

           if (cancellation->Reason == CancellationReason::Error)
           {
               cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
               cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
               cout << "CANCELED: Did you update the subscription info?" << std::endl;
           }
       }
   }

   int wmain()
   {
       TranslateSpeechToText();
       return 0;
   }
   ```

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld.

1. Kies in de menubalk **Bestand** > **Alles opslaan** om de wijzigingen op te slaan.

## <a name="build-and-run-the-application"></a>De toepassing bouwen en uitvoeren.

1. Selecteer in de menubalk **Build** > **Build Solution** om de toepassing te compileren. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Fouten opsporen** > **Foutopsporing starten** (of druk op **F5**) om de toepassing **helloworld** te starten.

1. Spreek een Engelse woordgroep of zin in. De toepassing stuurt uw spraak naar de Speech-service, waar deze wordt vertaald en getranscribeerd in tekst (in dit geval in het Frans en het Duits). De Speech-service zendt de tekst vervolgens terug naar de toepassing voor weergave.

````
Say something...
RECOGNIZED 'en-US': What's the weather in Seattle?
TRANSLATED into 'de': Wie ist das Wetter in Seattle?
TRANSLATED into 'fr': Quel temps fait-il à Seattle ?
````

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
