---
title: Containervoorbeeld van de opdracht Docker run uitvoeren
titleSuffix: Azure Cognitive Services
description: Opdracht Docker-run voor taaldetectiecontainer
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/12/2019
ms.author: dapine
ms.openlocfilehash: d2818336de2b3d4d810932cefb21edd95b8cf733
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70966731"
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