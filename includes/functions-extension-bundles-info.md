---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78201939"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Tijdens het opstarten downloadt en installeert de host de [uitbrei ding voor opslag bindingen](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) en andere micro soft-bindings extensies. Deze installatie treedt op omdat bindings uitbreidingen standaard zijn ingeschakeld in het bestand *host. json* met de volgende eigenschappen:
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> Als u fouten ondervindt met betrekking tot bindings uitbreidingen, controleert u of de bovenstaande eigenschappen aanwezig zijn in de *host. json*.
::: zone-end  