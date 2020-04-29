---
title: Container vereisten en aanbevelingen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877968"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens.

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Lees container beschreven.

| Container | Minimum | Aanbevolen |TPS<br>(Mini maal, Maxi maal)|
|-----------|---------|-------------|--|
| Lezen | 1 kernen, 8 GB geheugen, 0,24 TPS | 8 kernen, 16 GB geheugen, 1,17 TPS | 0,24, 1,17 |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van `docker run` de opdracht.
