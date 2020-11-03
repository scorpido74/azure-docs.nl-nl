---
title: Problemen met Azure Communication Services oplossen
description: Ontdek hoe u de informatie kunt verzamelen die u nodig hebt om problemen met uw Communication Services-oplossing op te lossen.
author: manoskow
manager: jken
services: azure-communication-services
ms.author: manoskow
ms.date: 10/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4921078e9e7b5d9de06fbbc9a97dc4a964569e04
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754655"
---
# <a name="troubleshooting-in-azure-communication-services"></a>Problemen met Azure Communication Services oplossen

Dit document helpt u bij het verzamelen van de informatie die u nodig hebt om problemen met uw Communication Services-oplossing op te lossen.

## <a name="getting-help"></a>Ondersteuning vragen

We moedigen ontwikkelaars aan om vragen te verzenden, functies voor te stellen en problemen te rapporteren in de [GitHub-opslagplaats](https://github.com/Azure/communication) van Communication Services. Andere forums zijn onder meer:

* [Microsoft Q&A](https://docs.microsoft.com/answers/questions/topics/single/101418.html)
* [StackOverflow](https://stackoverflow.com/questions/tagged/azure+communication)

Afhankelijk van het [ondersteuningsplan](https://azure.microsoft.com/support/plans/) van uw Azure-abonnement, kunt u rechtstreeks een ondersteuningsticket indienen via [Azure Portal](https://azure.microsoft.com/support/create-ticket/).

Om u te helpen bij het oplossen van bepaalde typen problemen, wordt u mogelijk gevraagd naar een van de volgende soorten gegevens:

* **MS-CV-id** : Deze id wordt gebruikt voor het oplossen van problemen met oproepen en berichten. 
* **Oproep-id** : Deze id wordt gebruikt om Communication Services-oproepen te identificeren.
* **Sms-bericht-id** : Deze id wordt gebruikt om sms-berichten te identificeren.

## <a name="access-your-ms-cv-id"></a>De MS-CV-id ophalen

U kunt de MS-CV-id ophalen door tijdens het initialiseren van uw clientbibliotheken diagnostische gegevens te configureren in het `clientOptions`-objectexemplaar. Diagnostische gegevens kunnen worden geconfigureerd voor een van de Azure-clientbibliotheken, waaronder chat, beheer en VoIP-oproepen.

### <a name="client-options-example"></a>Voorbeeld van clientopties

In de volgende codefragmenten wordt de configuratie van diagnostische gegevens gedemonstreerd. Wanneer de clientbibliotheken in combinatie met diagnostische gegevens worden gebruikt, worden er diagnostische gegevens verzonden naar de geconfigureerde gebeurtenislistener:

# <a name="c"></a>[C#](#tab/csharp)
``` 
// 1. Import Azure.Core.Diagnostics
using Azure.Core.Diagnostics;

// 2. Initialize an event source listener instance
using var listener = AzureEventSourceListener.CreateConsoleLogger();
Uri endpoint = new Uri("https://<RESOURCE-NAME>.communication.azure.net");
var (token, communicationUser) = await GetCommunicationUserAndToken();
CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(token);

// 3. Setup diagnostic settings
var clientOptions = new ChatClientOptions()
{
    Diagnostics =
    {
        LoggedHeaderNames = { "*" },
        LoggedQueryParameters = { "*" },
        IsLoggingContentEnabled = true,
    }
};

// 4. Initialize the ChatClient instance with the clientOptions 
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential, clientOptions);
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync("Thread Topic", new[] { new ChatThreadMember(communicationUser) });
```

# <a name="python"></a>[Python](#tab/python)
``` 
from azure.communication.chat import ChatClient, CommunicationUserCredential
endpoint = "https://communication-services-sdk-live-tests-for-python.communication.azure.com"
chat_client = ChatClient(
    endpoint,
    CommunicationUserCredential(token),
    http_logging_policy=your_logging_policy)
```
---

## <a name="access-your-call-id"></a>De oproep-id ophalen

Bij het indienen van een ondersteuningsaanvraag met betrekking tot oproepproblemen via Azure Portal wordt u mogelijk gevraagd de id op te geven van de oproep waarnaar u verwijst. Deze kan worden opgehaald via de aanroepende clientbibliotheek:

# <a name="javascript"></a>[JavaScript](#tab/javascript)
```javascript
// `call` is an instance of a call created by `callAgent.call` or `callAgent.join` methods 
console.log(call.id)
```

# <a name="ios"></a>[iOS](#tab/ios)
```objc
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends 
// todo: the code snippet suggests it's a property while the comment suggests it's a method call
print(call.callId) 
```

# <a name="android"></a>[Android](#tab/android)
```java
// The `call id` property can be retrieved by calling the `call.getCallId()` method on a call object after a call ends
// `call` is an instance of a call created by `callAgent.call(…)` or `callAgent.join(…)` methods 
Log.d(call.getCallId()) 
```
---


## <a name="access-your-sms-message-id"></a>De sms-bericht-id ophalen

Voor problemen met sms kunt u de bericht-id van het antwoordobject ophalen.

# <a name="net"></a>[.NET](#tab/dotnet)
```
// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
async function main() {
  const result = await smsClient.send({
    from: "+18445792722",
    to: ["+1972xxxxxxx"],
    message: "Hello World 👋🏻 via Sms"
  }, {
    enableDeliveryReport: true // Optional parameter
  });
console.log(result); // your message ID will be in the result
}
```
---

## <a name="related-information"></a>Gerelateerde informatie
- [Logboeken en diagnostische gegevens](logging-and-diagnostics.md)
- [Metrische gegevens](metrics.md)
