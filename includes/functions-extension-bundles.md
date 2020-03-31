---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/27/2019
ms.author: glenga
ms.openlocfilehash: d697334fe56fb9133a06cee79067c60bc3a37281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68639121"
---
De eenvoudigste manier om bindende extensies te installeren is om [uitbreidingsbundels](../articles/azure-functions/functions-bindings-register.md#extension-bundles)in te schakelen. Wanneer u bundels inschakelt, wordt automatisch een vooraf gedefinieerde set uitbreidingspakketten geïnstalleerd.

Als u extensiebundels wilt inschakelen, opent u het bestand host.json en werkt u de inhoud ervan bij aan de volgende code:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```