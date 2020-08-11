---
title: Real-time conversatie transcriptie (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van real-time conversatie-transcriptie met de spraak-SDK. Beschikbaar voor C++, C# en Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: f9670733f8aa040b87cec20ac28aacc636c04698
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056207"
---
# <a name="real-time-conversation-transcription-preview"></a>Real-time conversatie transcriptie (preview-versie)

Met de **ConversationTranscriber** -API van de Speech SDK kunt u vergaderingen en andere gesp rekken transcriberen met de mogelijkheid om meerdere deel nemers toe te voegen, te verwijderen en te identificeren door audio te streamen naar de spraak service met `PullStream` of `PushStream` . In dit onderwerp moet u weten hoe u spraak-naar-tekst kunt gebruiken met de Speech SDK (versie 1.8.0 of hoger). Zie [Wat zijn spraak Services](overview.md)? voor meer informatie.

## <a name="limitations"></a>Beperkingen

- De ConversationTranscriber-API wordt ondersteund voor C++, C# en Java op Windows, Linux en Android.
- Momenteel beschikbaar in de talen "en-US" en "zh-CN" in de volgende regio's: _Midden_ -en _EastAsia_.
- Vereist een matrix met een meerkanaals multi-microfoon met 7-Mic en een referentie stroom voor afspelen. De microfoon matrix moet voldoen aan [onze specificaties](https://aka.ms/sdsdk-microphone).
- De [SDK voor spraak apparaten](speech-devices-sdk.md) biedt geschikte apparaten en een voor beeld-app die conversatie transcriptie demonstreert.

## <a name="optional-sample-code-resources"></a>Optionele voorbeeld code resources

De SDK voor spraak apparaten biedt voorbeeld code in Java voor realtime audio-opname met 8 kanalen.

- [Voorbeeld code van ROOBO-apparaat](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Voorbeeld code van de Azure Kinect dev kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Vereisten

Een spraak service-abonnement. U kunt [een gratis Azure-account](https://azure.microsoft.com/free/cognitive-services/) maken als u er nog geen hebt.

## <a name="create-voice-signatures"></a>Spraak handtekeningen maken

De eerste stap bestaat uit het maken van gesp roken hand tekeningen voor de deel nemers aan de conversatie voor een efficiënte speaker-identificatie.

### <a name="audio-input-requirements"></a>Vereisten voor audio-invoer

- Het Wave-invoer bestand voor het maken van spraak handtekeningen moet de 16-bits steek proeven, een sample frequentie van 16 kHz en een indeling met één kanaal (mono) zijn.
- De aanbevolen lengte voor elk audio voorbeeld ligt tussen dertig seconden en twee minuten.

### <a name="sample-code"></a>Voorbeeldcode

In het volgende voor beeld ziet u twee verschillende manieren om een spraak handtekening te maken met [behulp van de rest API](https://aka.ms/cts/signaturegenservice) in C#. Houd er rekening mee dat u de werkelijke gegevens voor ' YourSubscriptionKey ', uw Wave-bestands naam voor ' speakerVoice. wav ' en uw regio voor `{region}` en ' YourServiceRegion ' (_centralus_ of _EastAsia_) moet vervangen.

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        // Replace with your own region
        var region = "YourServiceRegion";
        // Change the name of the wave file to match yours
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        // Add your subscription key to the header Ocp-Apim-Subscription-Key directly
        // Replace "YourSubscriptionKey" with your own subscription key
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        // Edit with your desired region for `{region}`
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribe-conversations"></a>Conversaties transcriberen

De volgende voorbeeld code laat zien hoe u gesp rekken in realtime kunt vertranscriben voor drie luid sprekers. Hierbij wordt ervan uitgegaan dat u al gesp roken hand tekeningen hebt gemaakt voor elke spreker zoals hierboven wordt weer gegeven. Vervang echte informatie door ' YourSubscriptionKey ' en ' YourServiceRegion ' bij het maken van het SpeechConfig-object.

Voor beelden van code-hooglichten zijn:

- Een `Conversation` object maken op basis van het `SpeechConfig` object met behulp van een vergaderings-id die is gegenereerd met`Guid.NewGuid()`
- Een `ConversationTranscriber` object maken en lid worden van het gesprek met `JoinConversationAsync()` om transcriptie te starten
- De gebeurtenissen van belang registreren
- Deel nemers aan de conversatie toevoegen of verwijderen met behulp van het gespreks object
- Audio streamen
- In spraak-SDK-versie 1.9.0 en hoger `int` worden zowel-als- `string` waardetypen ondersteund in het veld versie van spraak handtekening.

De transcriptie en de spreker-id worden weer gegeven in de geregistreerde gebeurtenissen.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Transcription;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region
        // Replace with your own subscription key and region
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        config.SetProperty("ConversationTranscriptionInRoomAndOnline", "true");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            var meetingId = Guid.NewGuid().ToString();
            using (var conversation = await Conversation.CreateConversationAsync(config, meetingId).ConfigureAwait(false))
            {
                // Create a conversation transcriber using audio stream input
                using (var conversationTranscriber = new ConversationTranscriber(audioInput))
                {
                    await conversationTranscriber.JoinConversationAsync(conversation);

                    // Subscribe to events
                    conversationTranscriber.Transcribing += (s, e) =>
                    {
                            Console.WriteLine($"TRANSCRIBING: Text={e.Result.Text}");
                    };

                    conversationTranscriber.Transcribed += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"TRANSCRIBED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    conversationTranscriber.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");

                        if (e.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                            stopTranscription.TrySetResult(0);
                        }
                    };

                    conversationTranscriber.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\nSession started event.");
                    };

                    conversationTranscriber.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\nSession stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopTranscription.TrySetResult(0);
                    };

                    // Add participants to the conversation.
                    // Create voice signatures using REST API described in the earlier section in this document.
                    // Voice signature needs to be in the following format:
                    // { "Version": <Numeric string or integer value>, "Tag": "string", "Data": "string" }

                    var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                    var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                    var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                    await conversation.AddParticipantAsync(speakerA);
                    await conversation.AddParticipantAsync(speakerB);
                    await conversation.AddParticipantAsync(speakerC);

                    // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                    await conversationTranscriber.StartTranscribingAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopTranscription.Task });

                    // Stop transcribing the conversation.
                    await conversationTranscriber.StopTranscribingAsync().ConfigureAwait(false);
                 }
            }
       }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Asynchrone conversatie transcriptie](how-to-async-conversation-transcription.md)
