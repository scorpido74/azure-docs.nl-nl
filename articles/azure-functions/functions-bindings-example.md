---
title: Voorbeeld van Azure-functies activeren en binden
description: Informatie over het configureren van Azure-functiebindingen
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8685c0fe02ad6c68918736e857c2015e2bfb4595
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227246"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Voorbeeld van Azure-functies activeren en binden

In dit artikel wordt uitgelegd hoe u een [trigger en bindingen configureert](./functions-triggers-bindings.md) in een Azure-functie.

Stel dat u een nieuwe rij wilt schrijven naar Azure Table-opslag wanneer een nieuw bericht wordt weergegeven in Azure Queue-opslag. Dit scenario kan worden geïmplementeerd met een Azure Queue Storage-trigger en een Azure Table Storage-uitvoerbinding. 

Hier is een *function.json* bestand voor dit scenario. 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Het eerste element `bindings` in de array is de trigger voor wachtrijopslag. De `type` `direction` eigenschappen en eigenschappen identificeren de trigger. De `name` eigenschap identificeert de functieparameter die de inhoud van het wachtrijbericht ontvangt. De naam van de wachtrij `queueName`die moet worden bewaakt, bevindt `connection`zich en de verbindingstekenreeks bevindt zich in de app-instelling die wordt geïdentificeerd door .

Het tweede element `bindings` in de array is de ambs-uitvoerbinding voor tabelopslag. De `type` `direction` eigenschappen en eigenschappen identificeren de binding. De `name` eigenschap geeft aan hoe de functie de nieuwe tabelrij biedt, in dit geval met behulp van de functieretourwaarde. De naam van de `tableName`tabel bevindt zich in en de `connection`verbindingstekenreeks bevindt zich in de app-instelling die is geïdentificeerd door .

Als u de inhoud van *function.json* in de Azure-portal wilt weergeven en bewerken, klikt u op de optie **Geavanceerde editor** op het tabblad **Integreren** van uw functie.

> [!NOTE]
> De waarde `connection` van de naam van een app-instelling die de verbindingstekenreeks bevat, niet de verbindingstekenreeks zelf. Bindingen gebruiken verbindingstekenreeksen die zijn opgeslagen in app-instellingen om de aanbevolen procedures af te dwingen die *function.json* geen servicegeheimen bevat.

## <a name="c-script-example"></a>C# scriptvoorbeeld

Hier is C# scriptcode die werkt met deze trigger en binding. Merk op dat de naam van de `order`parameter die de inhoud van het wachtrijbericht bevat, is ; deze naam is `name` vereist omdat de eigenschapswaarde in *function.json*`order` 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript-voorbeeld

Hetzelfde *function.json-bestand* kan worden gebruikt met een JavaScript-functie:

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>Voorbeeld van klassenbibliotheek

In een klassenbibliotheek worden dezelfde &mdash; trigger- en bindingswachtrij- en tabelnamen, opslagaccounts, functieparameters voor invoer en uitvoer &mdash; geleverd door kenmerken in plaats van een function.json-bestand. Hier volgt een voorbeeld:

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

U hebt nu een werkende functie die wordt geactiveerd door een Azure Queue en gegevens naar Azure Table-opslag uitvoert.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bindingsexpressies van Azure-functies](./functions-bindings-expressions-patterns.md)
