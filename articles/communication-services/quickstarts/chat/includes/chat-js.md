---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: d0754ea2d7e8f8f59ec475be8e27fcffd058c11f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376889"
---
## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie. 
- Installeer [Node.js](https://nodejs.org/en/download/) Active LTS- en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. Voor deze quickstart moet u het **eindpunt** van uw resource vastleggen.
- Een [toegangstoken voor gebruikers](../../access-tokens.md). Zorg ervoor dat u het bereik instelt op ‘chat’ en noteer de tokenreeks en ook de gebruikersId-reeks.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-web-application"></a>Een nieuwe webtoepassing maken

Open eerst uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daarnaartoe.

```console
mkdir chat-quickstart && cd chat-quickstart
```
   
Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

Gebruik een teksteditor om een bestand met de naam **start-chat.js** te maken in de hoofdmap van het project. In de volgende secties voegt u alle broncode voor deze quickstart toe aan dit bestand.

### <a name="install-the-packages"></a>De pakketten installeren

Gebruik de opdracht `npm install` voor het installeren van de onderstaande Communication Services-clientbibliotheken voor JavaScript.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in uw **package.json**-bestand.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In deze quickstart wordt webpack gebruikt om de toepassingsassets te bundelen. Voer de volgende opdracht uit om de webpack-, webpack-CLI- en webpack-dev-server npm-pakketten te installeren en deze weer te geven als ontwikkelingsafhankelijkheden in uw **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Maak een **index. html**-bestand in de hoofdmap van uw project. We zullen dit bestand gebruiken als een sjabloon om chat-functionaliteit toe te voegen met behulp van de Azure Communication chat-clientbibliotheek voor JavaScript.

Dit is de code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```
Maak een bestand in de hoofdmap van uw project met de naam **client.js** om de toepassingslogica voor deze quickstart te bevatten. 

### <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client in uw web-app wilt maken, gebruikt u het Communication Services-eindpunt en het toegangstoken dat is gegenereerd als onderdeel van de vereiste stappen. Met toegangstokens voor gebruikers kunt u clienttoepassingen maken die zich rechtstreeks verifiëren bij Azure Communication Services. Zodra u deze tokens op uw server hebt gegenereerd, geeft u ze terug op een clientapparaat. U moet de klasse `AzureCommunicationUserCredential` uit de `Common client library` gebruiken om het token aan uw chat-client door te geven.

Maak een bestand **client.js** in de hoofdmap van uw project. We zullen dit bestand gebruiken om chat-functionaliteit toe te voegen met behulp van de Azure Communication chat-clientbibliotheek voor JavaScript.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationUserCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationUserCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
Vervang **ENDPOINT** door het eindpunt dat eerder is gemaakt op basis van de documentatie [Een Azure Communication-resource maken](../../create-communication-resource.md).
Vervang **USER_ACCESS_TOKEN** door een token dat is uitgegeven op basis van de documentatie [Toegangstoken voor gebruikers](../../access-tokens.md).
Voeg deze code toe aan het **client.js**-bestand.


### <a name="run-the-code"></a>De code uitvoeren
Gebruik de `webpack-dev-server` om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Open uw browser en ga naar http://localhost:8080/.
In de console voor ontwikkelaarshulpprogramma’s in uw browser zou u het volgende moeten zien:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objectmodel 
De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services chat-clientbibliotheek voor JavaScript.

| Naam                                   | Beschrijving                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden, te ontvangen, bij te werken of te verwijderen, gebruikers toe te voegen, te verwijderen of op te halen, getypte meldingen te verzenden, bevestigingen te lezen en u op chatgebeurtenissen te abonneren. |


## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `createThread` om een chat-thread te maken.

`createThreadRequest` wordt gebruikt om de thread-aanvraag te beschrijven:

- Gebruik `topic` om een onderwerp te geven aan deze chat. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `UpdateThread`. 
- Gebruik `members` om de leden weer te geven die moeten worden toegevoegd aan de chat-thread;

De methode `createChatThread` (wanneer opgelost) retourneert `threadId`, dat wordt gebruikt om bewerkingen uit te voeren op de zojuist gemaakte chat-thread, zoals het toevoegen van leden aan de chat-thread, het verzenden of verwijderen van een bericht enzovoort.

```Javascript
async function createChatThread() {
   let createThreadRequest = {
       topic: 'Preparation for London conference',
       members: [{
                   user: { communicationUserId: '<USER_ID_FOR_JACK>' },
                   displayName: 'Jack'
               }, {
                   user: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                   displayName: 'Geeta'
               }]
   };
   let chatThreadClient= await chatClient.createChatThread(createThreadRequest);
   let threadId = chatThreadClient.threadId;
   return threadId;
}

createChatThread().then(async threadId => {
   console.log(`Thread created:${threadId}`);
   // PLACEHOLDERS
   // <CREATE CHAT THREAD CLIENT>
   // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
   // <SEND MESSAGE TO A CHAT THREAD>
   // <LIST MESSAGES IN A CHAT THREAD>
   // <ADD NEW MEMBER TO THREAD>
   // <LIST MEMBERS IN A THREAD>
   // <REMOVE MEMBER FROM THREAD>
});
```

Vervang **USER_ID_FOR_JACK** en **USER_ID_FOR_GEETA** door de gebruikers-id’s die zijn verkregen in de vorige stap (Gebruikers maken en [toegangstokens voor gebruikers](../../access-tokens.md) uitgeven)

Wanneer u uw browsertabblad vernieuwt, zou u in de console het volgende moeten zien:
```console
Thread created: <threadId>
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen

De methode `getChatThreadClient` retourneert een `chatThreadClient` voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: leden toevoegen, bericht verzenden, enz. threadId is de unieke id van de bestaande chat-thread.

```JavaScript

let chatThreadClient = await chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`);

