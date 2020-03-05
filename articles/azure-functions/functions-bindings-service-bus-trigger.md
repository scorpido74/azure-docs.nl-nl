---
title: Azure Service Bus-bindingen voor Azure Functions
description: Meer informatie over het uitvoeren van een Azure-functie wanneer Azure Service Bus berichten worden gemaakt.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273570"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus trigger voor Azure Functions

De Service Bus-trigger te gebruiken om te reageren op berichten van een Service Bus-wachtrij of onderwerp.

Zie het [overzicht](functions-bindings-service-bus-output.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) waarmee de [meta gegevens](#message-metadata) van een bericht worden gelezen en een service bus wachtrij bericht wordt geregistreerd:

```cs
[FunctionName("ServiceBusQueueTriggerCSharp")]                    
public static void Run(
    [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
    string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    ILogger log)
{
    log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
    log.LogInformation($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.LogInformation($"DeliveryCount={deliveryCount}");
    log.LogInformation($"MessageId={messageId}");
}
```

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een service bus trigger in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van berichten](#message-metadata) gelezen en wordt een service bus wachtrij bericht geregistreerd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Dit is de C#-scriptcode:

```cs
using System;

public static void Run(string myQueueItem,
    Int32 deliveryCount,
    DateTime enqueuedTimeUtc,
    string messageId,
    TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"DeliveryCount={deliveryCount}");
    log.Info($"MessageId={messageId}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Service Bus trigger binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van berichten](#message-metadata) gelezen en wordt een service bus wachtrij bericht geregistreerd. 

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "queueName": "testqueue",
    "connection": "MyServiceBusConnection",
    "name": "myQueueItem",
    "type": "serviceBusTrigger",
    "direction": "in"
    }
],
"disabled": false
}
```

Dit is het JavaScript-script-code:

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('DeliveryCount =', context.bindingData.deliveryCount);
    context.log('MessageId =', context.bindingData.messageId);
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u een Service Bus wachtrij bericht kunt lezen via een trigger.

Een Service Bus binding is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `serviceBusTrigger`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "msg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "inputqueue",
      "connection": "AzureServiceBusConnectionString"
    }
  ]
}
```

De code in  *_\_init_\_. py* declareert een para meter als `func.ServiceBusMessage`, waarmee u het wachtrij bericht in uw functie kunt lezen.

```python
import azure.functions as func

import logging
import json

def main(msg: func.ServiceBusMessage):
    logging.info('Python ServiceBus queue trigger processed message.')

    result = json.dumps({
        'message_id': msg.message_id,
        'body': msg.get_body().decode('utf-8'),
        'content_type': msg.content_type,
        'expiration_time': msg.expiration_time,
        'label': msg.label,
        'partition_key': msg.partition_key,
        'reply_to': msg.reply_to,
        'reply_to_session_id': msg.reply_to_session_id,
        'scheduled_enqueue_time': msg.scheduled_enqueue_time,
        'session_id': msg.session_id,
        'time_to_live': msg.time_to_live,
        'to': msg.to,
        'user_properties': msg.user_properties,
    })

    logging.info(result)
```

# <a name="java"></a>[Java](#tab/java)

De volgende Java-functie maakt gebruik van de `@ServiceBusQueueTrigger` annotatie van de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime) om de configuratie voor een service bus wachtrij trigger te beschrijven. De functie trekt het bericht dat in de wachtrij is geplaatst en voegt het toe aan de logboeken.

```java
@FunctionName("sbprocessor")
 public void serviceBusProcess(
    @ServiceBusQueueTrigger(name = "msg",
                             queueName = "myqueuename",
                             connection = "myconnvarname") String message,
   final ExecutionContext context
 ) {
     context.getLogger().info(message);
 }
