---
title: Uitwisseling van meerdere deel nemers transcriberen met de Speech SDK-Speech-Service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van de conversatie-transcriptie met de spraak-SDK. Beschikbaar voor C++, C#en Java.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: jhakulin
ms.openlocfilehash: 5ad912b1cee5495e18a5eb2da4d981eadd74dd7d
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066437"
---
# <a name="transcribe-multi-participant-conversations-with-the-speech-sdk"></a>Uitwisseling van meerdere deel nemers transcriberen met de Speech SDK

Met de **ConversationTranscriber** API van de Speech SDK kunt u vergaderingen/gesp rekken transcriberen met de mogelijkheid om deel nemers toe te voegen, te verwijderen en te identificeren `PullStream` door audio te streamen naar de spraak Services met of. `PushStream`

## <a name="limitations"></a>Beperkingen

* De conversatie-transcriber wordt C++ondersteund C#voor, en Java op Windows, Linux en Android.
* De ROOBO DevKit is de ondersteunde hardware-omgeving voor het maken van de conversatie transcripties, omdat deze een circulaire multi-microfoon matrix biedt die efficiënt kan worden gebruikt voor de identificatie van de spreker. [Zie speech apparaten SDK (Engelstalig) voor meer informatie](speech-devices-sdk.md).
* Speech SDK-ondersteuning voor conversatie transcriptie is beperkt tot audio-pull-en push-modus stromen met 8 kanalen van 16-bits 16 kHz PCM-audio. Op dit moment worden alleen deze kits ondersteund voor het vastleggen van 8 kanalen:
   * [ROOBO slimme audio circulaire 7-Mic DK](https://ddk.roobo.com/)
   * [Azure KINECT DK](https://azure.microsoft.com/en-in/services/kinect-dk/).
* De conversatie transcriptie is momenteel beschikbaar in de talen ' nl-US ' en ' zh-CN ' in de volgende regio's: centralus en EastAsia.

## <a name="prerequisites"></a>Vereisten

* [Meer informatie over het gebruik van spraak naar tekst met de spraak-SDK.](quickstart-csharp-dotnet-windows.md)
* [Uw proef abonnement voor spraak downloaden.](https://azure.microsoft.com/try/cognitive-services/)
* Speech SDK-versie 1.5.1 of hoger is vereist.

## <a name="create-voice-signatures-for-participants"></a>Spraak handtekeningen maken voor deel nemers

De eerste stap is het maken van spraak handtekeningen voor de deel nemers aan de conversatie. Het maken van spraak handtekeningen is vereist voor een efficiënte luidspreker-identificatie.

### <a name="requirements-for-input-wave-file"></a>Vereisten voor Wave-invoer bestand

* Het Wave-invoer bestand voor het maken van spraak handtekeningen moet in 16-bits voor beelden, een sample frequentie van 16 kHz en een indeling met één kanaal (mono) zijn.
* De aanbevolen lengte voor elk audio sample ligt tussen 30 seconden en twee minuten.

In het volgende voor beeld ziet u twee verschillende manieren om een spraak handtekening te maken C#met behulp van [de rest API](https://aka.ms/cts/signaturegenservice) van:

```csharp
class Program
{
    static async Task CreateVoiceSignatureByUsingFormData()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var form = new MultipartFormDataContent();
        var content = new ByteArrayContent(fileBytes);
        form.Add(content, "file", "file");
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromFormData", form);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static async Task CreateVoiceSignatureByUsingBody()
    {
        var region = "YourServiceRegion";
        byte[] fileBytes = File.ReadAllBytes(@"speakerVoice.wav");
        var content = new ByteArrayContent(fileBytes);

        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "YourSubscriptionKey");
        var response = await client.PostAsync($"https://signature.{region}.cts.speech.microsoft.com/api/v1/Signature/GenerateVoiceSignatureFromByteArray", content);
        // A voice signature contains Version, Tag and Data key values from the Signature json structure from the Response body.
        // Voice signature format example: { "Version": <Numeric value>, "Tag": "string", "Data": "string" }
        var jsonData = await response.Content.ReadAsStringAsync();
    }

    static void Main(string[] args)
    {
        CreateVoiceSignatureByUsingFormData().Wait();
        CreateVoiceSignatureByUsingBody().Wait();
    }
}
```

## <a name="transcribing-conversations"></a>Transcriberen van discussies

Als u conversaties met meerdere deel nemers wilt `ConversationTranscriber` transcriberen, maakt u het `AudioConfig` object dat is gekoppeld aan het object dat is gemaakt `PullAudioInputStream` voor `PushAudioInputStream`de gespreks sessie en streamen van audio met of.

We gaan ervan uit dat u een ConversationTranscriber-klasse `MyConversationTranscriber`met de naam hebt. Uw code kan er als volgt uitzien:

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Conversation;

public class MyConversationTranscriber
{
    public static async Task ConversationWithPullAudioStreamAsync()
    {
        // Creates an instance of a speech config with specified subscription key and service region.
        // Replace with your own subscription key and region.
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
        var stopTranscription = new TaskCompletionSource<int>();

        // Create an audio stream from a wav file.
        // Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
        using (var audioInput = Helper.OpenWavFile(@"8channelsOfRecordedPCMAudio.wav"))
        {
            // Creates a conversation transcriber using audio stream input.
            using (var transcriber = new ConversationTranscriber(config, audioInput))
            {
                // Subscribes to events.
                transcriber.Recognizing += (s, e) =>
                {
                    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                };

                transcriber.Recognized += (s, e) =>
                {
                    if (e.Result.Reason == ResultReason.RecognizedSpeech)
                    {
                        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}, UserID={e.Result.UserId}");
                    }
                    else if (e.Result.Reason == ResultReason.NoMatch)
                    {
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                    }
                };

                transcriber.Canceled += (s, e) =>
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

                transcriber.SessionStarted += (s, e) =>
                {
                    Console.WriteLine("\nSession started event.");
                };

                transcriber.SessionStopped += (s, e) =>
                {
                    Console.WriteLine("\nSession stopped event.");
                    Console.WriteLine("\nStop recognition.");
                    stopTranscription.TrySetResult(0);
                };

                // Sets a conversation Id.
                transcriber.ConversationId = "AConversationFromTeams";

                // Add participants to the conversation.
                // Create voice signatures using REST API described in the earlier section in this document.
                // Voice signature needs to be in the following format:
                // { "Version": <Numeric value>, "Tag": "string", "Data": "string" }

                var speakerA = Participant.From("Speaker_A", "en-us", signatureA);
                var speakerB = Participant.From("Speaker_B", "en-us", signatureB);
                var speakerC = Participant.From("SPeaker_C", "en-us", signatureC);
                transcriber.AddParticipant(speakerA);
                transcriber.AddParticipant(speakerB);
                transcriber.AddParticipant(speakerC);

                // Starts transcribing of the conversation. Uses StopTranscribingAsync() to stop transcribing when all participants leave.
                await transcriber.StartTranscribingAsync().ConfigureAwait(false);

                // Waits for completion.
                // Use Task.WaitAny to keep the task rooted.
                Task.WaitAny(new[] { stopTranscription.Task });

                // Stop transcribing the conversation.
                await transcriber.StopTranscribingAsync().ConfigureAwait(false);

                // Ends the conversation.
                await transcriber.EndConversationAsync().ConfigureAwait(false);
            }
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