```
Voeg deze code toe in plaats van de opmerking `<CREATE CHAT THREAD CLIENT>` in **client.js**, vernieuw uw browsertabblad en controleer de console, waar u het volgende zou moeten zien:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik de methode `sendMessage` om een chatbericht te verzenden naar de thread die u zojuist hebt gemaakt, geïdentificeerd door threadId.

`sendMessageRequest` beschrijft de vereiste velden van de chatberichtaanvraag:

- Gebruik `content` om de inhoud van het chatbericht op te geven;

`sendMessageOptions` beschrijft de optionele velden van de chatberichtaanvraag:

- Gebruik `priority` om het prioriteitsniveau van het chatbericht op te geven, zoals ‘Normaal’ of ‘Hoog’. Deze eigenschap kan worden gebruikt om een UI-indicator te hebben voor de ontvangende gebruiker in uw app, om de aandacht te vestigen op het bericht of aangepaste bedrijfslogica uit te voeren.   
- Gebruik `senderDisplayName` om de weergavenaam van de afzender op te geven.

Het antwoord `sendChatMessageResult` bevat een ‘id’. Dit is de unieke id van het bericht.

```JavaScript

let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    priority: 'Normal',
    senderDisplayName : 'Jack'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
console.log(`Message sent!, message id:${messageId}`);

```
Voeg deze code toe in plaats van de opmerking `<SEND MESSAGE TO A CHAT THREAD>` in **client.js**, vernieuw uw browsertabblad en controleer de console.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

Met realtime signalering kunt u zich abonneren om te luisteren naar nieuwe inkomende berichten en de huidige berichten dienovereenkomstig bij te werken in het geheugen. Azure Communication Services ondersteunt een [lijst met gebeurtenissen waarop u zich kunt abonneren](../../../concepts/chat/concepts.md#real-time-signaling).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
Voeg deze code toe in plaats van de opmerking `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` in **client.js**.
Wanneer u uw browsertabblad vernieuwt, zou u in de console een bericht `Notification chatMessageReceived` moeten zien.

U kunt chatberichten ook ophalen door de methode `listMessages` op opgegeven intervallen te pollen. 

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
 while (!nextMessage.done) {
     let chatMessage = nextMessage.value;
     console.log(`Message :${chatMessage.content}`);
     // your code here
     nextMessage = await pagedAsyncIterableIterator.next();
 }

```
Voeg deze code toe in plaats van de opmerking `<LIST MESSAGES IN A CHAT THREAD>` in **client.js**.
Vernieuw uw tabblad, waarna u in de console een lijst met berichten zou moeten zien die in deze chat-thread zijn verzonden.


