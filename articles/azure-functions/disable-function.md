---
title: Functies in Azure Functions uitschakelen
description: Meer informatie over het uitschakelen en inschakelen van functies in Azure Functions 1. x en 2. x.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7968580fcaa40575571a41f067fa74fbdc0a3a34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233048"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Functies in Azure Functions uitschakelen

In dit artikel wordt uitgelegd hoe u een functie in Azure Functions kunt uitschakelen. Als u een functie wilt *uitschakelen* , moet u ervoor zorgen dat de runtime de automatische trigger die voor de functie is gedefinieerd, negeert. Hoe u dat doet, is afhankelijk van de runtime versie en de programmeer taal:

* Functies 2. x:
  * Eén manier voor alle talen
  * Optionele manier voor C# class-bibliotheken
* Functions 1. x:
  * Script talen
  * C#Class-bibliotheken

## <a name="functions-2x---all-languages"></a>Functies 2. x-alle talen

In functions 2. x, schakelt u een functie uit met behulp van een app-instelling in de notatie `AzureWebJobs.<FUNCTION_NAME>.Disabled`. U kunt deze toepassings instelling op verschillende manieren maken en wijzigen, zoals met behulp van [Azure cli](/cli/azure/) en op het tabblad **beheren** van uw functie in de [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure-CLI

In azure CLI gebruikt u de opdracht [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) om de app-instelling te maken en te wijzigen. Met de volgende opdracht wordt een functie met de naam `QueueTrigger` uitgeschakeld door het maken van een app-instelling met de naam `AzureWebJobs.QueueTrigger.Disabled` deze instellen op `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Als u de functie opnieuw wilt inschakelen, voert u dezelfde opdracht opnieuw uit met de waarde `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

U kunt ook de **functie status** switch op het tabblad **beheren** van de functie gebruiken. De switch werkt door het maken en verwijderen van de `AzureWebJobs.<FUNCTION_NAME>.Disabled` app-instelling.

![Functie status schakelaar](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functies 2. x- C# class-bibliotheken

In een functions 2. x-klassebibliotheek wordt u aangeraden de methode te gebruiken die geschikt is voor alle talen. Maar als u wilt, kunt u [het kenmerk uitschakelen gebruiken als in de functies 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1. x-script talen

Voor script talen als C# script en Java script gebruikt u de eigenschap `disabled` van het bestand *Function. json* om de runtime te laten weten dat er geen functie moet worden geactiveerd. Deze eigenschap kan worden ingesteld op `true` of op de naam van een app-instelling:

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

In het tweede voor beeld is de functie uitgeschakeld wanneer er een app-instelling met de naam IS_DISABLED is ingesteld op `true` of 1.

U kunt het bestand bewerken in de Azure Portal of de schakel optie **status** op het tabblad **beheren** van de functie gebruiken. De portal-switch werkt door het bestand *Function. json* te wijzigen.

![Functie status schakelaar](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1. x C# -class-bibliotheken

In een functions 1. x-klassebibliotheek gebruikt u een `Disable` kenmerk om te voor komen dat een functie wordt geactiveerd. U kunt het kenmerk zonder een constructor-para meter gebruiken, zoals wordt weer gegeven in het volgende voor beeld:

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

Het kenmerk zonder een constructor-para meter vereist dat u het project opnieuw compileert en implementeert om de uitgeschakelde status van de functie te wijzigen. Een flexibele manier om het kenmerk te gebruiken is door een constructor-para meter op te geven die verwijst naar een Booleaanse app-instelling, zoals wordt weer gegeven in het volgende voor beeld:

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

Met deze methode kunt u de functie in-en uitschakelen door de app-instelling te wijzigen zonder opnieuw te compileren of opnieuw te implementeren. Als u een app-instelling wijzigt, wordt de functie-app opnieuw gestart, zodat de status wijziging in de modus onmiddellijk wordt herkend.

> [!IMPORTANT]
> Het kenmerk `Disabled` is de enige manier om een klassen bibliotheek functie uit te scha kelen. Het gegenereerde *Function. json* -bestand voor een klassen bibliotheek functie is niet bedoeld om rechtstreeks te worden bewerkt. Als u het bestand bewerkt, wordt de eigenschap `disabled` niet toegepast.
>
> Hetzelfde geldt voor de **functie status** switch op het tabblad **beheren** , omdat deze werkt door het bestand *Function. json* te wijzigen.
>
> Houd er ook rekening mee dat de portal kan aangeven dat de functie is uitgeschakeld wanneer dat niet het geval is.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is informatie over het uitschakelen van automatische triggers. Zie [Triggers en bindingen](functions-triggers-bindings.md)voor meer informatie over triggers.
