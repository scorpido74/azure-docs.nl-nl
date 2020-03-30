---
title: Opwarmtrigger azure-functies
description: Meer informatie over het gebruik van de opwarmtrigger in Azure-functies.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure-functies, functies, gebeurtenisverwerking, warming-up, koude start, premium, dynamische compute, serverloze architectuur
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167315"
---
# <a name="azure-functions-warm-up-trigger"></a>Opwarmtrigger azure-functies

In dit artikel wordt uitgelegd hoe u werken met de opwarmtrigger in Azure-functies. De opwarmtrigger wordt alleen ondersteund voor functie-apps die worden uitgevoerd in een [Premium-abonnement.](functions-premium-plan.md) Er wordt een opwarmtrigger aangeroepen wanneer een instantie wordt toegevoegd om een functie-app voor hardlopen te schalen. U een opwarmtrigger gebruiken om aangepaste afhankelijkheden vooraf te laden tijdens het [voorverwarmende proces,](./functions-premium-plan.md#pre-warmed-instances) zodat uw functies klaar zijn om onmiddellijk te beginnen met het verwerken van aanvragen. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>Pakketten - Functies 2.x en hoger

Het [NuGet-pakket microsoft.azure.webjobs.extensions,versie](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) **3.0.5 of hoger** is vereist. Broncode voor het pakket bevindt zich in de [GitHub-repository van Azure-Webjobs-sdk-extensions.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Met de opwarmtrigger u een functie definiëren die wordt uitgevoerd op een nieuw exemplaar wanneer deze wordt toegevoegd aan uw hardloopapp. U een opwarmfunctie gebruiken om verbindingen te openen, afhankelijkheden te laden of andere aangepaste logica uit te voeren voordat uw app verkeer begint te ontvangen. 

De opwarmtrigger is bedoeld om gedeelde afhankelijkheden te maken die worden gebruikt door de andere functies in uw app. [Bekijk hier voorbeelden van gedeelde afhankelijkheden.](./manage-connections.md#client-code-examples)

Houd er rekening mee dat de opwarmtrigger alleen wordt aangeroepen tijdens scale-outbewerkingen, niet tijdens het opnieuw opstarten of andere niet-schaal start-ups. U moet ervoor zorgen dat uw logica alle benodigde afhankelijkheden kan laden zonder de opwarmtrigger te gebruiken. Lazy loading is een goed patroon om dit te bereiken.

## <a name="trigger---example"></a>Trigger - voorbeeld

# <a name="c"></a>[C #](#tab/csharp)

In het volgende voorbeeld wordt een [C#-functie](functions-dotnet-class-library.md) weergegeven die op elk nieuw exemplaar wordt uitgevoerd wanneer deze aan uw app wordt toegevoegd. Een toeschrijfattribuut retourwaarde is niet vereist.


* Uw functie moet ```warmup``` een naam hebben (case-ongevoelig) en er kan slechts één opwarmfunctie per app zijn.
* Als u warming-up wilt gebruiken als een .NET-klassebibliotheekfunctie, moet u een pakketverwijzing naar **Microsoft.Azure.WebJobs.Extensions >= 3.0.5**
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


Tijdelijke opmerkingen geven aan waar in de toepassing gedeelde afhankelijkheden moeten worden aangegeven en geïnitialiseren. 
[Meer informatie over gedeelde afhankelijkheden vindt u hier](./manage-connections.md#client-code-examples).

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C# Script](#tab/csharp-script)


In het volgende voorbeeld wordt een opwarmtrigger weergegeven in een *function.json-bestand* en een [C#-scriptfunctie](functions-reference-csharp.md) die op elk nieuw exemplaar wordt uitgevoerd wanneer deze aan uw app wordt toegevoegd.

Uw functie moet ```warmup``` een naam hebben (case-ongevoelig) en er kan slechts één opwarmfunctie per app zijn.

Hier is het *function.json* bestand:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

In de [configuratiesectie](#trigger---configuration) worden deze eigenschappen uitgelegd.

Hier is C# scriptcode die `HttpRequest`zich bindt aan:

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

In het volgende voorbeeld wordt een opwarmtrigger weergegeven in een *function.json-bestand* en een [JavaScript-functie](functions-reference-node.md) die op elk nieuw exemplaar wordt uitgevoerd wanneer deze aan uw app wordt toegevoegd.

Uw functie moet ```warmup``` een naam hebben (case-ongevoelig) en er kan slechts één opwarmfunctie per app zijn.

Hier is het *function.json* bestand:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

In de [configuratiesectie](#trigger---configuration) worden deze eigenschappen uitgelegd.

Hier is de JavaScript-code:

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld wordt een opwarmtrigger weergegeven in een *function.json-bestand* en een [Python-functie](functions-reference-python.md) die op elk nieuw exemplaar wordt uitgevoerd wanneer deze aan uw app wordt toegevoegd.

Uw functie moet ```warmup``` een naam hebben (case-ongevoelig) en er kan slechts één opwarmfunctie per app zijn.

Hier is het *function.json* bestand:

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

In de [configuratiesectie](#trigger---configuration) worden deze eigenschappen uitgelegd.

Hier is de Python-code:

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voorbeeld wordt een opwarmtrigger weergegeven die wordt uitgevoerd wanneer elk nieuw exemplaar aan uw app wordt toegevoegd.

Uw functie moet `warmup` een naam hebben (case-ongevoelig) en er kan slechts één opwarmfunctie per app zijn.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>Trigger - kenmerken

In [klassebibliotheken van C#](functions-dotnet-class-library.md)is het kenmerk beschikbaar om de `WarmupTrigger` functie te configureren.

# <a name="c"></a>[C #](#tab/csharp)

In dit voorbeeld wordt uitgelegd hoe u het [kenmerk opwarmt.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs)

Houd er rekening mee ```Warmup``` dat uw functie moet worden aangeroepen en dat er slechts één opwarmfunctie per app kan zijn.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

Zie het [voorbeeld van](#trigger---example)de trigger voor een volledig voorbeeld .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

# <a name="java"></a>[Java](#tab/java)

De opwarmtrigger wordt niet ondersteund in Java als attribuut.

---

## <a name="trigger---configuration"></a>Trigger - configuratie

In de volgende tabel worden de bindende configuratie-eigenschappen uitgelegd `WarmupTrigger` die u instelt in het *function.json-bestand* en het kenmerk.

|functie.json, eigenschap | Eigenschap Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
| **Type** | N.v.t.| Vereist - moet `warmupTrigger`worden ingesteld op . |
| **direction** | N.v.t.| Vereist - moet `in`worden ingesteld op . |
| **Naam** | N.v.t.| Vereist - de variabele naam die wordt gebruikt in functiecode.|

## <a name="trigger---usage"></a>Trigger - gebruik

Er wordt geen aanvullende informatie verstrekt aan een opwarmfunctie wanneer deze wordt aangeroepen.

## <a name="trigger---limits"></a>Trigger - limieten

* De opwarmtrigger is alleen beschikbaar voor apps die op het [Premium-abonnement](./functions-premium-plan.md)worden uitgevoerd.
* De opwarmtrigger wordt alleen aangeroepen tijdens scale-upbewerkingen, niet tijdens het opnieuw opstarten of andere niet-schaal start-ups. U moet ervoor zorgen dat uw logica alle benodigde afhankelijkheden kan laden zonder de opwarmtrigger te gebruiken. Lazy loading is een goed patroon om dit te bereiken.
* De opwarmtrigger kan niet worden aangeroepen zodra een instantie al wordt uitgevoerd.
* Er kan slechts één opwarmtriggerfunctie per functie-app zijn.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over triggers en bindingen voor Azure-functies](functions-triggers-bindings.md)
