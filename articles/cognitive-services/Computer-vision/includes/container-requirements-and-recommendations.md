---
title: Containervereisten en aanbevelingen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481762"
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
