---
title: Trigger en bindingen voor Azure-opslag in Azure-opslag voor Azure-opslag
description: Meer informatie over het gebruik van de Azure Blob-opslagtrigger en -bindingen in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277230"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Overzicht azure Blob-opslagbindingen voor Azure-functies

Azure Functions integreert met [Azure Storage](https://docs.microsoft.com/azure/storage/) via triggers [en bindingen.](./functions-triggers-bindings.md) Met de integratie met Blob-opslag u functies bouwen die reageren op wijzigingen in blobgegevens en lees- en schrijfwaarden.

| Actie | Type |
|---------|---------|
| Een functie uitvoeren als blob-opslaggegevens worden gewijzigd | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Blob-opslaggegevens in een functie lezen | [Invoerbinding](./functions-bindings-storage-blob-input.md) |
| Een functie toestaan blobopslaggegevens te schrijven |[Uitvoerbinding](./functions-bindings-storage-blob-output.md) |

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

- [Een functie uitvoeren wanneer blobopslaggegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob-opslaggegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
- [Blob-opslaggegevens schrijven vanuit een functie](./functions-bindings-storage-blob-output.md)
