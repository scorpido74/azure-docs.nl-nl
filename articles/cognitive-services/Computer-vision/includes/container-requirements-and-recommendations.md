---
title: Containervereisten en aanbevelingen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c62044582cb488a5ef2d20b3f407c0865b3994ba
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877968"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief die 29 regels en een totaal van 803 tekens bevat.

In de volgende tabel worden de minimale en aanbevolen toewijzing van resources voor elke leescontainer beschreven.

| Container | Minimum | Aanbevolen |Tps<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
| Lezen | 1 cores, 8 GB geheugen, 0,24 TPS | 8 cores, 16 GB geheugen, 1,17 TPS | 0.24, 1.17 |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS - transacties per seconde.

Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.
