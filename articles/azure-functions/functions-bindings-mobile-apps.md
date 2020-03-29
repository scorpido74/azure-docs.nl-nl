---
title: Bindingen voor mobiele apps voor Azure-functies
description: Meer informatie over het gebruik van Azure Mobile Apps-bindingen in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76120538"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Bindingen voor mobiele apps voor Azure-functies 

> [!NOTE]
> Azure Mobile Apps-bindingen zijn alleen beschikbaar voor Azure Functions 1.x. Ze worden niet ondersteund in Azure Functions 2.x en hoger.

In dit artikel wordt uitgelegd hoe u werken met [Azure Mobile Apps-bindingen](../app-service-mobile/app-service-mobile-value-prop.md) in Azure-functies. Azure Functions ondersteunt invoer- en uitvoerbindingen voor mobiele apps.

Met de bindingen voor mobiele apps u gegevenstabellen in mobiele apps lezen en bijwerken.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - Functies 1.x

Bindingen voor mobiele apps worden geleverd in het [NuGet-pakket Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet, versie 1.x. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/)

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Invoer

De invoerbinding mobiele apps laadt een record van een eindpunt van een mobiele tabel en geeft deze door aan uw functie. In de functies C# en F# worden wijzigingen in de record automatisch teruggestuurd naar de tabel wanneer de functie wordt afgesloten.

## <a name="input---example"></a>Invoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C#-script (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Voorbeeld van invoer - C#-script

In het volgende voorbeeld wordt een invoerbinding voor Mobiele apps weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie wordt geactiveerd door een wachtrijbericht met een record-id. De functie leest de opgegeven record `Text` en wijzigt de eigenschap.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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
In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

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

### <a name="input---javascript"></a>Invoer - JavaScript

