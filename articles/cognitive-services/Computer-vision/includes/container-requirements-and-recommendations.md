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
ms.openlocfilehash: bffdb7b33fc7da38b6985edc3948b848f417a497
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982088"
---
> [!NOTE]
> De vereisten en aanbevelingen zijn gebaseerd op benchmarks met één aanvraag per seconde, met behulp van een afbeelding van 8 MB van een gescande zakelijke brief met 29 regels en een totaal van 803 tekens.

In de volgende tabel wordt de minimale en aanbevolen toewijzing van resources voor elke Lees container beschreven.

| Container | Minimum | Aanbevolen |
|-----------|---------|-------------|
| Lees 3,0-Preview | 8 kernen, 16 GB geheugen | 8 kernen, 24 GB geheugen |
| Lees 3,1-Preview | 8 kernen, 16 GB geheugen | 8 kernen, 24 GB geheugen |

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van de `docker run` opdracht.
