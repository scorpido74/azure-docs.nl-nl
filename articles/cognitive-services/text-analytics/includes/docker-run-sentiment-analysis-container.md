---
title: Container voorbeeld van de opdracht docker run uitvoeren
titleSuffix: Azure Cognitive Services
description: Opdracht docker run voor Sentimentanalyse container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: 253c391b222c757a367bd5cd39939052cc697b00
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588376"
---
Voer de volgende opdracht uit om de *sentimentanalyse v3* -container uit te voeren `docker run` .

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Voert een *sentimentanalyse* container uit vanuit de container installatie kopie
* Wijst een CPU-kern en 8 GB aan geheugen toe
* Beschrijft TCP-poort 5000 en wijst een pseudo-TTY toe voor de container
* Verwijdert de container automatisch nadat deze is afgesloten. De container installatie kopie is nog steeds beschikbaar op de hostcomputer.