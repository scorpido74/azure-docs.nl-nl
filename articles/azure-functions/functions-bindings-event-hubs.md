---
title: Azure Event Hubs-bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Event Hubs-bindingen in Azure Functions.
author: craigshoemaker
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.topic: reference
ms.date: 11/08/2017
ms.author: cshoe
ms.openlocfilehash: 2e76853a7b1bf2e6dfda84ffa1454074c266d2c1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227283"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Azure Event Hubs-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met [Azure Event hubs](../event-hubs/event-hubs-what-is-event-hubs.md) -bindingen voor Azure functions. Azure Functions ondersteunt trigger-en uitvoer bindingen voor Event Hubs.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

Voor Azure Functions versie 1. x zijn de Event Hubs-bindingen in het [micro soft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-pakket, versie 2. x.
De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) .


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

Voor de functies 2. x gebruikt u het pakket [micro soft. Azure. webjobs. Extensions. Event hubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) , versie 3. x.
De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
