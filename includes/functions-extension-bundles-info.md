---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 1fc37c6f93fba34944caa7a91c2a89ce5dcdc398
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201939"
---
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
> [!TIP]
> Tijdens het opstarten downloadt en installeert de host de [extensie Storage binding](../articles/azure-functions/functions-bindings-storage-queue.md#functions-2x-and-higher) en andere Microsoft binding extensies. Deze installatie gebeurt omdat bindende extensies standaard zijn ingeschakeld in het *host.json-bestand* met de volgende eigenschappen:
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
> Als u fouten tegenkomt met betrekking tot bindende extensies, controleert u of de bovenstaande eigenschappen aanwezig zijn in *host.json*.
::: zone-end  