---
title: Retour waarde van een Azure-functie gebruiken
description: Meer informatie over het beheren van retour waarden voor Azure Functions
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: reference
ms.date: 01/14/2019
ms.author: cshoe
ms.openlocfilehash: 1ea7ec0444ba80d3494afba77ad9d7fdabd5f982
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086411"
---
# <a name="using-the-azure-function-return-value"></a>De functie retour waarde van Azure gebruiken

In dit artikel wordt uitgelegd hoe resultaat waarden binnen een functie werken.

In talen die een retour waarde hebben, kunt u een functie- [uitvoer binding](./functions-triggers-bindings.md#binding-direction) binden aan de retour waarde:

* Pas in C# een klassen bibliotheek het kenmerk uitvoer binding toe op de retour waarde van de methode.
* In andere talen stelt u de `name` eigenschap in *Function. json* in `$return`op.

Als er meerdere uitvoer bindingen zijn, gebruikt u de retour waarde voor slechts één van beide.

In C# en C# script, alternatieve manieren om gegevens te verzenden naar een uitvoer binding `out` , zijn para meters en [Collector-objecten](functions-reference-csharp.md#writing-multiple-output-values).

Zie het taalspecifieke voor beeld waarin het gebruik van de retour waarde wordt weer gegeven:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [F#](#f-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

## <a name="c-example"></a>C#Hierbij

Dit is C# de code die gebruikmaakt van de retour waarde voor een uitvoer binding, gevolgd door een async-voor beeld:

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

## <a name="c-script-example"></a>C#script voorbeeld

Hier ziet u de uitvoer binding in het bestand *Function. json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Hier volgt de C# script code, gevolgd door een async-voor beeld:

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

## <a name="f-example"></a>F#Hierbij

Hier ziet u de uitvoer binding in het bestand *Function. json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

Hier volgt de F# code:

```fsharp
let Run(input: WorkItem, log: ILogger) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.LogInformation(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="javascript-example"></a>Java script-voor beeld

Hier ziet u de uitvoer binding in het bestand *Function. json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

In Java script komt de geretourneerde waarde voor in de tweede `context.done`para meter voor:

```javascript
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

## <a name="python-example"></a>Python-voor beeld

Hier ziet u de uitvoer binding in het bestand *Function. json* :

```json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```
Dit is de python-code:

```python
def main(input: azure.functions.InputStream) -> str:
    return json.dumps({
        'name': input.name,
        'length': input.length,
        'content': input.read().decode('utf-8')
    })
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Functions bindings fouten verwerken](./functions-bindings-errors.md)
