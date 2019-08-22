---
title: Functies in Azure Functions uitschakelen
description: Meer informatie over het uitschakelen en inschakelen van functies in Azure Functions 1. x en 2. x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 498bb8c0f1e7bb674605d4a98f0be0f3e0b9a7c9
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650495"
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

In functions 2. x, schakelt u een functie uit met behulp van een app `AzureWebJobs.<FUNCTION_NAME>.Disabled`-instelling in de indeling. U kunt deze toepassings instelling op verschillende manieren maken en wijzigen, zoals met behulp van [Azure cli](/cli/azure/) en op het tabblad **beheren** van uw functie in de [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Azure-CLI

In azure cli gebruikt u de [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) opdracht om de app-instelling te maken en te wijzigen. Met de volgende opdracht wordt een functie uitgeschakeld die `QueueTrigger` wordt genoemd door het maken van `AzureWebJobs.QueueTrigger.Disabled` een app- `true`instelling met de naam ingesteld op. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Als u de functie opnieuw wilt inschakelen, voert u dezelfde opdracht opnieuw uit met `false`de waarde.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

U kunt ook de **functie status** switch op het tabblad **beheren** van de functie gebruiken. De switch werkt door het maken en verwijderen `AzureWebJobs.<FUNCTION_NAME>.Disabled` van de app-instelling.

![Functie status schakelaar](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functies 2. x- C# class-bibliotheken

In een functions 2. x-klassebibliotheek wordt u aangeraden de methode te gebruiken die geschikt is voor alle talen. Maar als u wilt, kunt u [het kenmerk uitschakelen gebruiken als in de functies 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1. x-script talen

Voor script talen als C# script en Java script gebruikt u de `disabled` eigenschap van het bestand *Function. json* om de runtime te laten weten dat er geen functie moet worden geactiveerd. Deze eigenschap kan worden ingesteld op `true` de naam van een app-instelling:

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

In het tweede voor beeld is de functie uitgeschakeld wanneer er een app-instelling met de naam IS_DISABLED is en is ingesteld `true` op of 1.

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
> Het `Disabled` kenmerk is de enige manier om een klassen bibliotheek functie uit te scha kelen. Het gegenereerde *Function. json* -bestand voor een klassen bibliotheek functie is niet bedoeld om rechtstreeks te worden bewerkt. Als u het bestand bewerkt, heeft alles wat u naar `disabled` de eigenschap doet geen effect.
>
> Hetzelfde geldt voor de **functie status** switch op het tabblad **beheren** , omdat deze werkt door het bestand *Function. json* te wijzigen.
>
> Houd er ook rekening mee dat de portal kan aangeven dat de functie is uitgeschakeld wanneer dat niet het geval is.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is informatie over het uitschakelen van automatische triggers. Zie [Triggers en bindingen](functions-triggers-bindings.md)voor meer informatie over triggers.
