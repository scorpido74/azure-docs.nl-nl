---
title: Azure-functies HTTP-triggers en bindingen
description: Leer http-triggers en bindingen gebruiken in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 29b5e9c7673b4a730a41bf7cf2b1c4a2a86209ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462102"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Overzicht van Azure Functions HTTP-triggers en bindingen

Azure-functies kunnen worden aangeroepen via HTTP-verzoeken om serverloze API's te bouwen en te reageren op [webhooks.](https://en.wikipedia.org/wiki/Webhook)

| Actie | Type |
|---------|---------|
| Een functie uitvoeren vanuit een HTTP-aanvraag | [Trigger](./functions-bindings-http-webhook-trigger.md) |
| Een HTTP-antwoord van een functie retourneren |[Uitvoerbinding](./functions-bindings-http-webhook-output.md) |

De code in dit artikel wordt standaard gebruikt in versie 2.x en hoger. Zie de [1.x-functiesjablonen](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)voor informatie over de syntaxis van 1.x.

## <a name="add-to-your-functions-app"></a>Toevoegen aan de app Functies

### <a name="functions-2x-and-higher"></a>Functies 2.x en hoger

Werken met de trigger en bindingen vereist dat u verwijzen naar de juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassebibliotheken, terwijl de extensiebundel wordt gebruikt voor alle andere toepassingstypen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren , versie 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | De [uitbreidingsbundel registreren]          | De [Azure Tools-extensie](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen om te gebruiken met Visual Studio Code. |
| C# Script (alleen online in Azure-portal)         | Een binding toevoegen                            | Zie [Uw extensies bijwerken]als u bestaande bindende extensies wilt bijwerken zonder dat u uw functie-app opnieuw hoeft te publiceren. |

[core tools]: ./functions-run-local.md
[extensiebundel]: ./functions-bindings-register.md#extension-bundles
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functies 1.x-apps hebben automatisch een verwijzing naar het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren vanuit een HTTP-aanvraag](./functions-bindings-http-webhook-trigger.md)
- [Een HTTP-antwoord van een functie retourneren](./functions-bindings-http-webhook-output.md)
