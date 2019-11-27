---
title: Mobile Apps bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Mobile Apps-bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 474cdc52d34d3f5e00dee69b7cc9861042cbcc04
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231118"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps bindingen voor Azure Functions 

> [!NOTE]
> Azure Mobile Apps-bindingen zijn alleen beschikbaar voor Azure Functions 1. x. Ze worden niet ondersteund in Azure Functions 2. x.

In dit artikel wordt uitgelegd hoe u kunt werken met [Azure Mobile apps](../app-service-mobile/app-service-mobile-value-prop.md) -bindingen in azure functions. Azure Functions ondersteunt invoer-en uitvoer bindingen voor Mobile Apps.

Met de Mobile Apps bindingen kunt u gegevens tabellen in mobiele apps lezen en bijwerken.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

Mobile Apps bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet, versie 1. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Invoer

De Mobile Apps invoer binding laadt een record uit een eind punt van een mobiele tabel en geeft deze door aan uw functie. In C# en F# werken alle wijzigingen die in de record zijn aangebracht, automatisch terug naar de tabel wanneer de functie is afgesloten.

## <a name="input---example"></a>Invoer-voor beeld

Zie het voorbeeld taalspecifieke:

* [C#script (. CSX)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Voor beeld C# van invoer script

In het volgende voor beeld ziet u een Mobile apps invoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie wordt geactiveerd door een wachtrij bericht dat een record-id heeft. Met de functie wordt de opgegeven record gelezen en wordt de eigenschap `Text` gewijzigd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Invoer-java script

In het volgende voor beeld ziet u een Mobile Apps invoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie wordt geactiveerd door een wachtrij bericht dat een record-id heeft. Met de functie wordt de opgegeven record gelezen en wordt de eigenschap `Text` gewijzigd.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
In de [configuratie](#input---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Invoer - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Voor informatie over kenmerk eigenschappen die u kunt configureren, raadpleegt u [de volgende configuratie sectie](#input---configuration).

## <a name="input---configuration"></a>Invoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `MobileTable`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **type**|| Moet worden ingesteld op ' mobileTable '|
| **direction**||Moet worden ingesteld op ' in '|
| **naam**|| Naam van invoer parameter in functie handtekening.|
|**tableName** |**TableName**|De naam van de gegevens tabel van de mobiele app|
| **id**| **Id** | De id van de record die moet worden opgehaald. Kan statisch zijn of op basis van de trigger die de functie aanroept. Als u bijvoorbeeld een wachtrij trigger gebruikt voor uw functie, gebruikt `"id": "{queueTrigger}"` de teken reeks waarde van het wachtrij bericht als de record-ID die moet worden opgehaald.|
|**Combi**|**Verbinding**|De naam van een app-instelling met de URL van de mobiele app. De functie gebruikt deze URL om de vereiste REST bewerkingen te maken voor uw mobiele app. Maak een app-instelling in de functie-app die de URL van de mobiele app bevat en geef vervolgens de naam van de app-instelling op in de eigenschap `connection` in uw invoer binding. De URL ziet eruit als `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|De naam van een app-instelling met de API-sleutel van uw mobiele app. Geef de API-sleutel op als u [een API-sleutel in uw mobiele node. js-app implementeert](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)of [een API-sleutel in uw mobiele .net-app implementeert](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Als u de sleutel wilt opgeven, maakt u een app-instelling in de functie-app die de API-sleutel bevat en voegt u vervolgens de eigenschap `apiKey` toe aan de invoer binding met de naam van de app-instelling. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Deel de API-sleutel niet met uw mobiele app-clients. Het mag alleen veilig worden gedistribueerd aan service-side clients, zoals Azure Functions. Azure Functions slaat de verbindings gegevens en API-sleutels op als app-instellingen, zodat ze niet worden ingecheckt in uw opslag plaats voor broncode beheer. Hiermee wordt uw gevoelige informatie beveiligd.

## <a name="input---usage"></a>Invoer - gebruik

In C# functions, wanneer de record met de opgegeven id wordt gevonden, wordt deze door gegeven aan de benoemde [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) -para meter. Wanneer de record niet wordt gevonden, wordt de parameter waarde `null`. 

In Java script-functies wordt de record door gegeven aan het `context.bindings.<name>`-object. Wanneer de record niet wordt gevonden, wordt de parameter waarde `null`. 

In C# en F# -functies worden alle wijzigingen die u aanbrengt in de invoer record (invoer parameter) automatisch teruggestuurd naar de tabel wanneer de functie is afgesloten. U kunt een record in Java script-functies niet wijzigen.

## <a name="output"></a>Uitvoer

Gebruik de Mobile Apps uitvoer binding om een nieuwe record te schrijven naar een Mobile Apps tabel.  

## <a name="output---example"></a>Uitvoer - voorbeeld

Zie het voorbeeld taalspecifieke:

* [C#](#output---c-example)
* [C#script (. CSX)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Uitvoer - voorbeeld met C#

In het volgende voor beeld ziet u een [ C# functie](functions-dotnet-class-library.md) die wordt geactiveerd door een wachtrij bericht en maakt u een record in een tabel met mobiele apps.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Uitvoer - voorbeeld van C#-script

In het volgende voor beeld ziet u een Mobile apps uitvoer binding in een *Function. json* -bestand en een [ C# script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie wordt geactiveerd door een bericht in de wachtrij en maakt een nieuwe record met een in code vastgelegde waarde voor de eigenschap `Text`.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C#-scriptcode:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Uitvoer - JavaScript-voorbeeld

In het volgende voor beeld ziet u een Mobile Apps uitvoer binding in een *Function. json* -bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie wordt geactiveerd door een bericht in de wachtrij en maakt een nieuwe record met een in code vastgelegde waarde voor de eigenschap `Text`.

Hier vindt u de bindings gegevens in het bestand *Function. json* :

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

In de [configuratie](#output---configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Uitvoer - kenmerken

Gebruik in [ C# class bibliotheken](functions-dotnet-class-library.md)het kenmerk [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) .

Zie [output-Configuration](#output---configuration)(Engelstalig) voor meer informatie over kenmerk eigenschappen die u kunt configureren. Hier volgt een voor beeld van een `MobileTable` kenmerk in een methode handtekening:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Zie voor een volledig voor beeld [uitvoer- C# voor beeld](#output---c-example).

## <a name="output---configuration"></a>Uitvoer - configuratie

De volgende tabel bevat informatie over de binding configuratie-eigenschappen die u hebt ingesteld in het bestand *Function. json* en het kenmerk `MobileTable`.

|de eigenschap Function.JSON | De kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **type**|| Moet worden ingesteld op ' mobileTable '|
| **direction**||Moet worden ingesteld op out|
| **naam**|| De naam van de uitvoer parameter in de functie handtekening.|
|**tableName** |**TableName**|De naam van de gegevens tabel van de mobiele app|
|**Combi**|**MobileAppUriSetting**|De naam van een app-instelling met de URL van de mobiele app. De functie gebruikt deze URL om de vereiste REST bewerkingen te maken voor uw mobiele app. Maak een app-instelling in de functie-app die de URL van de mobiele app bevat en geef vervolgens de naam van de app-instelling op in de eigenschap `connection` in uw invoer binding. De URL ziet eruit als `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|De naam van een app-instelling met de API-sleutel van uw mobiele app. Geef de API-sleutel op als u [een API-sleutel in uw back-end van de node. js-app implementeert](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)of [een API-sleutel in uw back-end van uw .net-app implementeert](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Als u de sleutel wilt opgeven, maakt u een app-instelling in de functie-app die de API-sleutel bevat en voegt u vervolgens de eigenschap `apiKey` toe aan de invoer binding met de naam van de app-instelling. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Deel de API-sleutel niet met uw mobiele app-clients. Het mag alleen veilig worden gedistribueerd aan service-side clients, zoals Azure Functions. Azure Functions slaat de verbindings gegevens en API-sleutels op als app-instellingen, zodat ze niet worden ingecheckt in uw opslag plaats voor broncode beheer. Hiermee wordt uw gevoelige informatie beveiligd.

## <a name="output---usage"></a>Uitvoer - gebruik

Gebruik C# in script functies een benoemde uitvoer parameter van het type `out object` om toegang te krijgen tot de uitvoer record. In C# class-bibliotheken kan het kenmerk `MobileTable` worden gebruikt met een van de volgende typen:

* `ICollector<T>` of `IAsyncCollector<T>`, waarbij `T` `JObject` of een type met een `public string Id` eigenschap.
* `out JObject`
* `out T` of `out T[]`, waarbij `T` elk type is met een `public string Id` eigenschap.

Gebruik in node. js-functies de `context.bindings.<name>` voor toegang tot de uitvoer record.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
