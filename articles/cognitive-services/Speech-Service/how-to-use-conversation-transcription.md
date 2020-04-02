---
title: Realtime gesprekstranscriptie (voorbeeld) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van real-time gesprekstranscriptie met de Speech SDK. Beschikbaar voor C++, C#en Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: weixu
ms.openlocfilehash: e2c9c0aadc8e443f07f60f3ccddb4a1b6dd661b1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520916"
---
# <a name="real-time-conversation-transcription-preview"></a>Realtime gesprekstranscriptie (voorbeeld)

Met de **ConversationTranscriber** API van de Speech SDK u vergaderingen en andere gesprekken transcriberen met de mogelijkheid `PullStream` `PushStream`om meerdere deelnemers toe te voegen, te verwijderen en te identificeren door audio naar de Spraakservice te streamen met of. In dit onderwerp moet u weten hoe u spraak-naar-tekst gebruiken met de SpraakSDK (versie 1.8.0 of hoger). Zie [Wat zijn spraakservices](overview.md)voor meer informatie .

## <a name="limitations"></a>Beperkingen

- De ConversationTranscriber API wordt ondersteund voor C++, C#en Java op Windows, Linux en Android.
- Momenteel beschikbaar in "en-US" en "zh-CN" talen in de volgende regio's: _centralus_ en _eastasia_.
- Vereist een cirkelvormige array met meerdere microfoons met 7 microfoons met een naslagstream voor afspelen. De microfoonarray moet voldoen aan [onze specificatie.](https://aka.ms/sdsdk-microphone)
- De [Speech Devices SDK](speech-devices-sdk.md) biedt geschikte apparaten en een voorbeeld-app die gesprekstranscriptie demonstreert.

## <a name="optional-sample-code-resources"></a>Optionele voorbeeldcodebronnen

De Speech Device SDK biedt voorbeeldcode in Java voor real-time audio-opname met behulp van 8 kanalen.

- [VOORBEELDcode ROOBO-apparaat](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java)
- [Voorbeeldcode azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)

## <a name="prerequisites"></a>Vereisten

Een spraakserviceabonnement. Je [een spraakproefabonnement krijgen](https://azure.microsoft.com/try/cognitive-services/) als je er geen hebt.

## <a name="create-voice-signatures"></a>Spraakhandtekeningen maken

De eerste stap is het maken van spraakhandtekeningen voor de deelnemers aan het gesprek voor een efficiënte luidsprekeridentificatie.

### <a name="audio-input-requirements"></a>Vereisten voor audioinvoer

- Het invoeraudiogolfbestand voor het maken van spraakhandtekeningen moet in 16-bits samples, een 16 kHz-samplesnelheid en een mono-indeling met één kanaal (mono) zijn opgenomen.
- De aanbevolen lengte voor elk audiomonster ligt tussen de dertig seconden en twee minuten.

### <a name="sample-code"></a>Voorbeeldcode

In het volgende voorbeeld worden twee verschillende manieren weergegeven om spraakhandtekening te maken [met behulp van de REST API](https://aka.ms/cts/signaturegenservice) in C#. Houd er rekening mee dat u echte informatie moet vervangen door "YourSubscriptionKey", uw wave-bestandsnaam voor "speakerVoice.wav", en uw regio voor `{region}` en "YourServiceRegion"_(centralus_ of _eastasia)._

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

## <a name="transcribe-conversations"></a>Gesprekken transcriberen

De volgende voorbeeldcode laat zien hoe je gesprekken in realtime transcribeert voor drie sprekers. Het gaat ervan uit dat u al spraakhandtekeningen hebt gemaakt voor elke spreker, zoals hierboven wordt weergegeven. Vervang echte informatie voor "YourSubscriptionKey" en "YourServiceRegion" bij het maken van het Object SpeechConfig.

Hoogtepunten van voorbeeldcode zijn:

- Een `Conversation` object maken `SpeechConfig` van het object met behulp van een vergaderings-id die wordt gegenereerd met`Guid.NewGuid()`
- Een `ConversationTranscriber` object maken en `JoinConversationAsync()` deelnemen aan het gesprek om transcriptie te starten
- Het registreren van de gebeurtenissen van belang
- Deelnemers aan het gesprek toevoegen of verwijderen met het object Gesprek
- De audio streamen
- In Speech SDK-versie 1.9.0 en verder worden beide `int` en `string` waardetypen ondersteund in het veld van de spraakhandtekeningversie.

De transcriptie en luidspreker-id komen terug in de geregistreerde gebeurtenissen.

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
                using (var conversationTranscriber = new ConversationTranscriber    (audioInput))
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
> [Asynchrone gesprekstranscriptie](how-to-async-conversation-transcription.md)
