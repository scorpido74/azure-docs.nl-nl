---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878239"
---
```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

De volgende eigenschappen zijn beschikbaar in `extensionBundle`:

| Eigenschap | Beschrijving |
| -------- | ----------- |
| id | De naam ruimte voor de uitbreidings bundels van Microsoft Azure-functies. |
| versie | De versie van de bundel die moet worden geïnstalleerd. De functions-runtime kiest altijd de Maxi maal toegestane versie die is gedefinieerd door het versie bereik of-interval. Met de bovenstaande versie waarde kunnen alle bundel versies van 1.0.0 tot maar niet inclusief 2.0.0. Zie de [interval notatie voor het opgeven van versie reeksen](/nuget/reference/package-versioning#version-ranges)voor meer informatie. |

Bundel versies worden als pakketten in de bundel wijziging verhoogd. De belangrijkste versie wijzigingen treden op wanneer pakketten in de bundel door een primaire versie worden verhoogd. Belang rijke versie wijzigingen in de bundel vallen doorgaans samen met een wijziging in de primaire versie van de functions-runtime.  

De huidige set uitbrei dingen die zijn geïnstalleerd door de standaard bundel, wordt opgesomd in dit [bestand extensies. json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
