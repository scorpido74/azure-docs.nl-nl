---
title: Asynchroon gesprek transcriptie (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het gebruik van asynchrone conversatie-transcriptie met behulp van de speech-service. Alleen beschikbaar voor Java.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
ms.openlocfilehash: 9a2a00a8c76c7b5c02cde623bf93f536b27cf074
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621953"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Asynchrone conversatie transcriptie (preview-versie)

In dit artikel wordt asynchroon gesprek transcriptie gedemonstreerd met behulp van de **RemoteConversationTranscriptionClient** -API. Als u conversatie transcriptie hebt geconfigureerd om asynchrone transcriptie uit te voeren en een `conversationId`hebt, kunt u de transcriptie verkrijgen die is gekoppeld aan die `conversationId` met behulp van de **RemoteConversationTranscriptionClient** -API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroon versus realtime + asynchroon

Met asynchrone transcriptie streamt u de conversatie audio, maar hoeft er in realtime geen transcriptie te worden geretourneerd. Gebruik in plaats daarvan de `conversationId` `Conversation` om de status van de asynchrone transcriptie op te vragen. Wanneer de asynchrone transcriptie klaar is, krijgt u een `RemoteConversationTranscriptionResult`.

Met realtime plus asynchroon krijgt u de transcriptie in realtime, maar krijgt u ook de transcriptie door een query uit te geven op de `conversationId` (vergelijkbaar met asynchroon scenario).

Er zijn twee stappen vereist voor het uitvoeren van asynchrone transcriptie. De eerste stap is het uploaden van de audio, het kiezen van alleen asynchroon of realtime plus asynchroon. De tweede stap bestaat uit het ophalen van de transcriptie-resultaten.

## <a name="upload-the-audio"></a>De audio uploaden

Voordat asynchrone transcriptie kunnen worden uitgevoerd, moet u de audio verzenden naar de conversatie transcriptie-service met behulp van de micro soft cognitieve speech client SDK (versie 1.8.0 of hoger).

Deze voorbeeld code laat zien hoe u een conversatie transcriber kunt maken voor de modus alleen asynchroon. Als u audio naar de transcriber wilt streamen, moet u audio streaming code die is afgeleid van het verzetten van [conversaties in realtime met de spraak-SDK](how-to-use-conversation-transcription-service.md)toevoegen. Raadpleeg de sectie **beperkingen** van het onderwerp voor een overzicht van de ondersteunde platforms en talen api's.

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
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create an audio stream from a wav file or from the default microphone if you want to stream live audio from the supported devices
// Replace with your own audio file name and Helper class which implements AudioConfig using PullAudioInputStreamCallback
PullAudioInputStreamCallback wavfilePullStreamCallback = Helper.OpenWavFile("16Khz16Bits8channelsOfRecordedPCMAudio.wav");
// Create an audio stream format assuming the file used above is 16Khz, 16 bits and 8 channel pcm wav file
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

Als u real-time _plus_ asynchroon en commentaar wilt toevoegen aan de juiste regels code, volgt u de volgende stappen:

```java
// Set the property for asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);
```

## <a name="get-transcription-results"></a>Transcriptie-resultaten ophalen

Met deze stap worden de asynchrone transcriptie-resultaten opgehaald, maar er wordt uitgegaan van een real-time verwerking die u mogelijk op een andere locatie moet uitvoeren. Zie voor meer informatie [telegespreks gesprekken in realtime met de spraak-SDK](how-to-use-conversation-transcription-service.md).

Voor de code die hier wordt weer gegeven, hebt u **versie 1.8.0 van de externe conversatie**nodig, alleen ondersteund voor Java (1.8.0 of hoger) in Windows, Linux en Android (alleen API-niveau 26 of hoger).

### <a name="obtaining-the-client-sdk"></a>De client-SDK verkrijgen

U kunt **communicatie op afstand** verkrijgen door uw pom. XML-bestand als volgt te bewerken.

1. Maak aan het einde van het bestand een `repositories` element met een verwijzing naar de Maven-opslag plaats voor de afsluitende tag `</project>`:

   ```xml
   <repositories>
     <repository>
       <id>maven-cognitiveservices-speech</id>
       <name>Microsoft Cognitive Services Speech Maven Repository</name>
       <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
     </repository>
   </repositories>
   ```

2. Voeg ook een `dependencies`-element toe, met de remoteconversation-client-SDK 1.8.0 als afhankelijkheid:

   ```xml
   <dependencies>
     <dependency>
       <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
       <artifactId>remote-conversation</artifactId>
       <version>1.8.0</version>
     </dependency>
   </dependencies>
   ```

3. Sla de wijzigingen op

### <a name="sample-transcription-code"></a>Voorbeeld code transcriptie

Nadat u de `conversationId`hebt, maakt u een externe conversatie transcriptie-client **RemoteConversationTranscriptionClient** op de client toepassing om de status van de asynchrone transcriptie op te vragen. Gebruik de methode **getTranscriptionOperation** in **RemoteConversationTranscriptionClient** om een [PollerFlux](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/PollerFlux.java) -object op te halen. Het PollerFlux-object bevat informatie over de externe bewerkings status **RemoteConversationTranscriptionOperation** en het uiteindelijke resultaat **RemoteConversationTranscriptionResult**. Zodra de bewerking is voltooid, kunt u **RemoteConversationTranscriptionResult** ophalen door **getFinalResult** aan te roepen op een [SyncPoller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/SyncPoller.java). In deze code wordt de inhoud van het resultaat gewoon naar de systeem uitvoer afgedrukt.

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
