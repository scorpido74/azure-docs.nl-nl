---
title: Asynchrone gesprekstranscriptie (voorbeeld) - Spraakservice
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van asynchrone gesprekstranscriptie met behulp van de spraakservice. Alleen beschikbaar voor Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: c1f0110c83eb42aaedbd36736946ae3faff58699
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80366617"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Asynchrone gesprekstranscriptie (voorbeeld)

In dit artikel wordt asynchrone gesprekstranscriptie gedemonstreerd met behulp van de **RemoteConversationTranscriptionClient** API. Als u Gesprekstranscriptie hebt geconfigureerd om asynchrone transcriptie uit te voeren en een `conversationId`, u de transcriptie verkrijgen die aan die `conversationId` is gekoppeld met behulp van de Api voor **RemoteConversationTranscriptionClient.**

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroon vs. real-time + asynchroon

Met asynchrone transcriptie streamt u de gespreksaudio, maar hebt u geen transcriptie nodig die in realtime wordt geretourneerd. In plaats daarvan, nadat de `conversationId` `Conversation` audio is verzonden, gebruik de van om query voor de status van de asynchrone transcriptie. Wanneer de asynchrone transcriptie klaar is, krijgt u een `RemoteConversationTranscriptionResult`.

Met real-time plus asynchrone, krijg je de transcriptie in real time, maar ook de transcriptie door te vragen met de `conversationId` (vergelijkbaar met asynchrone scenario).

Er zijn twee stappen nodig om asynchrone transcriptie te bereiken. De eerste stap is het uploaden van de audio, het kiezen van asynchrone alleen of real-time plus asynchroon. De tweede stap is om de transcriptie resultaten te krijgen.

## <a name="upload-the-audio"></a>De audio uploaden

Voordat asynchrone transcriptie kan worden uitgevoerd, moet u de audio naar Conversation Transcription Service verzenden met behulp van Microsoft Cognitive Speech client SDK (versie 1.8.0 of hoger).

In deze voorbeeldcode ziet u hoe u gesprekstranscriber maakt voor de asynchrone modus. Om audio naar de transcriber te streamen, moet u audiostreamingcode toevoegen die is afgeleid van [Transcribe-gesprekken in realtime met de Speech SDK.](how-to-use-conversation-transcription-service.md) Raadpleeg het gedeelte **Beperkingen** van dat onderwerp om de ondersteunde API's en talen te bekijken.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speechConfig.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// pick a conversation Id that is a GUID.
String conversationId = UUID.randomUUID().toString();

// Create a Conversation
Future<Conversation> conversationFuture = Conversation.createConversationAsync(speechConfig, conversationId);
Conversation conversation = conversationFuture.get();

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16kHz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16kHz, 16 bits and 8 channel pcm wav file
AudioStreamFormat audioStreamFormat = AudioStreamFormat.getWaveFormatPCM((long)16000, (short)16,(short)8);
// Create an input stream
AudioInputStream audioStream = AudioInputStream.createPullStream(wavfilePullStreamCallback, audioStreamFormat);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromStreamInput(audioStream));

// join a conversation
transcriber.joinConversationAsync(conversation);

// Add the event listener for the realtime events
transcriber.transcribed.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber Recognized:" + e.toString());
});

transcriber.canceled.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber canceled:" + e.toString());
    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

transcriber.sessionStopped.addEventListener((o, e) -> {
    System.out.println("Conversation transcriber stopped:" + e.toString());

    try {
        transcriber.stopTranscribingAsync().get();
    } catch (InterruptedException ex) {
        ex.printStackTrace();
    } catch (ExecutionException ex) {
        ex.printStackTrace();
    }
});

// start the transcription.
Future<?> future = transcriber.startTranscribingAsync();
...
```

Als u wilt dat real-time _plus_ asynchrone, commentaar en uncomment de juiste regels code als volgt:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Transcriptieresultaten

Deze stap krijgt de asynchrone transcriptie resultaten, maar gaat ervan uit dat alle real-time verwerking die u nodig zou kunnen hebben wordt elders gedaan. Zie [Gesprekken transcriberen in realtime met de Speech SDK](how-to-use-conversation-transcription-service.md)voor meer informatie.

Voor de hier getoonde code hebt u **versie 1.8.0 op afstand**nodig, alleen ondersteund voor Java (1.8.0 of hoger) op Windows, Linux en Android (API-niveau 26 of hoger).

### <a name="obtaining-the-client-sdk"></a>Het verkrijgen van de client SDK

U **een extern gesprek** verkrijgen door uw pom.xml-bestand als volgt te bewerken.

1. Maak aan het einde van het `</project>`bestand, `repositories` vóór de afsluitende tag, een element met een verwijzing naar de Maven-opslagplaats voor de SpraakSDK:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Voeg ook `dependencies` een element toe, met de remoteconversation-client-sdk 1.8.0 als afhankelijkheid:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. De wijzigingen opslaan

### <a name="sample-transcription-code"></a>Voorbeeld transcriptiecode

Nadat u `conversationId`de externe gesprekstranscriptieclient **RemoteConversationTranscriptionClient** hebt gemaakt bij de clienttoepassing om de status van de asynchrone transcriptie op te vragen. Gebruik **de methode getTranscriptionOperation** in **RemoteConversationTranscriptionClient** om een [PollerFlux-object](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) te krijgen. Het object PollerFlux heeft informatie over de externe bewerkingsstatus **RemoteConversationTranscriptionOperation** en het uiteindelijke resultaat **RemoteConversationTranscriptionResult**. Zodra de bewerking is voltooid, krijgt **u RemoteConversationTranscriptionResult** door **getFinalResult** op een [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java)te bellen. In deze code printen we gewoon de resultaatinhoud af op systeemuitvoer.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Get the PollerFlux for the remote operation
PollerFlux<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> remoteTranscriptionOperation = client.getTranscriptionOperation(conversationId);

// Subscribe to PollerFlux to get the remote operation status
remoteTranscriptionOperation.subscribe(
        pollResponse -> {
            System.out.println("Poll response status : " + pollResponse.getStatus());
            System.out.println("Poll response status : " + pollResponse.getValue().getServiceStatus());
        }
);

// Obtain the blocking operation using getSyncPoller
SyncPoller<RemoteConversationTranscriptionOperation, RemoteConversationTranscriptionResult> blockingOperation =  remoteTranscriptionOperation.getSyncPoller();

// Wait for the operation to finish
blockingOperation.waitForCompletion();

// Get the final result response
RemoteConversationTranscriptionResult resultResponse = blockingOperation.getFinalResult();

// Print the result
if(resultResponse != null) {
    if(resultResponse.getConversationTranscriptionResults() != null) {
        for (int i = 0; i < resultResponse.getConversationTranscriptionResults().size(); i++) {
            ConversationTranscriptionResult result = resultResponse.getConversationTranscriptionResults().get(i);
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult.name()));
            System.out.println(result.getProperties().getProperty(PropertyId.SpeechServiceResponse_JsonResult));
            System.out.println(result.getOffset());
            System.out.println(result.getDuration());
            System.out.println(result.getUserId());
            System.out.println(result.getReason());
            System.out.println(result.getResultId());
            System.out.println(result.getText());
            System.out.println(result.toString());
        }
    }
}

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
