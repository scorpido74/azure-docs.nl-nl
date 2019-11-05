---
title: Container vereisten en aanbevelingen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/20/2019
ms.author: dapine
ms.openlocfilehash: 2fa97a3a18741f506e10e5e4d61da286db3f744c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73481762"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens.

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Lees container beschreven.

| Container | Minimum | Aanbevolen |TPS<br>(Mini maal, Maxi maal)|
|-----------|---------|-------------|--|
| Lezen | 1 kernen, 8 GB geheugen, 0,24 TPS | 8 kernen, 16 GB geheugen, 1,17 TPS | 0,24, 1,17 |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde.

Core en geheugen komen overeen met de instellingen `--cpus` en `--memory` die worden gebruikt als onderdeel van de `docker run` opdracht.
