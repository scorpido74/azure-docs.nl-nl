---
title: Overzicht van Durable Functions - Azure
description: Inleiding tot de extensie Durable Functions voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 323ab530d8199dd154e5d3568c09f86f6f52d702
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087199"
---
# <a name="what-are-durable-functions"></a>Wat is Durable Functions?

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

## <a name="benefits"></a>Voordelen

Met de extensie kunt u stateful werkstromen definiëren met behulp van een [*orchestrator-functie*](durable-functions-types-features-overview.md#orchestrator-functions) wat de volgende voordelen kan bieden:

* U kunt uw werkstromen definiëren in code. Er zijn geen JSON-schema's of ontwerpers nodig.
* Andere functies kunnen zowel synchroon als asynchroon worden aangeroepen. Uitvoer van aangeroepen functies kan worden opgeslagen in lokale variabelen.
* Tijdens de voortgang worden automatisch controlepunten gemaakt wanneer de functie wacht. Er gaat nooit lokale status verloren wanneer het proces recyclet of de VM opnieuw wordt opgestart.

## <a name="application-patterns"></a>Toepassingspatronen

De primaire use case voor Durable Functions is het vereenvoudigen van complexe stateful coördinatievereisten in serverloze toepassingen. Hier volgen enkele typische toepassingspatronen die kunnen profiteren van Durable Functions:

* [Ketenvorming](durable-functions-concepts.md#chaining)
* [Fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [Asynchrone HTTP-API's](durable-functions-concepts.md#async-http)
* [Controle](durable-functions-concepts.md#monitoring)
* [Menselijke tussenkomst](durable-functions-concepts.md#human)

## <a name="language-support"></a>Ondersteunde talen

Durable Functions ondersteunt momenteel de volgende talen:

* **C#** : zowel [vooraf gecompileerde klassebibliotheken](../functions-dotnet-class-library.md) als [C#-script](../functions-reference-csharp.md).
* **F#** : vooraf gecompileerde klassebibliotheken en F#-script. F#-script wordt alleen ondersteund voor versie 1.x van de Azure Functions-runtime.
* **JavaScript**: alleen ondersteund voor versie 2.x van de Azure Functions-runtime. Versie 1.7.0 of hoger van de Durable Functions-extensie vereist. 

Durable Functions heeft als doel alle [Azure Functions-talen](../supported-languages.md) te ondersteunen. Zie [Durable Functions issues list](https://github.com/Azure/azure-functions-durable-extension/issues) voor de laatste voortgangsstatus van de ondersteuning voor meer talen.

Net als Azure Functions zijn er sjablonen waarmee u Durable Functions kunt ontwikkelen met behulp van [Visual studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)en de [Azure Portal](durable-functions-create-portal.md).

## <a name="billing"></a>Billing

Durable Functions worden op dezelfde manier in rekening gebracht als Azure Functions. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie.

## <a name="jump-right-in"></a>Duik er meteen in

U kunt in minder dan 10 minuten aan de slag gaan met Durable Functions door een van deze taalspecifieke quickstart-zelfstudies te volgen:

* [C#Visual Studio 2019 gebruiken](durable-functions-create-first-csharp.md)
* [JavaScript met Visual Studio Code](quickstart-js-vscode.md)

In beide quickstarts maakt en test u een lokale ‘hallo wereld’-duurzame functie. Vervolgens publiceert u de functiecode op Azure. De functie die u maakt, organiseert en koppelt aanroepen naar andere functies.

## <a name="learn-more"></a>Meer informatie

De volgende video laat de voordelen van Durable Functions zien:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Omdat Durable Functions een geavanceerde extensie voor [Azure Functions](../functions-overview.md) is, is het niet geschikt voor alle toepassingen. Zie voor meer informatie over Durable Functions [Durable Functions patterns and technical concepts](durable-functions-concepts.md). Zie voor een vergelijking met andere Azure-technologieën [Compare Azure Functions and Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Durable Functions-patronen en technische concepten](durable-functions-concepts.md)
