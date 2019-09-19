---
title: Containervereisten en aanbevelingen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/18/2019
ms.author: dapine
ms.openlocfilehash: 47c889a428ec2916dc8cad73e30ef5ff630372a7
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71129804"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens.

#### <a name="readtabread"></a>[Lezen](#tab/read)

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Lees container beschreven.

| Container | Minimum | Aanbevolen |TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
| Lezen | 1 kernen, 8 GB geheugen, 0,24 TPS | 8 kernen, 16 GB geheugen, 1,17 TPS | 0,24, 1,17 |

#### <a name="recognize-texttabrecognize-text"></a>[Tekst herkennen](#tab/recognize-text)

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Tekst herkennen container beschreven.

| Container | Minimum | Aanbevolen |TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
| Tekst herkennen | 1 Core, 8 GB geheugen, 0,12 TPS | 8 kernen, 16 GB geheugen, 0,60 TPS | 0,12, 0,60 |

***

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van `docker run` de opdracht.
