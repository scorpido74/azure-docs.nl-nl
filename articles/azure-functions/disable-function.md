---
title: Functies uitschakelen in Azure-functies
description: Meer informatie over het uitschakelen en inschakelen van functies in Azure-functies.
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: fb8edf635856078655b8640ba0e1723fdd5e8a5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77116146"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Functies uitschakelen in Azure-functies

In dit artikel wordt uitgelegd hoe u een functie uitschakelt in Azure-functies. Als u een functie *uitschakelt,* moet de runtime de automatische trigger negeren die voor de functie is gedefinieerd. Hiermee u voorkomen dat een specifieke functie wordt uitgevoerd zonder de hele functie-app te stoppen.

De aanbevolen manier om een functie uit te schakelen `AzureWebJobs.<FUNCTION_NAME>.Disabled`is door een app-instelling in de indeling te gebruiken. U deze toepassingsinstelling op verschillende manieren maken en wijzigen, onder meer door het [Azure CLI](/cli/azure/) te gebruiken en het tabblad **Beheren van** uw functie in de [Azure-portal.](https://portal.azure.com) 

> [!NOTE]  
> Wanneer u een door HTTP geactiveerde functie uitschakelt met behulp van de in dit artikel beschreven methoden, kan het eindpunt nog steeds toegankelijk zijn wanneer het wordt uitgevoerd op uw lokale computer.  

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In de Azure CLI [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) gebruikt u de opdracht om de app-instelling te maken en te wijzigen. Met de volgende opdracht `QueueTrigger` wordt een functie `AzureWebJobs.QueueTrigger.Disabled` met de `true`naam uitgeschakeld door een app-instelling met de naam in te stellen op . 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Als u de functie opnieuw wilt inschakelen, voert `false`u dezelfde opdracht opnieuw uit met een waarde van .

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>De portal gebruiken

U ook de **functiestatusinschakelen** gebruiken op het tabblad **Beheren van** de functie. De switch werkt door de `AzureWebJobs.<FUNCTION_NAME>.Disabled` app-instelling te maken en te verwijderen.

![Functiestatusschakelaar](media/disable-function/function-state-switch.png)

## <a name="other-methods"></a>Andere methoden

Hoewel de toepassingsinstellingsmethode wordt aanbevolen voor alle talen en alle runtime-versies, zijn er verschillende andere manieren om functies uit te schakelen. Deze methoden, die variëren per taal en runtime-versie, worden gehandhaafd voor achterwaartse compatibiliteit. 

### <a name="c-class-libraries"></a>C# klasse bibliotheken

In een klassebibliotheekfunctie u `Disable` het kenmerk ook gebruiken om te voorkomen dat de functie wordt geactiveerd. U het kenmerk gebruiken zonder constructorparameter, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Het kenmerk zonder constructorparameter vereist dat u het project opnieuw compileert en opnieuw implementeert om de uitgeschakelde status van de functie te wijzigen. Een flexibelere manier om het kenmerk te gebruiken, is door een constructorparameter op te nemen die verwijst naar een Booleaanse app-instelling, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Met deze methode u de functie in- en uitschakelen door de app-instelling te wijzigen, zonder opnieuw te compileren of opnieuw te implementeren. Als u een app-instelling wijzigt, wordt de functie-app opnieuw opgestart, zodat de statuswijziging uitgeschakeld onmiddellijk wordt herkend.

> [!IMPORTANT]
> Het `Disabled` kenmerk is de enige manier om een klassebibliotheekfunctie uit te schakelen. Het gegenereerde *function.json-bestand* voor een klassebibliotheekfunctie is niet bedoeld om direct te worden bewerkt. Als u dat bestand bewerkt, heeft `disabled` alles wat u met de eigenschap doet geen effect.
>
> Hetzelfde geldt voor de **functiestatusschakelaar** op het tabblad **Beheren,** omdat het werkt door het *bestand function.json te* wijzigen.
>
> Houd er ook rekening mee dat de portal kan aangeven dat de functie is uitgeschakeld wanneer dit niet het is.

### <a name="functions-1x---scripting-languages"></a>Functies 1.x - scripttalen

In versie 1.x u `disabled` ook de eigenschap van het *function.json-bestand* gebruiken om de runtime te vertellen dat u geen functie moet activeren. Deze methode werkt alleen voor scripttalen zoals C# script en JavaScript. De `disabled` eigenschap kan `true` worden ingesteld op of op de naam van een app-instelling:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
of 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

In het tweede voorbeeld wordt de functie uitgeschakeld wanneer er een app-instelling is die IS_DISABLED heet en is ingesteld op `true` of 1.

U het bestand bewerken in de Azure-portal of de **functiestatusinschakelen** gebruiken op het tabblad **Beheren van** de functie. De portalswitch werkt door het *functie.json-bestand te* wijzigen.

![Functiestatusschakelaar](media/disable-function/function-state-switch.png)

## <a name="next-steps"></a>Volgende stappen

Dit artikel gaat over het uitschakelen van automatische triggers. Zie [Triggers en bindingen](functions-triggers-bindings.md)voor meer informatie over triggers.
