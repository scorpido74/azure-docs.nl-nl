---
title: Container vereisten en aanbevelingen.
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 8/26/2019
ms.author: dapine
ms.openlocfilehash: 5da088bf0356dd54d98ec5393fd2db8068f9c666
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034476"
---
De volgende tabel beschrijft de minimale en aanbevolen CPU-kernen en het geheugen die moeten worden toegewezen voor elke Tekst herkennen-container.

| Container | Minimum | Aanbevolen |TPS<br>(Minimum, Maximum)|
|-----------|---------|-------------|--|
|Tekst herkennen|1 Core, 8 GB geheugen, 0,5 TPS|2 kernen, 8 GB geheugen, 1 TPS|0,5, 1|

* Elke kern moet ten minste 2,6 gigahertz (GHz) of sneller zijn.
* TPS-trans acties per seconde

Core en geheugen komen overeen met `--cpus` de `--memory` instellingen en, die worden gebruikt als onderdeel van `docker run` de opdracht.