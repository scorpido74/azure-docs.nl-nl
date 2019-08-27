---
title: Docker-pull voor de Taaldetectie container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Taaldetectie container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: da6f04f1042d1a02178f345c5fe67387ae0a7d0f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051285"
---
## <a name="pull-the-language-detection-container"></a>De Taaldetectie-container ophalen

Container installatie kopieën voor Text Analytics zijn beschikbaar op de micro soft-Container Registry.

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|-----------|------------|
| Taaldetectie | `mcr.microsoft.com/azure-cognitive-services/language` |

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [taaldetectie](https://go.microsoft.com/fwlink/?linkid=2018759) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.


### <a name="docker-pull-for-the-language-detection-container"></a>Docker-pull voor de Taaldetectie container

```
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```
