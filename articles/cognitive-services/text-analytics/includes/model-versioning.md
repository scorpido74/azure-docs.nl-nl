---
title: Model versie beheer
titleSuffix: Azure Cognitive Services
description: Model versies opgeven in de V3-eind punten
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088992"
---
Met versie 3 van de Text Analytics-API kunt u de model versie kiezen die het meest actueel is voor uw gegevens. Gebruik de optionele para meter `model-version` om de versie van het model te selecteren die voor uw aanvragen gewenst is. Als deze para meter niet is opgegeven, wordt de API standaard `latest`, de laatste stabiele versie. Hoewel u de nieuwste versie van het model kunt gebruiken in elke aanvraag, worden slechts enkele functies bijgewerkt in elke versie. In de volgende tabel wordt beschreven welke onderdelen zijn bijgewerkt in elke model versie:

| Model versie           | Bijgewerkte onderdelen         | Nieuwste versie voor:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Herkenning van entiteiten                      | Herkenning van entiteiten                      |
| `2019-10-01`            | Entiteits herkenning, sentiment analyse  | Taal detectie, extractie van sleutel zinnen, sentiment analyse|


Elke reactie van de V3-eind punten bevat een `model-version` veld waarin de gebruikte model versie wordt opgegeven.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Bekijk [wat er nieuw is](../whats-new.md) voor meer informatie over de updates voor deze model versies.
