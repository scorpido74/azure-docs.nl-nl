---
title: Containervoorbeeld van de opdracht Docker run uitvoeren
titleSuffix: Azure Cognitive Services
description: Opdracht Docker-run voor container Sleutelzinextractie
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: b8c3bdfc05e855139b595cd0ba7bd723cdeaee45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966784"
---
Als u de container *Trefwoordextractie* `docker run` wilt uitvoeren, voert u de volgende opdracht uit.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Een *key phrase extraction-container* uit de containerafbeelding
* Hiermee wordt één CPU-kern en 4 gigabyte (GB) geheugen toegewezen
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.