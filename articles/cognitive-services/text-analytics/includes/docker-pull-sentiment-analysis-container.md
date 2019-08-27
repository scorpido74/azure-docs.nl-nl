---
title: Docker-pull voor de Sentimentanalyse container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Sentimentanalyse container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 0954ef88f6917159156fbb73103b7b7af3283c00
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051413"
---
## <a name="pull-the-sentiment-analysis-container"></a>De Sentimentanalyse-container ophalen

Container installatie kopieën voor Text Analytics zijn beschikbaar op de micro soft-Container Registry.

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|-----------|------------|
| Sentimentanalyse | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [sentimentanalyse](https://go.microsoft.com/fwlink/?linkid=2018654) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

### <a name="docker-pull-for-the-sentiment-analysis-container"></a>Docker-pull voor de Sentimentanalyse container

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```
