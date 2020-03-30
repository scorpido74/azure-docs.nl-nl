---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 47e9e37676a4afa9ec29393bf970c368f3f9e5be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586122"
---
## <a name="add-to-your-functions-app"></a>Toevoegen aan de app Functies

### <a name="functions-2x-and-higher"></a>Functies 2.x en hoger

Werken met de trigger en bindingen vereist dat u verwijzen naar de juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassebibliotheken, terwijl de extensiebundel wordt gebruikt voor alle andere toepassingstypen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren , versie 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | De [uitbreidingsbundel registreren]          | De [Azure Tools-extensie] wordt aanbevolen om te gebruiken met Visual Studio Code. |
| C# Script (alleen online in Azure-portal)         | Een binding toevoegen                            | Zie [Uw extensies bijwerken]als u bestaande bindende extensies wilt bijwerken zonder dat u uw functie-app opnieuw hoeft te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[extensiebundel]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ../articles/azure-functions/install-update-binding-extensions-manual.md
[Azure Tools-extensie]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functies 1.x-apps hebben automatisch een verwijzing naar het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x.