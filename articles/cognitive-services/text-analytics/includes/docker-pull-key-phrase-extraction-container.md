---
title: Docker-pull voor de Sleuteltermextractie container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Sleuteltermextractie container
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: e71db5455a3d6726cd6567d568a13fcdcf9cb520
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051304"
---
## <a name="pull-the-key-phrase-extraction-container"></a>De Sleuteltermextractie-container ophalen

Container installatie kopieën voor Text Analytics zijn beschikbaar op de micro soft-Container Registry.

| Container | Container Registry/opslagplaats/naam van installatie kopie |
|-----------|------------|
| Sleuteltermextractie | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [Sleuteltermextractie](https://go.microsoft.com/fwlink/?linkid=2018757) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-pull voor de Sleuteltermextractie container

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
