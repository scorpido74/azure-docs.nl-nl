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
ms.openlocfilehash: a3db0e2ffdd4a75f02634ca2227c3c41416d4f65
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588373"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-pull voor de Sentimentanalyse v3-container

De V3-container van de sentiment analyse container is beschikbaar in verschillende talen. Als u de container voor de Engelse container wilt downloaden, gebruikt u de onderstaande opdracht. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en
```

Als u de container voor een andere taal wilt downloaden, vervangt u door `en` een van de onderstaande taal codes. 

| Text Analytics-container | Taalcode |
|--|--|
| Engels | `en` |
| Spaans | `es` |
| Frans | `fr` |
| Italiaans | `it` |
| Duits | `de` |
| Chinees-vereenvoudigd | `zh` |
| Chinees-traditioneel | `zht` |
| Japans | `ja` |
| Portugees | `pt` |
| Nederlands | `nl` |

Zie [docker hub](https://go.microsoft.com/fwlink/?linkid=2018654)voor een volledige beschrijving van de beschik bare labels voor de Text Analytics containers.