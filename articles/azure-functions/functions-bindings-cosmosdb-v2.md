---
title: Azure Cosmos DB-bindingen voor functies 2.x
description: Meer informatie over het gebruik van Azure Cosmos DB-triggers en bindingen in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2017
ms.author: cshoe
ms.openlocfilehash: f258a7aff52796a53540706bc8413575d63c9e7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605769"
---
# <a name="azure-cosmos-db-trigger-and-bindings-for-azure-functions-2x-overview"></a>Azure Cosmos DB trigger en bindingen voor Azure Functions 2.x overzicht

> [!div class="op_single_selector" title1="Selecteer de versie van de runtime van Azure-functies die u gebruikt: "]
> * [Versie 1](functions-bindings-cosmosdb.md)
> * [Versie 2](functions-bindings-cosmosdb-v2.md)

In deze set artikelen wordt uitgelegd hoe u werken met [Azure Cosmos DB-bindingen](../cosmos-db/serverless-computing-database.md) in Azure Functions 2.x. Azure Functions ondersteunt trigger-, invoer- en uitvoerbindingen voor Azure Cosmos DB.

| Actie | Type |
|---------|---------|
| Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd | [Trigger](./functions-bindings-cosmosdb-v2-trigger.md) |
| Een Azure Cosmos DB-document lezen | [Invoerbinding](./functions-bindings-cosmosdb-v2-input.md) |
| Wijzigingen opslaan in een Azure Cosmos DB-document  |[Uitvoerbinding](./functions-bindings-cosmosdb-v2-output.md) |

> [!NOTE]
> Deze verwijzing is voor [Azure Functions versie 2.x](functions-versions.md).  Zie [Azure Cosmos DB-bindingen voor Azure Functions 1.x voor](functions-bindings-cosmosdb.md)informatie over het gebruik van deze bindingen in Functies 1.x.
>
> Deze binding heette oorspronkelijk DocumentDB. In Functies versie 2.x worden de trigger, bindingen en het pakket allemaal Cosmos DB genoemd.

## <a name="supported-apis"></a>Ondersteunde API's

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="add-to-your-functions-app"></a>Toevoegen aan de app Functies

### <a name="functions-2x-and-higher"></a>Functies 2.x en hoger

Werken met de trigger en bindingen vereist dat u verwijzen naar de juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassebibliotheken, terwijl de extensiebundel wordt gebruikt voor alle andere toepassingstypen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren , versie 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | De [uitbreidingsbundel registreren]          | De [Azure Tools-extensie] wordt aanbevolen om te gebruiken met Visual Studio Code. |
| C# Script (alleen online in Azure-portal)         | Een binding toevoegen                            | Zie [Uw extensies bijwerken]als u bestaande bindende extensies wilt bijwerken zonder dat u uw functie-app opnieuw hoeft te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB
[core tools]: ./functions-run-local.md
[extensiebundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools-extensie]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functies 1.x-apps hebben automatisch een verwijzing naar het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Azure Cosmos DB-document wordt gemaakt of gewijzigd (Trigger)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Een Azure Cosmos DB-document lezen (invoerbinding)](./functions-bindings-cosmosdb-v2-input.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoerbinding)](./functions-bindings-cosmosdb-v2-output.md)
