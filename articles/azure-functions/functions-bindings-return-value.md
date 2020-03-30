---
title: Retourwaarde van een Azure-functie gebruiken
description: Informatie over het beheren van retourwaarden voor Azure-functies
author: craigshoemaker
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 7ba104e288204dfbf3d24f5783bf69682a286553
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74480574"
---
# <a name="using-the-azure-function-return-value"></a>De retourwaarde azure-functie gebruiken

In dit artikel wordt uitgelegd hoe retourwaarden werken binnen een functie.

In talen met een retourwaarde u een [functieuitvoer](./functions-triggers-bindings.md#binding-direction) binden die bindend is voor de retourwaarde:

* Pas in een klassebibliotheek van C# het eigenschap uitvoerbinding toe op de retourwaarde van de methode.
* Pas in Java de uitvoerbinding sannotatie toe op de functiemethode.
* Stel in andere `name` talen de eigenschap in `$return` *function.json* in op .

Als er meerdere uitvoerbindingen zijn, gebruikt u de retourwaarde voor slechts één van deze bindingen.

In het script C# en C# zijn `out` alternatieve manieren om gegevens naar een uitvoerbinding te verzenden parameters en [verzamelobjecten](functions-reference-csharp.md#writing-multiple-output-values).

# <a name="c"></a>[C #](#tab/csharp)

Hier is C#-code die de retourwaarde gebruikt voor een uitvoerbinding, gevolgd door een voorbeeld van async:

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static string Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
[FunctionName("QueueTrigger")]
[return: Blob("output-container/{id}")]
public static Task<string> Run([QueueTrigger("inputqueue")]WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Hier is de uitvoerbinding in het *bestand function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Hier is de C# scriptcode, gevolgd door een voorbeeld van async:

```cs
public static string Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
public static Task<string> Run(WorkItem input, ILogger log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.LogInformation($"C# script processed queue message. Item={json}");
    return Task.FromResult(json);
}
```

# <a name="f"></a>[F #](#tab/fsharp)

Hier is de uitvoerbinding in het *bestand function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Hier is de F # code:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

Hier is de uitvoerbinding in het *bestand function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

In JavaScript gaat de retourwaarde in `context.done`de tweede parameter voor:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

# <a name="python"></a>[Python](#tab/python)

Hier is de uitvoerbinding in het *bestand function.json:*

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Hier is de Python-code:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

# <a name="java"></a>[Java](#tab/java)

Hier is java-code die de retourwaarde gebruikt voor een uitvoerbinding:

```java
@FunctionName("QueueTrigger")
@StorageAccount("AzureWebJobsStorage")
@BlobOutput(name = "output", path = "output-container/{id}")
public static String run(
  @QueueTrigger(name = "input", queueName = "inputqueue") WorkItem input,
  final ExecutionContext context
) {
  String json = String.format("{ \"id\": \"%s\" }", input.id);
  context.getLogger().info("Java processed queue message. Item=" + json);
  return json;
}
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bindingsfouten voor Azure-functies verwerken](./functions-bindings-errors.md)
