---
title: Modelversiebeheer
titleSuffix: Azure Cognitive Services
description: Modelversies opgeven in de V3-eindpunten
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77088992"
---
Met versie 3 van de Text Analytics API u de modelversie kiezen die het meest actueel is voor uw gegevens. Gebruik de `model-version` optionele parameter om de gewenste versie van het model te selecteren voor uw aanvragen. Als deze parameter niet is opgegeven, `latest`wordt de API standaard gebruikt voor de nieuwste stabiele versie. Hoewel u de nieuwste modelversie in elk verzoek gebruiken, worden in elke versie slechts enkele functies bijgewerkt. In de onderstaande tabel wordt beschreven welke functies in elke modelversie zijn bijgewerkt:

| Modelversie           | Functies bijgewerkt         | Nieuwste versie voor:           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | Herkenning van entiteiten                      | Herkenning van entiteiten                      |
| `2019-10-01`            | Entiteitsherkenning, Sentimentanalyse  | Taaldetectie, Sleutelzinextractie, Sentimentanalyse|


Elk antwoord van de v3-eindpunten bevat een `model-version` veld waarin de modelversie wordt opgegeven die is gebruikt.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
Zie [Nieuw](../whats-new.md) voor meer informatie over de updates voor deze modelversies.
