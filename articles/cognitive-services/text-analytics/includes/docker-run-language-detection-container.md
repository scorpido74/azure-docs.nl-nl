---
title: Containervoorbeeld van de opdracht Docker run uitvoeren
titleSuffix: Azure Cognitive Services
description: Opdracht Docker-run voor taaldetectiecontainer
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1f6bfbf3a15f17c3cfd0487cdd0bbb535ffc31ae
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876992"
---
Als u de container *Taaldetectie* wilt uitvoeren, voert u de volgende `docker run` opdracht uit.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Een *taaldetectiecontainer* uit de containerafbeelding worden uitgevoerd
* Hiermee wordt één CPU-kern en 4 gigabyte (GB) geheugen toegewezen
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer.