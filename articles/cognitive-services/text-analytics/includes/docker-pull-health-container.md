---
title: Docker-pull voor de status container
titleSuffix: Azure Cognitive Services
description: Docker-pull-opdracht voor Text Analytics voor Status container
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108915"
---
Vul het [aanvraag formulier voor de Cognitive Services containers](https://aka.ms/cognitivegate) in en verzend het om toegang tot de container aan te vragen.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

Gebruik de opdracht docker login met de referenties die u in uw onboarding-e-mail hebt gekregen om verbinding te maken met het persoonlijke container register voor Cognitive Services containers.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om deze container installatie kopie te downloaden uit het persoonlijke container register.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
