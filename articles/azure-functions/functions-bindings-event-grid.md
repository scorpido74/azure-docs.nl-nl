---
title: Azure Event Grid-bindingen voor Azure-functies
description: Meer informatie over het afhandelen van gebeurtenisrastergebeurtenissen in Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 21654a3b325e8b8f0a3e49ee64b7624c8540d0d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461076"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid-bindingen voor Azure-functies

In deze verwijzing wordt uitgelegd hoe [u gebeurtenisrastergebeurtenissen](../event-grid/overview.md) in Azure-functies verwerken. Zie [Gebeurtenissen ontvangen naar een HTTP-eindpunt voor](../event-grid/receive-events.md)meer informatie over het verwerken van gebeurtenisrasterberichten in een HTTP-eindpunt.

Event Grid is een Azure-service die HTTP-aanvragen verzendt om u op de hoogte te stellen van gebeurtenissen die plaatsvinden in *uitgevers.* Een uitgever is de service of bron die de gebeurtenis voortkomt. Een Azure blob-opslagaccount is bijvoorbeeld een uitgever en [een blob-upload of verwijdering is een gebeurtenis.](../storage/blobs/storage-blob-event-overview.md) Sommige [Azure-services hebben ingebouwde ondersteuning voor het publiceren van gebeurtenissen in gebeurtenisraster.](../event-grid/overview.md#event-sources)

*Gebeurtenishandlers* ontvangen en verwerken gebeurtenissen. Azure Functions is een van de vele [Azure-services met ingebouwde ondersteuning voor het afhandelen van gebeurtenisrastergebeurtenissen.](../event-grid/overview.md#event-handlers) In deze verwijzing leert u een gebeurtenisrastertrigger te gebruiken om een functie aan te roepen wanneer een gebeurtenis wordt ontvangen van gebeurtenisraster en om de uitvoerbinding te gebruiken om gebeurtenissen naar een [aangepast gebeurtenisrasteronderwerp](../event-grid/post-to-custom-topic.md)te verzenden.

Als u dat liever hebt, u een HTTP-trigger gebruiken om gebeurtenisrastergebeurtenissen te verwerken. zie [Gebeurtenissen ontvangen naar een HTTP-eindpunt](../event-grid/receive-events.md). Momenteel u geen trigger voor gebeurtenisraster gebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt geleverd in het [CloudEvents-schema](../event-grid/cloudevents-schema.md#azure-functions). Gebruik in plaats daarvan een HTTP-trigger.

| Actie | Type |
|---------|---------|
| Een functie uitvoeren wanneer een gebeurtenisgebeurtenis voor een gebeurtenis wordt verzonden | [Trigger](./functions-bindings-event-grid-trigger.md) |
| Hiermee verzendt u een gebeurtenis gebeurtenis gebeurtenis Gebeurtenisraster |[Uitvoerbinding](./functions-bindings-event-grid-output.md) |

De code in deze verwijzing wordt standaard gebruikt op syntaxis .NET Core, gebruikt in functies versie 2.x en hoger. Zie de [1.x-functiesjablonen](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates)voor informatie over de syntaxis van 1.x.

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
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functies 1.x-apps hebben automatisch een verwijzing naar het [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet-pakket, versie 2.x.

## <a name="next-steps"></a>Volgende stappen
* [Een functie uitvoeren wanneer een gebeurtenisgebeurtenis voor een gebeurtenis wordt verzonden](./functions-bindings-event-grid-trigger.md)
* [Een gebeurtenis Event Grid verzenden](./functions-bindings-event-grid-trigger.md)
