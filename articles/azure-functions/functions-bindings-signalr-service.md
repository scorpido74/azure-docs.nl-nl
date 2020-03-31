---
title: Azure Functions SignalR Service-bindingen
description: Meer informatie over het gebruik van SignalR-servicebindingen met Azure-functies.
author: craigshoemaker
ms.topic: reference
ms.date: 02/28/2019
ms.author: cshoe
ms.openlocfilehash: 863620ce6f0af33b05ef290ae95ccdc99a53a54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523033"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-servicebindingen voor Azure Functions

In deze set artikelen wordt uitgelegd hoe u realtime berichten verifiëren en verzenden naar clients die zijn verbonden met [Azure SignalR Service](https://azure.microsoft.com/services/signalr-service/) met behulp van SignalR Service-bindingen in Azure-functies. Azure Functions ondersteunt invoer- en uitvoerbindingen voor SignalR Service.

| Actie | Type |
|---------|---------|
| De URL en het access-token van de service retourneren | [Invoerbinding](./functions-bindings-signalr-service-input.md) |
| Berichten van De SignalR-service verzenden |[Uitvoerbinding](./functions-bindings-signalr-service-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan de app Functies

### <a name="functions-2x-and-higher"></a>Functies 2.x en hoger

Werken met de trigger en bindingen vereist dat u verwijzen naar de juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassebibliotheken, terwijl de extensiebundel wordt gebruikt voor alle andere toepassingstypen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren , versie 3.x | |
| C# Script, Java, JavaScript, Python, PowerShell | De [uitbreidingsbundel registreren]          | De [Azure Tools-extensie] wordt aanbevolen om te gebruiken met Visual Studio Code. |
| C# Script (alleen online in Azure-portal)         | Een binding toevoegen                            | Zie [Uw extensies bijwerken]als u bestaande bindende extensies wilt bijwerken zonder dat u uw functie-app opnieuw hoeft te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService
[core tools]: ./functions-run-local.md
[extensiebundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./install-update-binding-extensions-manual.md
[Azure Tools-extensie]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

Raadpleeg de ontwikkeling en configuratie van Azure Functions met [Azure SignalR Service](../azure-signalr/signalr-concept-serverless-development-config.md)voor meer informatie over het samen configureren en gebruiken van SignalR-service en Azure-functies.

### <a name="annotations-library-java-only"></a>Annotatiesbibliotheek (alleen Java)

Als u de annotaties van de SignalR-service in Java-functies wilt gebruiken, moet u een afhankelijkheid toevoegen aan het artefact *azure-functions-java-library-signalr* (versie 1.0 of hoger) aan uw *pom.xml-bestand.*

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-signalr</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="next-steps"></a>Volgende stappen

- [De URL en het toegangstoken voor serviceeindpunt (invoerbinding) retourneren](./functions-bindings-signalr-service-input.md)
- [Berichten van SignalR-service verzenden (uitvoerbinding)](./functions-bindings-signalr-service-output.md) 
