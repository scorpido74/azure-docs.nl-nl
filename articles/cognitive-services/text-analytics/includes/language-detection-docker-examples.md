---
title: Voor beelden van Taaldetectie container docker
titleSuffix: Azure Cognitive Services
description: Voor beelden van Taaldetectie container docker
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 01863bfd1bf1c2c6f5688145ac5cbf74d1d2dfc1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80878655"
---
### <a name="language-detection-container-docker-examples"></a>Voor beelden van Taaldetectie container docker

De volgende docker-voor beelden zijn voor de Taaldetectie-container.

#### <a name="basic-example"></a>Basis voorbeeld 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>Voor beeld van logboek registratie 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