In het volgende voorbeeld wordt een invoerbinding voor mobiele apps weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie wordt geactiveerd door een wachtrijbericht met een record-id. De functie leest de opgegeven record `Text` en wijzigt de eigenschap.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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
In de [configuratiesectie](#input---configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Invoer - kenmerken

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Zie [de volgende configuratiesectie](#input---configuration)voor informatie over kenmerkeigenschappen die u configureren.

## <a name="input---configuration"></a>Invoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `MobileTable` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **Type**| N.v.t. | Moet worden ingesteld op "mobileTable"|
| **direction**| N.v.t. |Moet worden ingesteld op "in"|
| **Naam**| N.v.t. | Naam van de invoerparameter in functiehandtekening.|
|**tableName** |**Tabelnaam**|Naam van de gegevenstabel van de mobiele app|
| **id**| **Id** | De id van de record op te halen. Kan statisch zijn of gebaseerd zijn op de trigger die de functie aanroept. Als u bijvoorbeeld een wachtrijtrigger voor uw `"id": "{queueTrigger}"` functie gebruikt, gebruikt u de tekenreekswaarde van het wachtrijbericht als de record-id om op te halen.|
|**verbinding**|**Verbinding**|De naam van een app-instelling met de URL van de mobiele app. De functie gebruikt deze URL om de vereiste REST-bewerkingen te construeren ten opzichte van uw mobiele app. Maak een app-instelling in uw functie-app die de URL van de mobiele `connection` app bevat en geef vervolgens de naam op van de app-instelling in de eigenschap in uw invoerbinding. De URL `http://<appname>.azurewebsites.net`ziet eruit als .
|**apiKey**|**ApiKey (ApiKey)**|De naam van een app-instelling met de API-sleutel van uw mobiele app. Geef de API-sleutel op als u [een API-sleutel implementeert in uw mobiele Node.js-app](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)of [een API-sleutel implementeert in uw mobiele .NET-app.](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) Als u de sleutel wilt opgeven, maakt u een app-instelling `apiKey` in uw functie-app die de API-sleutel bevat en voegt u de eigenschap in uw invoerbinding toe met de naam van de app-instelling. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Deel de API-sleutel niet met uw mobiele app-clients. Het moet alleen veilig worden gedistribueerd naar clients aan de servicezijde, zoals Azure-functies. Azure Functions slaat uw verbindingsgegevens en API-sleutels op als app-instellingen, zodat ze niet worden ingecheckt in de opslagplaats voor bronbeheer. Dit beschermt uw gevoelige informatie.

## <a name="input---usage"></a>Invoer - gebruik

In C#-functies wordt deze doorgegeven aan de parameter [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) wanneer de record met de opgegeven id wordt gevonden. Wanneer de record niet wordt gevonden, is `null`de parameterwaarde . 

In JavaScript-functies wordt de `context.bindings.<name>` record doorgegeven aan het object. Wanneer de record niet wordt gevonden, is `null`de parameterwaarde . 

In de functies C# en F# worden wijzigingen die u aanbrengt in de invoerrecord (invoerparameter) automatisch teruggestuurd naar de tabel wanneer de functie wordt afgesloten. U een record in JavaScript-functies niet wijzigen.

## <a name="output"></a>Uitvoer

Gebruik de uitvoerbinding voor mobiele apps om een nieuwe record naar een tabel Met mobiele apps te schrijven.  

## <a name="output---example"></a>Uitvoer - voorbeeld

Zie het taalspecifieke voorbeeld:

* [C #](#output---c-example)
* [C#-script (.csx)](#output---c-script-example)
* [Javascript](#output---javascript-example)

### <a name="output---c-example"></a>Uitvoer - C#-voorbeeld

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die wordt geactiveerd door een wachtrijbericht en een record maakt in een mobiele app-tabel.

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

### <a name="output---c-script-example"></a>Uitvoer - C#-scriptvoorbeeld

In het volgende voorbeeld wordt een uitvoerbinding voor mobiele apps weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die de binding gebruikt. De functie wordt geactiveerd door een wachtrijbericht en maakt een `Text` nieuwe record met een hardgecodeerde waarde voor de eigenschap.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#output---configuration) worden deze eigenschappen uitgelegd.

Hier is de C# scriptcode:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Uitvoer - JavaScript-voorbeeld

In het volgende voorbeeld wordt een uitvoerbinding voor mobiele apps weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die de binding gebruikt. De functie wordt geactiveerd door een wachtrijbericht en maakt een `Text` nieuwe record met een hardgecodeerde waarde voor de eigenschap.

Hier zijn de bindende gegevens in het *function.json-bestand:*

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

In de [configuratiesectie](#output---configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Uitvoer - kenmerken

Gebruik [in klassebibliotheken van C#](functions-dotnet-class-library.md)het kenmerk [MobileTable.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs)

Zie [Uitvoer - configuratie](#output---configuration)voor informatie over kenmerkeigenschappen die u configureren. Hier is `MobileTable` een kenmerkvoorbeeld in een methodehandtekening:

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

Zie Voorbeeld uitvoer [- C#](#output---c-example)voor een volledig voorbeeld .

## <a name="output---configuration"></a>Uitvoer - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `MobileTable` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **Type**| N.v.t. | Moet worden ingesteld op "mobileTable"|
| **direction**| N.v.t. |Moet worden ingesteld op "out"|
| **Naam**| N.v.t. | Naam van de uitvoerparameter in functiehandtekening.|
|**tableName** |**Tabelnaam**|Naam van de gegevenstabel van de mobiele app|
|**verbinding**|**MobileAppuriSetting**|De naam van een app-instelling met de URL van de mobiele app. De functie gebruikt deze URL om de vereiste REST-bewerkingen te construeren ten opzichte van uw mobiele app. Maak een app-instelling in uw functie-app die de URL van de mobiele `connection` app bevat en geef vervolgens de naam op van de app-instelling in de eigenschap in uw invoerbinding. De URL `http://<appname>.azurewebsites.net`ziet eruit als .
|**apiKey**|**ApiKeySetting**|De naam van een app-instelling met de API-sleutel van uw mobiele app. Geef de API-sleutel op als u [een API-sleutel implementeert in de backend van uw mobiele node.js-app](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)of [een API-sleutel implementeert in de backend van uw mobiele.NET-app](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Als u de sleutel wilt opgeven, maakt u een app-instelling `apiKey` in uw functie-app die de API-sleutel bevat en voegt u de eigenschap in uw invoerbinding toe met de naam van de app-instelling. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Deel de API-sleutel niet met uw mobiele app-clients. Het moet alleen veilig worden gedistribueerd naar clients aan de servicezijde, zoals Azure-functies. Azure Functions slaat uw verbindingsgegevens en API-sleutels op als app-instellingen, zodat ze niet worden ingecheckt in de opslagplaats voor bronbeheer. Dit beschermt uw gevoelige informatie.

## <a name="output---usage"></a>Uitvoer - gebruik

Gebruik in Scriptfuncties van C# een `out object` benoemde uitvoerparameter van het type om toegang te krijgen tot de uitvoerrecord. In klassebibliotheken van C# kan het `MobileTable` kenmerk worden gebruikt met een van de volgende typen:

* `ICollector<T>`of, `IAsyncCollector<T>` `T` waar `JObject` is een of `public string Id` welk type met een eigenschap.
* `out JObject`
* `out T`of, `out T[]` `T` waar is een `public string Id` type met een eigenschap.

Gebruik in de functies `context.bindings.<name>` Node.js om toegang te krijgen tot de uitvoerrecord.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)
