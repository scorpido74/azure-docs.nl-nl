---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/14/2019
ms.author: glenga
ms.openlocfilehash: b67e2bf2ae5af2feb334e898ce69fd5b959c7cf0
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689546"
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