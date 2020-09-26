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
ms.openlocfilehash: b28049f1854494b61d63824334b986d814a641cd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91309304"
---
Vul het [aanvraag formulier voor de Cognitive Services containers](https://aka.ms/csgate) in en verzend het om toegang tot de container aan te vragen.
Het formulier vraagt informatie over u, uw bedrijf en het gebruikers scenario waarvoor u de container gaat gebruiken. Nadat u het formulier hebt verzonden, wordt het door het Azure Cognitive Services-team beoordeeld om te controleren of u voldoet aan de criteria voor toegang tot het persoonlijke container register.

> [!IMPORTANT]
> * Op het formulier moet u een e-mail adres gebruiken dat is gekoppeld aan een Azure-abonnements-ID.
> * De Azure-resource die u gebruikt om de container uit te voeren, moet zijn gemaakt met de goedgekeurde Azure-abonnements-ID. 
> * Controleer uw e-mail adres (zowel postvak in-map als ongewenste e-mail) voor updates over de status van uw toepassing van micro soft.

Gebruik de opdracht docker login met de referenties die u in uw onboarding-e-mail hebt gekregen om verbinding te maken met het persoonlijke container register voor Cognitive Services containers.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Gebruik de [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) opdracht om deze container installatie kopie te downloaden uit het persoonlijke container register.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
