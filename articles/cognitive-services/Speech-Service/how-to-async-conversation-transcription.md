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
ms.openlocfilehash: 433ea2778d99de39991565b2529e2f8eab4e13df
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506976"
---
# <a name="asynchronous-conversation-transcription-preview"></a>Asynchrone conversatie transcriptie (preview-versie)

In dit artikel wordt asynchroon gesprek transcriptie gedemonstreerd met behulp van de **RemoteConversationTranscriptionClient** -API. Als u conversatie transcriptie hebt geconfigureerd om asynchrone transcriptie uit te voeren en een `conversationId`hebt, kunt u de transcriptie verkrijgen die is gekoppeld aan die `conversationId` met behulp van de **RemoteConversationTranscriptionClient** -API.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchroon versus realtime + asynchroon

Met asynchrone transcriptie streamt u de conversatie audio, maar hoeft er in realtime geen transcriptie te worden geretourneerd. Gebruik in plaats daarvan de `conversationId` `ConversationTranscriber` om de status van de asynchrone transcriptie op te vragen. Wanneer de asynchrone transcriptie klaar is, krijgt u een `RemoteConversationTranscriptionResult`.

Met realtime plus asynchroon krijgt u de transcriptie in realtime, maar krijgt u ook de transcriptie door een query uit te geven op de `conversationId` (vergelijkbaar met asynchroon scenario).

Er zijn twee stappen vereist voor het uitvoeren van asynchrone transcriptie. De eerste stap is het uploaden van de audio, het kiezen van alleen asynchroon of realtime plus asynchroon. De tweede stap bestaat uit het ophalen van de transcriptie-resultaten.

## <a name="upload-the-audio"></a>De audio uploaden

Voordat asynchrone transcriptie kunnen worden uitgevoerd, moet u de audio naar conversatie transcriptie verzenden met behulp van de micro soft cognitieve speech client SDK (versie 1.8.0 of hoger).

Deze voorbeeld code laat zien hoe u een conversatie transcriber kunt maken voor de modus alleen asynchroon. Als u audio naar de transcriber wilt streamen, moet u audio streaming code die is afgeleid van het verzetten van [conversaties in realtime met de spraak-SDK](how-to-use-conversation-transcription-service.md)toevoegen. Raadpleeg de sectie **beperkingen** van het onderwerp voor een overzicht van de ondersteunde platforms en talen api's.

```java
// Create the speech config object
// Substitute real information for "YourSubscriptionKey" and "Region"
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");
speech_config.setProperty("ConversationTranscriptionInRoomAndOnline", "true");

// Set the property for asynchronous transcription
speechConfig.setServiceProperty("transcriptionMode", "Async", ServicePropertyChannel.UriQueryParameter);

// Set the property for real-time plus asynchronous transcription
//speechConfig.setServiceProperty("transcriptionMode", "RealTimeAndAsync", ServicePropertyChannel.UriQueryParameter);

// Do rest of the things as explained in how to use Conversation Transcription

// pick a conversation Id that is a GUID.
Conversation conversation = new Conversation(speechConfig, conversationId);

// Create a conversation transcriber
ConversationTranscriber transcriber = new ConversationTranscriber(AudioConfig.fromDefaultMicrophoneInput());

// join a conversation
transcriber.joinConversationAsync(conversation).get();

// stream audio as shown in “Transcribe conversations in real time”
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

Voor de code die hier wordt weer gegeven, hebt u **remoteconversation-client-SDK-versie 1.0.0**nodig, alleen ondersteund voor Java (1,8 of hoger) in Windows, Linux en ANDROID (API-niveau 26 of hoger).

### <a name="obtaining-the-client-sdk"></a>De client-SDK verkrijgen

U kunt **remoteconversation-client-SDK** verkrijgen door uw pom. XML-bestand als volgt te bewerken.

- Maak aan het einde van het bestand een `repositories` element met een verwijzing naar de Maven-opslag plaats voor de afsluitende tag `</project>`:

  ```xml
  <repositories>
    <repository>
      <id>maven-cognitiveservices-speech</id>
      <name>Microsoft Cognitive Services Speech Maven Repository</name>
      <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
    </repository>
  </repositories>
  ```

- Voeg ook een `dependencies`-element toe, met de remoteconversation-client-SDK 1.0.0 als afhankelijkheid:

  ```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.cognitiveservices.speech.remoteconversation</groupId>
      <artifactId>remoteconversation-client-sdk</artifactId>
      <version>1.0.0</version>
    </dependency>
  </dependencies>
  ```

- Sla de wijzigingen op

### <a name="sample-transcription-code"></a>Voorbeeld code transcriptie

Nadat u de `conversationId`hebt, maakt u een extern bewerkings object **RemoteConversationTranscriptionOperation** op de client om de status van de asynchrone transcriptie op te vragen. **RemoteConversationTranscriptionOperation** is uitgebreid op basis van de [poller](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/core/azure-core/src/main/java/com/azure/core/util/polling/Poller.java). Zodra de poller is voltooid, kunt u **RemoteConversationTranscriptionResult** ophalen door u te abonneren op de poller en query's uit te sturen naar het object. In deze code wordt de inhoud van het resultaat gewoon naar de systeem uitvoer afgedrukt.

```java
// Create the speech config object
SpeechConfig speechConfig = SpeechConfig.fromSubscription("YourSubscriptionKey", "Region");

// Create a remote Conversation Transcription client
RemoteConversationTranscriptionClient client = new RemoteConversationTranscriptionClient(speechConfig);

// Create a remote Conversation Transcription operation
RemoteConversationTranscriptionOperation operation = new RemoteConversationTranscriptionOperation(conversationId, client);

// Operation identifier now be the value of `conversationId`
System.out.println("Operation Identifier:" + operation.getId());

// Get the observer (which is a Flux) and subscribe to it for the response
operation.getObserver()
                .subscribe(
                        pollResponse -> {
                            System.out.println("Poll response status : " + pollResponse.getStatus());
                            if(pollResponse.getValue().getConversationTranscriptionResults() != null) {
                                for (int i = 0; i < pollResponse.getValue().getConversationTranscriptionResults().size(); i++) {
                                    ConversationTranscriptionResult result = pollResponse.getValue().getConversationTranscriptionResults().get(i);
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
                );
// Block on the operation till it is finished
operation.block();

System.out.println("Operation finished");
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Onze C#-voorbeelden op GitHub bekijken](https://aka.ms/csspeech/samples)
