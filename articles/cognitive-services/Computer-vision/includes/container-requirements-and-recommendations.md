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
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397182"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens.

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Lees container beschreven.

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Lees 2,0-Preview | 1 kern geheugen van 8 GB |  8 kernen, 16 GB geheugen |
| Lees 3,0-Preview | 8 kernen, 16 GB geheugen | 8 kernen, 24 GB geheugen |
| Lees 3,1-Preview | 8 kernen, 16 GB geheugen | 8 kernen, 24 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.
