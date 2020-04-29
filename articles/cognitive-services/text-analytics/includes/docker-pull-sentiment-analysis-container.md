---
title: Docker-pull voor de Sentimentanalyse container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Sentimentanalyse container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e502b886c8031174180d40f1c5b7e373a974ee8e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876994"
---
#### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-pull voor de Sentimentanalyse container

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [sentimentanalyse](https://go.microsoft.com/fwlink/?linkid=2018654) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
