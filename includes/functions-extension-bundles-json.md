---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: 4ce048cafa4c20e3bbdbd8ecf1669748531ee122
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129062"
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
| version | De versie van de bundel die moet worden geïnstalleerd. De functions-runtime kiest altijd de Maxi maal toegestane versie die is gedefinieerd door het versie bereik of-interval. Met de bovenstaande versie waarde kunnen alle bundel versies van 1.0.0 tot maar niet inclusief 2.0.0. Zie de [interval notatie voor het opgeven van versie reeksen](/nuget/reference/package-versioning#version-ranges-and-wildcards)voor meer informatie. |

Bundel versies worden als pakketten in de bundel wijziging verhoogd. De belangrijkste versie wijzigingen treden op wanneer pakketten in de bundel door een primaire versie worden verhoogd. Belang rijke versie wijzigingen in de bundel vallen doorgaans samen met een wijziging in de primaire versie van de functions-runtime.  

De huidige set uitbrei dingen die zijn geïnstalleerd door de standaard bundel, wordt opgesomd in dit [bestand extensies. json](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
