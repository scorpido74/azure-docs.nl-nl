---
title: Azure Service Bus bindingen voor Azure Functions
description: Meer informatie over het uitvoeren van een Azure-functie wanneer Azure Service Bus berichten worden gemaakt.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: c15fe311b331592a54c61a5cddb29d4b467ca550
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560809"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus trigger voor Azure Functions

Gebruik de Service Bus-trigger om te reageren op berichten van een Service Bus wachtrij of onderwerp. Te beginnen met de extensie versie 3.1.0 kunt u activeren op een wachtrij of onderwerp waarvoor een sessie is ingeschakeld.

Zie het [overzicht](functions-bindings-service-bus-output.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die [meta gegevens](#message-metadata) van een bericht leest en een service bus wachtrij bericht registreert:

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

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van Service Bus trigger in een *Function. json* -bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. Met de functie worden [meta gegevens van berichten](#message-metadata) gelezen en wordt een service bus wachtrij bericht geregistreerd.

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

Dit is de C#-script code:

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

Hier volgt de Java script-script code:

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

Een Service Bus binding is gedefinieerd in *Function. json* waarbij *type* is ingesteld op `serviceBusTrigger` .

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

De code in * _ \_ init_ \_ . py* declareert een para meter zoals `func.ServiceBusMessage` , waarmee u het wachtrij bericht in uw functie kunt lezen.

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

De volgende Java-functie maakt gebruik `@ServiceBusQueueTrigger` van de aantekening van de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime) om de configuratie voor een service bus wachtrij trigger te beschrijven. De functie trekt het bericht dat in de wachtrij is geplaatst en voegt het toe aan de logboeken.

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

Gebruik in [C# class libraries](functions-dotnet-class-library.md)de volgende kenmerken om een service bus trigger te configureren:

* [ServiceBusTriggerAttribute](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en het abonnement. In Azure Functions versie 1. x kunt u ook de toegangs rechten voor de verbinding opgeven. Als u geen toegangs rechten opgeeft, is de standaard waarde `Manage` . Zie de sectie [trigger-configuratie](#configuration) voor meer informatie.

  Hier volgt een voor beeld waarin het kenmerk wordt weer gegeven met een teken reeks parameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Omdat de `Connection` eigenschap niet is gedefinieerd, zoekt functies naar een app-instelling met `AzureWebJobsServiceBus` de naam. Dit is de standaard naam voor de service bus connection string. U kunt ook de `Connection` eigenschap instellen om de naam op te geven van een toepassings instelling die het Service Bus Connection String bevat dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

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

  Biedt een andere manier om het Service Bus-account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een Service Bus connection string bevat. Het kenmerk kan worden toegepast op de para meter, methode of class-niveau. In het volgende voor beeld wordt het niveau klasse en de methode weer gegeven:

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

Het Service Bus account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De `ServiceBusTrigger` eigenschap van het kenmerk `Connection` .
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op dezelfde para meter als het `ServiceBusTrigger` kenmerk.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de functie.
* Het `ServiceBusAccount` kenmerk dat wordt toegepast op de klasse.
* De instelling van de app ' AzureWebJobsServiceBus '.

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

Kenmerken worden niet ondersteund door een C#-script.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door Java script.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door python.

# <a name="java"></a>[Java](#tab/java)

`ServiceBusQueueTrigger`Met de aantekening kunt u een functie maken die wordt uitgevoerd wanneer een service bus wachtrij bericht wordt gemaakt. Beschik bare configuratie opties zijn wachtrij naam en connection string naam.

Met de `ServiceBusTopicTrigger` aantekening kunt u een onderwerp en een abonnement aanwijzen om te bepalen welke gegevens de functie activeren.

Zie het [voor beeld](#example) van de trigger voor meer details.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het- `ServiceBusTrigger` kenmerk.

|function. json-eigenschap | Kenmerk eigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op ' serviceBusTrigger '. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op in. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**naam** | N.v.t. | De naam van de variabele die de wachtrij of het onderwerp van het bericht in de functie code vertegenwoordigt. |
|**queueName**|**QueueName**|De naam van de wachtrij die moet worden bewaakt.  Stel deze waarde alleen in als u een wachtrij bewaken, niet voor een onderwerp.
|**onderwerpnaam**|**Onderwerpnaam**|De naam van het te bewaken onderwerp. Stel deze waarde alleen in als u een onderwerp bewaken, niet voor een wachtrij.|
|**subscriptionName**|**SubscriptionName**|De naam van het abonnement dat moet worden bewaakt. Stel deze waarde alleen in als u een onderwerp bewaken, niet voor een wachtrij.|
|**Combi**|**Verbinding**|De naam van een app-instelling die de Service Bus connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met "AzureWebJobs", kunt u alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' MyServiceBus ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyServiceBus '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard Service Bus Connection String in de app-instelling met de naam ' AzureWebJobsServiceBus '.<br><br>Volg de stappen die worden weer gegeven in [de beheer referenties ophalen](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string)om een Connection String te verkrijgen. De connection string moet voor een Service Bus naam ruimte zijn, niet beperkt tot een specifieke wachtrij of een specifiek onderwerp. |
|**accessRights**|**Toegang**|Toegangs rechten voor de connection string. Beschik bare waarden zijn `manage` en `listen` . De standaard waarde is `manage` , waarmee wordt aangegeven dat de de `connection` machtiging **beheren** heeft. Als u een connection string gebruikt dat niet de machtiging **beheren** heeft, stelt u `accessRights` in op ' Luis teren '. Anders kan het uitvoeren van de functions-runtime geen bewerkingen uitvoeren waarvoor het beheer van rechten nodig is. In Azure Functions versie 2. x en hoger is deze eigenschap niet beschikbaar omdat de meest recente versie van de Service Bus SDK geen ondersteuning biedt voor het beheren van bewerkingen.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`Als u verbinding maakt met een [sessie bewuste](../service-bus-messaging/message-sessions.md) wachtrij of een abonnement. `false`anders is dit de standaard waarde.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

De volgende parameter typen zijn beschikbaar voor het bericht in de wachtrij of het onderwerp:

* `string`-Als het bericht tekst is.
* `byte[]`-Nuttig voor binaire gegevens.
* Een aangepast type: als het bericht JSON bevat, probeert Azure Functions de JSON-gegevens te deserialiseren.
* `BrokeredMessage`-Geeft u het gegedeserialiseerde bericht met de methode [BrokeredMessage. GetBody \<T> ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze parameter typen zijn voor Azure Functions versie 1. x; voor 2. x en hoger gebruikt u [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage` .

# <a name="c-script"></a>[C#-script](#tab/csharp-script)

De volgende parameter typen zijn beschikbaar voor het bericht in de wachtrij of het onderwerp:

* `string`-Als het bericht tekst is.
* `byte[]`-Nuttig voor binaire gegevens.
* Een aangepast type: als het bericht JSON bevat, probeert Azure Functions de JSON-gegevens te deserialiseren.
* `BrokeredMessage`-Geeft u het gegedeserialiseerde bericht met de methode [BrokeredMessage. GetBody \<T> ()](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1) .

Deze para meters zijn voor Azure Functions versie 1. x; voor 2. x en hoger gebruikt u [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) in plaats van `BrokeredMessage` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Toegang tot de wachtrij of het onderwerp van het bericht met behulp van `context.bindings.<name from function.json>` . Het Service Bus bericht wordt door gegeven aan de functie als een teken reeks of een JSON-object.

# <a name="python"></a>[Python](#tab/python)

Het wachtrij bericht is beschikbaar voor de functie via een para meter getypeerd als `func.ServiceBusMessage` . Het Service Bus bericht wordt door gegeven aan de functie als een teken reeks of een JSON-object.

# <a name="java"></a>[Java](#tab/java)

Het binnenkomende Service Bus-bericht is beschikbaar via een `ServiceBusQueueMessage` of- `ServiceBusTopicMessage` para meter.

[Zie het voor beeld voor meer informatie](#example).

---

## <a name="poison-messages"></a>Verontreinigde berichten

De verwerking van verontreinigde berichten kan niet worden beheerd of geconfigureerd in Azure Functions. Service Bus verwerkt verontreinigde berichten zelf.

## <a name="peeklock-behavior"></a>PeekLock-gedrag

De functions-runtime ontvangt een bericht in de [modus PeekLock](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Het bericht wordt aangeroepen `Complete` als de functie is voltooid, of aanroepen `Abandon` als de functie mislukt. Als de functie langer dan de `PeekLock` time-out wordt uitgevoerd, wordt de vergren deling automatisch vernieuwd zolang de functie wordt uitgevoerd. 

De `maxAutoRenewDuration` kan worden geconfigureerd in *host. json*, dat wordt toegewezen aan [OnMessageOptions. MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Het maximum aantal dat is toegestaan voor deze instelling is 5 minuten volgens de Service Bus-documentatie, terwijl u de tijds limiet van functies kunt verhogen van de standaard waarde van 5 minuten tot 10 minuten. Voor Service Bus functies wilt u dat niet doen, omdat u de Service Bus vernieuwings limiet overschrijdt.

## <a name="message-metadata"></a>Meta gegevens van bericht

De trigger Service Bus biedt verschillende [Eigenschappen van meta gegevens](./functions-bindings-expressions-patterns.md#trigger-metadata). Deze eigenschappen kunnen worden gebruikt als onderdeel van binding expressies in andere bindingen of als para meters in uw code. Deze eigenschappen zijn leden van de [bericht](/dotnet/api/microsoft.azure.servicebus.message?view=azure-dotnet) klasse.

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`ContentType`|`string`|Een inhouds type-id die wordt gebruikt door de afzender en ontvanger voor toepassingsspecifieke logica.|
|`CorrelationId`|`string`|De correlatie-ID.|
|`DeadLetterSource`|`string`|De bron voor de onbestelbare letter.|
|`DeliveryCount`|`Int32`|Het aantal leveringen.|
|`EnqueuedTimeUtc`|`DateTime`|De in wachtrij geplaatste tijd in UTC.|
|`ExpiresAtUtc`|`DateTime`|De verloop tijd in UTC.|
|`Label`|`string`|Het toepassingsspecifiek label.|
|`MessageId`|`string`|Een door de gebruiker gedefinieerde waarde die Service Bus kan gebruiken om dubbele berichten te identificeren, indien ingeschakeld.|
|`MessageReceiver`|`MessageReceiver`|Ontvanger van Service Bus bericht. Kan worden gebruikt om het bericht af te breken, te volt ooien of deadletter.|
|`MessageSession`|`MessageSession`|Een ontvanger van een bericht specifiek voor wacht rijen en onderwerpen met sessie ingeschakeld.|
|`ReplyTo`|`string`|Het adres van de wachtrij voor beantwoorden.|
|`SequenceNumber`|`long`|Het unieke nummer dat is toegewezen aan een bericht door de Service Bus.|
|`To`|`string`|Het adres voor verzenden naar.|
|`UserProperties`|`IDictionary<string, object>`|Eigenschappen die zijn ingesteld door de afzender.|

Zie [code voorbeelden](#example) die gebruikmaken van deze eigenschappen eerder in dit artikel.

## <a name="next-steps"></a>Volgende stappen

- [Azure Service Bus berichten verzenden vanuit Azure Functions (uitvoer binding)](./functions-bindings-service-bus-output.md)
