---
title: Azure Service Bus-bindingen voor Azure-functies
description: Meer informatie over het verzenden van Azure Service Bus-triggers en bindingen in Azure-functies.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.openlocfilehash: 44e4114b328701d5de363e91f5562f1daad351c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277412"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus-bindingen voor Azure-functies

Azure Functions integreert met [Azure Service Bus](https://azure.microsoft.com/services/service-bus) via triggers en [bindingen.](./functions-triggers-bindings.md) Met de integratie met Service Bus u functies bouwen die reageren op wachtrij- of onderwerpberichten en deze verzenden.

| Actie | Type |
|---------|---------|
| Een functie uitvoeren wanneer een wachtrij of onderwerpbericht van een servicebus wordt gemaakt | [Trigger](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus-berichten verzenden |[Uitvoerbinding](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan de app Functies

### <a name="functions-2x-and-higher"></a>Functies 2.x en hoger

Werken met de trigger en bindingen vereist dat u verwijzen naar de juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassebibliotheken, terwijl de extensiebundel wordt gebruikt voor alle andere toepassingstypen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren , versie 4.x | |
| C# Script, Java, JavaScript, Python, PowerShell | De [uitbreidingsbundel registreren]          | De [Azure Tools-extensie] wordt aanbevolen om te gebruiken met Visual Studio Code. |
| C# Script (alleen online in Azure-portal)         | Een binding toevoegen                            | Zie [Uw extensies bijwerken]als u bestaande bindende extensies wilt bijwerken zonder dat u uw functie-app opnieuw hoeft te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[extensiebundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools-extensie]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functies 1.x-apps hebben automatisch een verwijzing naar het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een wachtrij of onderwerpbericht van een servicebus wordt gemaakt (Trigger)](./functions-bindings-service-bus-trigger.md)
- [Azure Service Bus-berichten verzenden vanuit Azure-functies (uitvoerbinding)](./functions-bindings-service-bus-output.md)
