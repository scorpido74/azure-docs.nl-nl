---
title: Azure Queue storage trigger en bindingen voor Azure Functions overzicht
description: Meer informatie over het gebruik van de trigger- en uitvoerbinding van Azure Queue-opslag in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 71321fa631bafde5b1dbaac2d165cb68fd8116d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277308"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Queue storage trigger en bindingen voor Azure Functions overzicht

Azure-functies kunnen worden uitgevoerd als nieuwe Azure Queue-opslagberichten worden gemaakt en wachtrijberichten binnen een functie kunnen schrijven.

| Actie | Type |
|---------|---------|
| Een functie uitvoeren als gegevens over wachtrijopslaggegevens worden gewijzigd | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Opslagberichten voor wachtrijen schrijven |[Uitvoerbinding](./functions-bindings-storage-queue-output.md) |

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
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functies 1.x-apps hebben automatisch een verwijzing naar het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren als gegevens over wachtrijopslaggegevens worden gewijzigd (Trigger)](./functions-bindings-storage-queue-trigger.md)
- [Opslagberichten voor wachtrijen schrijven (uitvoerbinding)](./functions-bindings-storage-queue-output.md)
