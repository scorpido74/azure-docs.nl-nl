---
title: Azure Service Bus-bindingen voor Azure-functies
description: Leer een Azure-functie uit te voeren wanneer Azure Service Bus-berichten worden gemaakt.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 1ead7fcd9d474369e3a62e372a971d88d26f4e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273570"
---
# <a name="azure-service-bus-trigger-for-azure-functions"></a>Azure Service Bus-trigger voor Azure-functies

Gebruik de trigger servicebus om te reageren op berichten vanuit een wachtrij of onderwerp van servicebus.

Zie het [overzicht](functions-bindings-service-bus-output.md)voor informatie over de installatie en configuratiedetails.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die [metagegevens van berichten](#message-metadata) leest en een wachtrijbericht van servicebus registreert:

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

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een triggerbinding servicebus in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie leest [metagegevens van berichten](#message-metadata) en registreert een wachtrijbericht servicebus.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is de C# scriptcode:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld ziet u een triggerbinding van ServiceBus in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie leest [metagegevens van berichten](#message-metadata) en registreert een wachtrijbericht servicebus. 

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

Hier is de JavaScript-scriptcode:

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

In het volgende voorbeeld wordt uitgelegd hoe u een wachtrijbericht voor servicebus lezen via een trigger.

Een servicebusbinding wordt gedefinieerd in *function.json* `serviceBusTrigger`waar *type* is ingesteld op .

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

De code * _ \_in init_\_.py* declareert een parameter als `func.ServiceBusMessage`, waarmee u het wachtrijbericht in uw functie lezen.

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

De volgende Java-functie gebruikt de `@ServiceBusQueueTrigger` annotatie van de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime) om de configuratie voor een wachtrijtrigger voor servicebus te beschrijven. De functie grijpt het bericht dat in de wachtrij wordt geplaatst en voegt het toe aan de logboeken.

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

Java-functies kunnen ook worden geactiveerd wanneer een bericht wordt toegevoegd aan een servicebusonderwerp. In het volgende `@ServiceBusTopicTrigger` voorbeeld wordt de annotatie gebruikt om de triggerconfiguratie te beschrijven.

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

## <a name="attributes-and-annotations"></a>Kenmerken en annotaties

# <a name="c"></a>[C #](#tab/csharp)

Gebruik in [klassebibliotheken van C#](functions-dotnet-class-library.md)de volgende kenmerken om een ServiceBus-trigger te configureren:

* [ServiceBusTriggerAttribuut](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusTriggerAttribute.cs)

  De constructor van het kenmerk neemt de naam van de wachtrij of het onderwerp en abonnement. In Versie 1.x van Azure Functions u ook de toegangsrechten van de verbinding opgeven. Als u geen toegangsrechten opgeeft, `Manage`is de standaardinstelling . Zie de sectie [Trigger - configuratie](#configuration) voor meer informatie.

  Hier is een voorbeeld dat het kenmerk toont dat wordt gebruikt met een tekenreeksparameter:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue")] string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  U `Connection` instellen dat de eigenschap de naam opgeeft van een app-instelling die de verbindingstekenreeks ServiceBus bevat, zoals in het volgende voorbeeld wordt weergegeven:

  ```csharp
  [FunctionName("ServiceBusQueueTriggerCSharp")]                    
  public static void Run(
      [ServiceBusTrigger("myqueue", Connection = "ServiceBusConnection")] 
      string myQueueItem, ILogger log)
  {
      ...
  }
  ```

  Zie Trigger - [voorbeeld](#example)voor een volledig voorbeeld .

* [ServicebusAccountAttribuut](https://github.com/Azure/azure-functions-servicebus-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.ServiceBus/ServiceBusAccountAttribute.cs)

  Biedt een andere manier om het te gebruiken Service Bus-account op te geven. De constructeur neemt de naam aan van een app-instelling die een servicebusverbindingstekenreeks bevat. Het kenmerk kan worden toegepast op parameter-, methode- of klassenniveau. In het volgende voorbeeld ziet u het niveau van de klasse en het methodeniveau:

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

Het te gebruiken Service Bus-account wordt in de volgende volgorde bepaald:

* De `ServiceBusTrigger` eigenschap `Connection` van het attribuut.
* Het `ServiceBusAccount` kenmerk dat is toegepast `ServiceBusTrigger` op dezelfde parameter als het kenmerk.
* Het `ServiceBusAccount` kenmerk dat op de functie wordt toegepast.
* Het `ServiceBusAccount` kenmerk dat op de klasse is toegepast.
* De app-instelling AzureWebJobsServiceBus.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

Met `ServiceBusQueueTrigger` de annotatie u een functie maken die wordt uitgevoerd wanneer een wachtrijbericht voor servicebus wordt gemaakt. Beschikbare configuratieopties zijn de naam van de wachtrij en de naam van de verbindingstekenreeks.

Met `ServiceBusTopicTrigger` de annotatie u een onderwerp en abonnement aanwijzen om te targeten welke gegevens de functie activeert.

Zie het [triggervoorbeeld](#example) voor meer details.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `ServiceBusTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**Type** | N.v.t. | Moet worden ingesteld op "serviceBusTrigger". Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op "in". Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. |
|**Naam** | N.v.t. | De naam van de variabele die het wachtrij- of onderwerpbericht in functiecode vertegenwoordigt. |
|**queueName**|**QueueName**|Naam van de wachtrij om te controleren.  Stel alleen in als u een wachtrij controleert, niet voor een onderwerp.
|**topicNaam**|**TopicName**|Naam van het onderwerp te controleren. Stel alleen in als u een onderwerp controleert, niet als wachtrij.|
|**abonnementNaam**|**SubscriptionName**|Naam van het te controleren abonnement. Stel alleen in als u een onderwerp controleert, niet als wachtrij.|
|**verbinding**|**Verbinding**|De naam van een app-instelling die de verbindingstekenreeks ServiceBus bevat om te gebruiken voor deze binding. Als de naam van de app-instelling begint met 'AzureWebJobs', u alleen de rest van de naam opgeven. Als u bijvoorbeeld `connection` instelt op 'MyServiceBus', wordt in de runtime Functies gezocht naar een app-instelling met de naam 'AzureWebJobsMyServiceBus'. Als u `connection` leeg blijft, gebruikt de runtime Van Functies de standaardverbindingstekenreeks servicebus in de app-instelling met de naam 'AzureWebJobsServiceBus'.<br><br>Als u een verbindingstekenreeks wilt verkrijgen, voert u de stappen uit die worden weergegeven bij [De beheerreferenties opvragen opvragen.](../service-bus-messaging/service-bus-quickstart-portal.md#get-the-connection-string) De verbindingstekenreeks moet zijn voor een naamruimte van servicebus, niet beperkt tot een specifieke wachtrij of onderwerp. |
|**accessRights accessRights accessRights accessRights**|**Toegang**|Toegangsrechten voor de verbindingstekenreeks. Beschikbare waarden `manage` `listen`zijn en . De standaardinstelling is `manage`, `connection` wat aangeeft dat de machtiging **Beheren** is. Als u een verbindingstekenreeks gebruikt **Manage** die geen `accessRights` machtiging beheren heeft, stelt u in op 'luisteren'. Anders kan de runtime van functies niet worden uitgevoerd om bewerkingen uit te voeren waarvoor beheerrechten vereist zijn. In Azure Functions versie 2.x en hoger is deze eigenschap niet beschikbaar omdat de nieuwste versie van de Service Bus SDK geen beheerbewerkingen ondersteunt.|
|**isSessionsEnabled**|**IsSessionsEnabled**|`true`als u verbinding maakt met een [sessiebewuste](../service-bus-messaging/message-sessions.md) wachtrij of abonnement. `false`anders, dat is de standaardwaarde.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C #](#tab/csharp)

De volgende parametertypen zijn beschikbaar voor de wachtrij of het onderwerpbericht:

* `string`- Als het bericht tekst is.
* `byte[]`- Handig voor binaire gegevens.
* Een aangepast type - Als het bericht JSON bevat, probeert Azure Functions de JSON-gegevens te deserialiseren.
* `BrokeredMessage`- Geeft u het gedeserialiseerde bericht met de methode [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Deze parametertypen zijn voor Azure Functions versie 1.x; voor 2.x en [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) hoger, `BrokeredMessage`gebruik in plaats van .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

De volgende parametertypen zijn beschikbaar voor de wachtrij of het onderwerpbericht:

* `string`- Als het bericht tekst is.
* `byte[]`- Handig voor binaire gegevens.
* Een aangepast type - Als het bericht JSON bevat, probeert Azure Functions de JSON-gegevens te deserialiseren.
* `BrokeredMessage`- Geeft u het gedeserialiseerde bericht met de methode [BrokeredMessage.GetBody\<T>().](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.getbody?view=azure-dotnet#Microsoft_ServiceBus_Messaging_BrokeredMessage_GetBody__1)

Deze parameters zijn voor Azure Functions versie 1.x; voor 2.x en [`Message`](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.message) hoger, `BrokeredMessage`gebruik in plaats van .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Toegang tot de wachtrij `context.bindings.<name from function.json>`of onderwerp bericht met behulp van . Het servicebusbericht wordt doorgegeven aan de functie als een tekenreeks of JSON-object.

# <a name="python"></a>[Python](#tab/python)

Het wachtrijbericht is beschikbaar voor de functie `func.ServiceBusMessage`via een parameter die is getypt als . Het servicebusbericht wordt doorgegeven aan de functie als een tekenreeks of JSON-object.

# <a name="java"></a>[Java](#tab/java)

Het binnenkomende Service Bus-bericht `ServiceBusQueueMessage` `ServiceBusTopicMessage` is beschikbaar via een of parameter.

[Zie het voorbeeld voor meer informatie](#example).

---

## <a name="poison-messages"></a>Gifberichten

De verwerking van gifberichten kan niet worden beheerd of geconfigureerd in Azure-functies. Service Bus behandelt giftige berichten zelf.

## <a name="peeklock-behavior"></a>PeekLock-gedrag

De runtime functies ontvangt een bericht in [de PeekLock-modus](../service-bus-messaging/service-bus-performance-improvements.md#receive-mode). Het `Complete` roept het bericht aan als de `Abandon` functie succesvol is voltooid of aanroept als de functie mislukt. Als de functie langer `PeekLock` duurt dan de time-out, wordt het slot automatisch verlengd zolang de functie wordt uitgevoerd. 

Het `maxAutoRenewDuration` is configureerbaar in *host.json*, die kaarten aan [OnMessageOptions.MaxAutoRenewDuration](https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.messagehandleroptions.maxautorenewduration?view=azure-dotnet). Het maximaal toegestane aantal voor deze instelling is 5 minuten volgens de documentatie van de ServiceBus, terwijl u de tijdslimiet voor functies verhogen van de standaardinstelling van 5 minuten naar 10 minuten. Voor servicebusfuncties zou u dat dan niet willen doen, omdat u de verlengingslimiet voor servicebus overschrijdt.

## <a name="message-metadata"></a>Metagegevens van berichten

De trigger servicebus biedt verschillende [metagegevenseigenschappen.](./functions-bindings-expressions-patterns.md#trigger-metadata) Deze eigenschappen kunnen worden gebruikt als onderdeel van bindende expressies in andere bindingen of als parameters in uw code. Deze eigenschappen zijn lid van de klasse [BrokeredMessage.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|`DeliveryCount`|`Int32`|Het aantal leveringen.|
|`DeadLetterSource`|`string`|De dode letter bron.|
|`ExpiresAtUtc`|`DateTime`|De vervaldatum in UTC.|
|`EnqueuedTimeUtc`|`DateTime`|De in de wachtrij tijd in UTC.|
|`MessageId`|`string`|Een door de gebruiker gedefinieerde waarde die Service Bus kan gebruiken om dubbele berichten te identificeren, indien ingeschakeld.|
|`ContentType`|`string`|Een inhoudstype-id die door de afzender en ontvanger wordt gebruikt voor toepassingsspecifieke logica.|
|`ReplyTo`|`string`|Het antwoord op het wachtrijadres.|
|`SequenceNumber`|`Int64`|Het unieke nummer dat is toegewezen aan een bericht van de ServiceBus.|
|`To`|`string`|Het verzenden naar adres.|
|`Label`|`string`|Het toepassingsspecifieke label.|
|`CorrelationId`|`string`|De correlatie-ID.|

Zie [codevoorbeelden](#example) die deze eigenschappen eerder in dit artikel gebruiken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Service Bus-berichten verzenden vanuit Azure-functies (uitvoerbinding)](./functions-bindings-service-bus-output.md)
