---
services: cognitive-services
author: ralphe
manager: cpoulain
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/04/2020
ms.author: ralphe
ms.openlocfilehash: 74aa4d62490778ba13a085d62264b7791112504e
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376900"
---
## <a name="prerequisites"></a>Vereisten

Voordat u aan de slag gaat, moet u het volgende doen:

> [!div class="checklist"]
> * [Een resource voor de Azure-spraakservice maken](../../../../overview.md#try-the-speech-service-for-free)
> * [Uw ontwikkelomgeving instellen en een leeg project maken](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

## <a name="add-sample-code"></a>Voorbeeldcode toevoegen

1. Open vanuit Visual Studio het bronbestand **helloworld.cpp**.

1. Vervang alle code door het volgende codefragment:

    ```cpp
    #include "pch.h"
    
    #define WIN32_LEAN_AND_MEAN
    #include <Windows.h>
    
    #include <iostream>
    #include <thread>
    #include <future>
    #include <string>
    #include <speechapi_cxx.h>
    
    using namespace std::chrono_literals;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Audio;
    using namespace Microsoft::CognitiveServices::Speech::Transcription;
    
    // Create a promise we will set when the user presses Ctrl-C
    std::promise<bool> promise;
    // Create the future we will use to wait for the promise to be fulfilled
    std::future<bool> future = promise.get_future();
    
    void StartNewConversation()
    {
        // Set these
        std::string subscriptionKey("YourSubscriptionKey");

        // Replace below with your own region identifier from here: https://aka.ms/speech/sdkregion
        std::string region("YourServiceRegion");
        
        std::string speechLanguage("en-US");
    
        // Create the conversation object you'll need to manage the conversation
        auto speechConfig = SpeechConfig::FromSubscription(subscriptionKey, region);
        speechConfig->SetSpeechRecognitionLanguage(speechLanguage);
        auto conversation = Conversation::CreateConversationAsync(speechConfig).get();
    
        // Start the conversation so you and others can join
        conversation->StartConversationAsync().get();
    
        // Get the conversation ID. It will be up to your scenario to determine how this is shared with other participants.
        std::cout << "Created conversation: " << conversation->GetConversationId() << std::endl;
    
        // You can now call various commands to manage the room. For example:
        conversation->MuteAllParticipantsAsync().get();
    
        // Create the conversation translator you'll need to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Add any event handlers
        conversationTranslator->SessionStarted += [](const SessionEventArgs& args)
        {
            std::cout << "Session started " << args.SessionId << std::endl;
        };
        conversationTranslator->SessionStopped += [](const SessionEventArgs& args)
        {
            std::cout << "Session stopped " << args.SessionId << std::endl;
        };
        conversationTranslator->Canceled += [](const ConversationTranslationCanceledEventArgs& args)
        {
            switch (args.Reason)
            {
                case CancellationReason::EndOfStream:
                    std::cout << "End of audio reached" << std::endl;
                    break;
    
                case CancellationReason::Error:
                    std::cout << "Canceled due to error. " << (long)args.ErrorCode << ": " << args.ErrorDetails << std::endl;
                    break;
            }
        };
        conversationTranslator->ConversationExpiration += [](const ConversationExpirationEventArgs& args)
        {
            std::cout << "Conversation will expire in " << args.ExpirationTime.count() << " minutes" << std::endl;
        };
        conversationTranslator->ParticipantsChanged += [](const ConversationParticipantsChangedEventArgs& args)
        {
            std::cout << "The following participant(s) have ";
            switch (args.Reason)
            {
                case ParticipantChangedReason::JoinedConversation:
                    std::cout << "joined";
                    break;
    
                case ParticipantChangedReason::LeftConversation:
                    std::cout << "left";
                    break;
    
                case ParticipantChangedReason::Updated:
                    std::cout << "been updated";
                    break;
            }
    
            std::cout << ":" << std::endl;
    
            for(std::shared_ptr<Participant> participant : args.Participants)
            {
                std::cout << "\t" << participant->DisplayName << std::endl;
            }
        };
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation so you can start receiving events
        conversationTranslator->JoinConversationAsync(conversation, "Test Host").get();
    
        // Send an instant message
        conversationTranslator->SendTextMessageAsync("This is a short test message").get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Leave the conversation. You will stop receiving events
        conversationTranslator->LeaveConversationAsync().get();
    
        // Once you are done, remember to delete the conversation.
        conversation->EndConversationAsync().get(); // You will not be able to rejoin after this
        conversation->DeleteConversationAsync().get(); // All participants still in the room will be ejected
    }
    
    int main()
    {
        // Register a handler for the Ctrl - C callback
        SetConsoleCtrlHandler(
            [](DWORD dwCtrlType) -> BOOL
            {
                if (dwCtrlType == CTRL_C_EVENT)
                {
                    // Signal that the user has pressed ctrl + C
                    promise.set_value(true);
                    return TRUE;
                }
    
                return FALSE;
            },
            TRUE);
    
        StartNewConversation();
    }
    ```

1. Vervang in hetzelfde bestand de tekenreeks `YourSubscriptionKey` door uw abonnementssleutel.

1. Vervang de tekenreeks `YourServiceRegion` door de [regio](~/articles/cognitive-services/Speech-Service/regions.md) die aan uw abonnement is gekoppeld.

1. Kies in de menubalk **Bestand** > **Alles opslaan** om de wijzigingen op te slaan.

## <a name="build-and-run-the-application-to-create-a-new-conversation"></a>De toepassing compileren en uitvoeren om een nieuw gesprek te maken

1. Selecteer in de menubalk **Build** > **Build Solution** om de toepassing te compileren. De code moet nu zonder fouten worden gecompileerd.

1. Kies **Fouten opsporen** > **Foutopsporing starten** (of druk op <kbd>F5</kbd>) om de toepassing **helloworld** te starten.

1. Zodra het bericht `Started transcribing` wordt weergegeven, kunt u beginnen met spreken. U ziet dat de transcripties worden weergegeven terwijl u spreekt
    - Als u de gesprekscode deelt met de anderen en zij ook aan het gesprek deelnemen, ziet u ook hun transcripties.

1. Als u klaar bent met spreken, drukt u op <kbd>Ctrl+C</kbd> op uw toetsenbord om het vastleggen van audio te stoppen.

    > [!NOTE]
    > In Visual Studio ziet u mogelijk een bericht over een uitzondering, die lijkt op het volgende: `Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` U kunt dit gewoon negeren.
    > <br/> <br/>
    > Druk op <kbd>F5</kbd> om verder te gaan.

## <a name="build-and-run-the-application-to-join-an-existing-conversation"></a>De toepassing compileren en uitvoeren om deel te nemen aan een bestaand gesprek

1. Kopieer en plak de volgende functie in uw **helloworld.cpp**, net vóór de functie `int main()`:

    ```cpp
    void JoinExistingConversation(const std::string& conversationId)
    {
        std::string speechLanguage("en-US");
    
        // You'll now need to create a ConversationTranslator to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Attach event handlers here. For example:
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation
        conversationTranslator->JoinConversationAsync(conversationId, "participant", speechLanguage).get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Once you are done, leave the conversation
        conversationTranslator->LeaveConversationAsync().get();
    }
    ```

2. Vervang `StartNewConversation();` in uw functie `int main()` door:

    ```cpp
    // Set this to the conversation you want to join
    JoinExistingConversation("YourConversationId");
    ```

[!INCLUDE [create-from-web](../create-from-web.md)]

4. Ga terug naar Visual Studio en vervang `YourConversationId` in uw functie `int main()` door de gespreks-id van de vorige stap.

5. Selecteer in de menubalk **Build** > **Build Solution** om de toepassing te compileren. De code moet zonder fouten worden gecompileerd.

6. Kies **Fouten opsporen** > **Foutopsporing starten** (of druk op <kbd>F5</kbd>) om de toepassing **helloworld** te starten.

7. Zodra het bericht `Started transcribing` wordt weergegeven, kunt u beginnen met spreken. U ziet dat de transcripties worden weergegeven terwijl u spreekt.
    - Als u teruggaat naar uw browser, ziet u dat uw transcripties daar ook worden weergegeven terwijl u spreekt.

8.  Als u klaar bent met spreken, drukt u op <kbd>Ctrl+C</kbd> om het vastleggen van audio te stoppen en het gesprek te beëindigen.

    > [!NOTE]
    > In Visual Studio ziet u mogelijk een bericht over een uitzondering, die lijkt op het volgende: `Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` U kunt dit gewoon negeren.
    > <br/> <br/>
    > Druk op <kbd>F5</kbd> om verder te gaan.

9. Ga terug naar uw browser en sluit het gesprek af met behulp van de afsluitknop in de rechterbovenhoek.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [footer](./footer.md)]
