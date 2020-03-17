---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: f47a543143c949715fe2a49adccf074759a346fa
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79381840"
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
| id | De naamruimte voor extensiebundels van Microsoft Azure Functions. |
| versie | De versie van de bundel die moet worden geïnstalleerd. De Functions-runtime kiest altijd de maximaal toegestane versie die door het versiebereik of -interval is gedefinieerd. Met de bovenstaande versiewaarde zijn alle bundelversies van 1.0.0 tot 2.0.0 toegestaan. Zie de [intervalnotatie voor het opgeven van versiebereiken](/nuget/reference/package-versioning#version-ranges) voor meer informatie. |

Bundelversies worden stapsgewijs verhoogd naarmate de pakketten in de bundel worden gewijzigd. Er vinden wijzigingen aan de primaire plaats wanneer pakketten in de bundel als gevolg van een primaire versie worden verhoogd. Wijzigingen aan de primaire versie in de bundel vallen doorgaans samen met een wijziging in de primaire versie van de Functions-runtime.  

De huidige set extensies die door de standaard bundel zijn geïnstalleerd, staat vermeld in dit [extensions.json-bestand](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json).
