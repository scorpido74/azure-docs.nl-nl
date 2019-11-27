---
title: Azure IoT Hub-bindingen voor Azure Functions
description: Meer informatie over het gebruik van IoT Hub bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 03/05/2019
ms.author: cshoe
ms.openlocfilehash: 93a8dc600e8fadb16df8e82fbe02a1b186191d12
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227259"
---
# <a name="azure-iot-hub-bindings-for-azure-functions"></a>Azure IoT Hub-bindingen voor Azure Functions

In dit artikel wordt uitgelegd hoe u kunt werken met Azure Functions bindingen voor IoT Hub. De IoT Hub ondersteuning is gebaseerd op de [Azure Event hubs-binding](functions-bindings-event-hubs.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Pakketten - functies 1.x

Voor Azure Functions versie 1. x zijn de IoT Hub-bindingen in het [micro soft. Azure. webjobs. ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) NuGet-pakket, versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs) .

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Pakketten - functies 2.x

Voor de functies 2. x gebruikt u het pakket [micro soft. Azure. webjobs. Extensions. Event hubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) , versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

> [!IMPORTANT]
> Hoewel de volgende code voorbeelden de Event hub API gebruiken, is de opgegeven syntaxis van toepassing op IoT Hub-functies.

[!INCLUDE [functions-bindings-event-hubs](../../includes/functions-bindings-event-hubs.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
