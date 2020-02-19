---
title: Trigger-en bindingen voor Azure Blob Storage voor Azure Functions
description: Meer informatie over het gebruik van de Azure Blob Storage-trigger en bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462306"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob-opslag bindingen voor Azure Functions-overzicht

Azure Functions integreert met [Azure Storage](https://docs.microsoft.com/azure/storage/) via [Triggers en bindingen](./functions-triggers-bindings.md). Door te integreren met Blob Storage kunt u functies bouwen die reageren op wijzigingen in BLOB-gegevens en lees-en schrijf waarden.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren als wijzigingen in Blob Storage-gegevens | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Blob Storage-gegevens in een functie lezen | [Invoer binding](./functions-bindings-storage-blob-input.md) |
| Een functie toestaan Blob Storage-gegevens te schrijven |[Uitvoer binding](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functies 2. x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#Script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen voor gebruik met Visual Studio code. |
| C#Script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer Blob Storage-gegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob Storage-gegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
- [Blob Storage-gegevens van een functie schrijven](./functions-bindings-storage-blob-output.md)