`listMessages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met `updateMessage` en `deleteMessage`.
Voor verwijderde berichten retourneert `chatMessage.deletedOn` een datum/tijd-waarde die aangeeft wanneer het bericht verwijderd werd. Voor bewerkte berichten retourneert `chatMessage.editedOn` een datum/tijd die aangeeft wanneer het bericht is bewerkt. Het oorspronkelijke tijdstip waarop het bericht is gemaakt, kan worden geraadpleegd met `chatMessage.createdOn` en kan worden gebruikt om de berichten te ordenen.

`listMessages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `chatMessage.type`. Deze typen zijn:

- `Text`: Normaal chatbericht verzonden door een thread-lid.

- `ThreadActivity/TopicUpdate`: Systeembericht dat aangeeft dat het onderwerp is bijgewerkt.

- `ThreadActivity/AddMember`: Systeembericht dat aangeeft dat een of meer leden zijn toegevoegd aan de chat-thread.

- `ThreadActivity/RemoveMember`: Een systeembericht dat aangeeft dat een lid is verwijderd uit de chat-thread.

Zie [Berichttypen](../../../concepts/chat/concepts.md#message-types)voor meer informatie.

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Een gebruiker toevoegen als lid van de chat-thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, geeft u ze toegang voor het verzenden van berichten naar de chat-thread en het toevoegen/verwijderen van andere leden. Voordat u de methode `addMembers` aanroept, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

`addMembersRequest` beschrijft het aanvraagobject waarin `members` de leden weergeeft die moeten worden toegevoegd aan de chat-thread;
- `user`, vereist, is de communicatiegebruiker die moet worden toegevoegd aan de chat-thread.
- `displayName`, optioneel, is de weergavenaam voor het thread-lid.
- `shareHistoryTime`, optioneel, is de tijd van waaruit de chat-geschiedenis wordt gedeeld met het lid. Als u de geschiedenis wilt delen sinds het begin van de chat-thread, stelt u deze eigenschap in op een willekeurige datum die gelijk is aan of kleiner is dan de aanmaaktijd van de thread. Als u geen geschiedenis wilt delen voordat het lid is toegevoegd, stel de eigenschap dan op de huidige datum in. Als u een deel van de geschiedenis wilt delen, stelt u de eigenschap in op de gewenste datum.

```JavaScript

let addMembersRequest =
{
    members: [
        {
            user: { communicationUserId: '<NEW_MEMBER_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addMembers(addMembersRequest);

```
Vervang **NEW_MEMBER_USER_ID** door een [nieuwe gebruikers-id](../../access-tokens.md). Voeg deze code toe in plaats van de opmerking `<ADD NEW MEMBER TO THREAD>` in **client.js**.

## <a name="list-users-in-a-chat-thread"></a>Gebruikers in een chat-thread weergeven
```JavaScript
async function listThreadMembers() {
   let pagedAsyncIterableIterator = await chatThreadClient.listMembers();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listThreadMembers();
```
Voeg deze code toe in plaats van de opmerking `<LIST MEMBERS IN A THREAD>` in **client.js**, vernieuw uw browsertabblad en controleer de console, waar u informatie over gebruikers in een thread zou moeten zien.

## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een lid, kunt u ook leden uit een chat-thread verwijderen. Als u wilt verwijderen, moet u de id's bijhouden van de leden die u hebt toegevoegd.

Gebruik de methode `removeMember`, waarbij `member` de communicatiegebruiker is die uit de thread moet worden verwijderd.

```JavaScript

await chatThreadClient.removeMember({ communicationUserId: <MEMBER_ID> });
await listThreadMembers();
```
Vervang **MEMBER_ID** door een gebruikers-id die in de vorige stap wordt gebruikt (<NEW_MEMBER_USER_ID>).
Voeg deze code toe in plaats van de opmerking `<REMOVE MEMBER FROM THREAD>` in **client.js**.
