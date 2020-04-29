---
title: Docker-pull voor de Sleuteltermextractie container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Sleuteltermextractie container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: db5d0cfecde32d3dd6afe181d67a6ce6c0826eda
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877000"
---
#### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>Docker-pull voor de Sleuteltermextractie container

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om een container installatie kopie te downloaden van micro soft container Registry.

Zie de [Sleuteltermextractie](https://go.microsoft.com/fwlink/?linkid=2018757) -container op de docker-hub voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.

```
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```
