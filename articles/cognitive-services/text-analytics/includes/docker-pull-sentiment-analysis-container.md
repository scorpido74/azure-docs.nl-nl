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
ms.openlocfilehash: 549ecf940c53267db6b3caa576c4c24db414337a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906086"
---
#### <a name="docker-pull-for-the-sentiment-analysis-v3-container"></a>Docker-pull voor de Sentimentanalyse v3-container

De V3-container van de sentiment analyse container is beschikbaar in verschillende talen. Als u de container voor de Engelse container wilt downloaden, gebruikt u de onderstaande opdracht. 

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:3.0-en
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