```

Java-functies kunnen ook worden geactiveerd wanneer een bericht wordt toegevoegd aan een Service Bus onderwerp. In het volgende voor beeld wordt de `@ServiceBusTopicTrigger` aantekening gebruikt om de trigger configuratie te beschrijven.

```java
@FunctionName("sbtopicprocessor")
    public void run(
        @ServiceBusTopicTrigger(
            name = "message",
            topicName = "mytopicname",
            subscriptionName = "mysubscription",
            connection = "ServiceBusConnection"
        ) String message,
        final ExecutionContext context
    ) {
        context.getLogger().info(message);
    }
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)de volgende kenmerken om een service bus trigger te configureren:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  De constructor van het kenmerk wordt de naam van de wachtrij of onderwerp en abonnement. In Azure Functions-versie 1.x, kunt u ook de toegangsrechten van de verbinding opgeven. Als u geen toegangs rechten opgeeft, wordt de standaard waarde `Manage`. Zie de sectie [trigger-configuratie](#configuration) voor meer informatie.

  Hier volgt een voorbeeld waarin het kenmerk wordt gebruikt met een queryreeks-parameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  U kunt de eigenschap `Connection` instellen om de naam op te geven van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Zie voor een volledig voor beeld [trigger-voor beeld](#example).

* [ServiceBusAccountAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Biedt een andere manier om op te geven van de Service Bus-account moet worden gebruikt. De constructor, wordt de naam van een app-instelling met een Service Bus-verbindingsreeks. Het kenmerk kan worden toegepast op de parameter, klasseniveau of methode. Het volgende voorbeeld toont het klasseniveau en methode:

  ```csharp
  [ServiceBusAccount("ClassLevelServiceBusAppSetting")]
  public static class AzureFunctions
  {
      [ServiceBusAccount("MethodLevelServiceBusAppSetting")]
      [FunctionName("ServiceBusQueueTriggerCSharp")]
      public static void Run(
          [ServiceBusTrigger("myqueue", AccessRights.Manage)] 
          string myQueueItem, ILogger log)
  {
      ...
  }
  ```

De Service Bus-account moet worden gebruikt, wordt bepaald in de volgende volgorde:

* De eigenschap `Connection` van het `ServiceBusTrigger`-kenmerk.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op dezelfde para meter als het kenmerk `ServiceBusTrigger`.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de functie.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de klasse.
* De 'AzureWebJobsServiceBus' app-instelling.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# het script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

Met de `ServiceBusQueueTrigger` aantekening kunt u een functie maken die wordt uitgevoerd wanneer een Service Bus wachtrij bericht wordt gemaakt. Beschik bare configuratie opties zijn wachtrij naam en connection string naam.

Met de `ServiceBusTopicTrigger` aantekening kunt u een onderwerp en een abonnement aanwijzen om te bepalen welke gegevens de functie activeren.

Zie het [voor beeld](#example) van de trigger voor meer details.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `ServiceBusTrigger`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op 'serviceBusTrigger'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt.|
|**direction** | N.v.t. | Moet worden ingesteld op 'in'. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger in Azure portal maakt. |
|**naam** | N.v.t. | De naam van de variabele die staat voor de wachtrij of onderwerp bericht in de functiecode aan te geven. |
|**queueName**|**QueueName**|Naam van de wachtrij om te controleren.  Alleen ingesteld als bewaking van een wachtrij, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|Naam van het onderwerp om te controleren. Alleen ingesteld als een onderwerp, voor een wachtrij niet controleren.|
|**subscriptionName**|**SubscriptionName**|De naam van het abonnement om te controleren. Alleen ingesteld als een onderwerp, voor een wachtrij niet controleren.|
|**Combi**|**Verbinding**|De naam van een app-instelling met de Service Bus-verbindingsreeks moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', kunt u alleen het restant van de naam opgeven. Als u bijvoorbeeld `connection` instelt op ' MyServiceBus ', zoekt de runtime van functions naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus connection string in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De verbindingsreeks moet voor een Service Bus-naamruimte, niet beperkt tot een specifieke wachtrij of onderwerp. |
|**accessRights**|**Toegang**|Toegangsrechten voor de verbindingsreeks. Beschik bare waarden zijn `manage` en `listen`. De standaard waarde is `manage`, wat aangeeft dat de `connection` de machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders wordt het Functions runtime mislukken probeert uit te voeren bewerkingen uit waarbij rechten beheren. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true` als er verbinding wordt gemaakt met een [sessie bewuste](../service-bus-messaging/message-sessions.md) wachtrij of een abonnement. `false` anders de standaard waarde.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

De volgende parameter typen zijn beschikbaar voor het bericht in de wachtrij of het onderwerp:

* `string`-als het bericht tekst is.
* `byte[]`-nuttig voor binaire gegevens.
* Een aangepast type - als het bericht JSON bevat, Azure Functions probeert te deserialiseren van de JSON-gegevens.
* `BrokeredMessage`: geeft u het gedeserialiseerd bericht met de methode [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze parameter typen zijn voor Azure Functions versie 1. x; gebruik voor 2. x en hoger [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage`.

# <a name="c-script"></a>[C#Schriften](#tab/csharp-script)

De volgende parameter typen zijn beschikbaar voor het bericht in de wachtrij of het onderwerp:

* `string`-als het bericht tekst is.
* `byte[]`-nuttig voor binaire gegevens.
* Een aangepast type - als het bericht JSON bevat, Azure Functions probeert te deserialiseren van de JSON-gegevens.
* `BrokeredMessage`: geeft u het gedeserialiseerd bericht met de methode [BrokeredMessage. GetBody\<t > ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze para meters zijn voor Azure Functions versie 1. x; gebruik voor 2. x en hoger [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp bericht met behulp van `context.bindings.<name from function.json>`. De Service Bus-bericht wordt doorgegeven in de functie als een tekenreeks of een JSON-object.

# <a name="python"></a>[Python](#tab/python)

Het wachtrij bericht is beschikbaar voor de functie via een para meter die is getypt als `func.ServiceBusMessage`. De Service Bus-bericht wordt doorgegeven in de functie als een tekenreeks of een JSON-object.

# <a name="java"></a>[Java](#tab/java)

Het binnenkomende Service Bus-bericht is beschikbaar via een `ServiceBusQueueMessage`-of `ServiceBusTopicMessage`-para meter.

[Zie het voor beeld voor meer informatie](#example).

---

## <a name="poison-messages"></a>Verontreinigde berichten

Beheer van Onverwerkbare berichten afhandelen, kan niet worden beheerd of geconfigureerd in Azure Functions. Service Bus verwerkt Onverwerkbare berichten zelf.

## <a name="peeklock-behavior"></a>PeekLock-gedrag

De functions-runtime ontvangt een bericht in de [modus PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Als de functie is voltooid, wordt `Complete` op het bericht aangeroepen, of wordt `Abandon` aangeroepen als de functie mislukt. Als de functie langer dan de `PeekLock`-time-out wordt uitgevoerd, wordt de vergren deling automatisch vernieuwd zolang de functie wordt uitgevoerd. 

De `maxAutoRenewDuration` kan worden geconfigureerd in *host. json*, dat wordt toegewezen aan [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). De maximaal toegestane aantal voor deze instelling is 5 minuten op basis van de documentatie voor Service Bus, terwijl u de tijdslimiet functies van de standaardwaarde van 5 minuten tot 10 minuten kunt verhogen. Voor Service Bus-functies wouldn't u vervolgens doen omdat u de limiet voor het vernieuwen van Service Bus wordt overschreden.

## <a name="message-metadata"></a>Meta gegevens van bericht

De trigger Service Bus biedt verschillende [Eigenschappen van meta gegevens](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van de expressies in andere bindingen voor gegevensbinding of als parameters in uw code. Deze eigenschappen zijn leden van de klasse [BrokeredMessage](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) .

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Het aantal bezorgingen.|
|`DeadLetterSource`|`string`|De dead-letter-bron.|
|`ExpiresAtUtc`|`DateTime`|De vervaldatum tijd in UTC.|
|`EnqueuedTimeUtc`|`DateTime`|De in de wachtrij-tijd in UTC.|
|`MessageId`|`string`|Een door de gebruiker gedefinieerde waarde die Service Bus gebruiken om dubbele berichten te identificeren als ingeschakeld.|
|`ContentType`|`string`|Een inhouds type-id die wordt gebruikt door de afzender en ontvanger voor toepassingsspecifieke logica.|
|`ReplyTo`|`string`|Het antwoordadres wachtrij.|
|`SequenceNumber`|`Int64`|Het unieke nummer toegewezen aan een bericht door Service Bus.|
|`To`|`string`|De verzenden naar een adres.|
|`Label`|`string`|Het toepassingsspecifiek label.|
|`CorrelationId`|`string`|De correlatie-ID.|

Zie [code voorbeelden](#example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

- [Azure Service Bus berichten verzenden vanuit Azure Functions (uitvoer binding)](./functions-bindings-service-bus-output.md)
