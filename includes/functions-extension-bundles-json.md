---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f64892193eb6cfcce8f948b54e5557b5fa3d90ab
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
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

De volgende eigenschappen `extensionBundle`zijn beschikbaar in :

| Eigenschap | Beschrijving |
| -------- | ----------- |
| id | De naamruimte voor microsoft Azure Functions-extensiebundels. |
| versie | De versie van de bundel te installeren. De runtime Functies kiest altijd de maximaal toegestane versie die wordt gedefinieerd door het versiebereik of -interval. De bovenstaande versiewaarde maakt alle bundelversies mogelijk van 1.0.0 tot maar niet inclusief 2.0.0. Zie de [intervalnotatie voor het opgeven van versiebereiken voor](/nuget/reference/package-versioning#version-ranges)meer informatie. |

Bundelversies worden verhoogd als pakketten in de bundel veranderen. Belangrijke versiewijzigingen treden op wanneer pakketten in de bundel worden verhoogd met een belangrijke versie. Belangrijke versiewijzigingen in de bundel vallen meestal samen met een wijziging in de hoofdversie van de runtime functies.  

De huidige set extensies die door de standaardbundel zijn geïnstalleerd, wordt opgesomd in dit [bestand extensions.json](https://github.com/Azure/azure-functions-extension-bundles/blob/dev/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
